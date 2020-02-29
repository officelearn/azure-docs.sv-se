---
title: Azure Media Services hög tillgänglighet
description: Lär dig hur du växlar till ett sekundärt Media Services konto om det uppstår ett avbrott eller fel i ett regionalt Data Center.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202291"
---
# <a name="azure-media-services-high-availability-guidance"></a>Vägledning för Azure Media Services hög tillgänglighet

Azure Media Services Encoding service är en regional bearbetnings plattform för batch och är för närvarande inte avsedd för hög tillgänglighet inom en enda region. Encoding-tjänsten tillhandahåller för närvarande inte omedelbar redundansväxling av tjänsten om det uppstår ett system för data avbrott i regionala Data Center eller fel i underliggande komponenter eller beroende tjänster (till exempel lagring, SQL osv.)  Den här artikeln förklarar hur du distribuerar Media Services för att upprätthålla en arkitektur med hög tillgänglighet med redundans och säkerställa optimal tillgänglighet för dina program. 

Genom att följa rikt linjerna och metod tipsen som beskrivs i artikeln kan du minska risken för kodnings fel, fördröjningar och minimera återställnings tiden om ett avbrott inträffar i en enda region.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Så här skapar du ett regionalt kodnings system

* [Skapa](create-account-cli-how-to.md) två (eller fler) Azure Media Services-konton.
* Prenumerera på **JobStateChange** -meddelanden i varje konto.

    * I Media Services v3 görs det via Azure Event Grid. Mer information finns i:
    
       * [Event Grid exempel](../../event-grid/receive-events.md), 
       * [Azure Event Grid scheman för Media Services händelser](media-services-event-schemas.md), 
       * [Registrera dig för händelser via Azure Portal eller CLI](reacting-to-media-services-events.md) (du kan också göra det med EVENTGRID Management SDK)
       * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (som stöder Media Services-händelser internt). 
       
        Du kan också använda Event Grid händelser via Azure Functions.
    * I Media Services v2 görs detta via [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* När du [skapar ett jobb](transforms-jobs-concept.md):

    * Välj slumpmässigt ett konto i listan över konton som används för närvarande (den här listan innehåller vanligt vis båda kontona, men om problem upptäcks kan det bara innehålla ett konto). Om listan är tom kan du generera en avisering så att en operatör kan undersöka den.
    * Allmän vägledning är att du behöver en [reserverad enhet](media-reserved-units-cli-how-to.md) per uppgift eller [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (om du inte använder [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) i v3).
    * Hämta antalet [medie reserverade enheter](media-reserved-units-cli-how-to.md) (MRUs) för det valda kontot. Om det aktuella **medie reserverade enhets** antalet inte redan är vid det högsta värdet, lägger du till antalet MRUs som krävs av jobbet och uppdaterar tjänsten. Om din jobb sändnings frekvens är hög och du ofta skickar frågor till MRUs för att upptäcka att du har maximalt, använder du ett distribuerat cacheminne för värdet med en rimlig tids gräns.
    * Behåll antalet synlighetssekvensnummer-jobb.
* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått det schemalagda läget, registrerar du den tid det anger schemats tillstånd och regionen/kontot som används.    
* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått bearbetnings statusen markerar du posten för jobbet som bearbetning.
* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått statusen slutfört/fel/avbruten, markerar du posten för jobbet som slutgiltig och minskar antalet synlighetssekvensnummer-jobb. Hämta antalet enheter som är reserverade för det valda kontot och jämför det aktuella MRU-numret mot antalet synlighetssekvensnummer-jobb. Om antalet synlighetssekvensnummer är mindre än det senast använda värdet, kan du minska det och uppdatera tjänsten.
* Ha en separat process som regelbundet tittar på dina poster i jobben. Om du har jobb med schemalagt tillstånd som inte har avancerat till bearbetnings tillstånd under en bestämd tid för en specifik region, tar du bort den regionen från listan över konton som används för närvarande.

    * Beroende på dina affärs behov kan du välja att avbryta dessa jobb omedelbart och skicka dem igen till det andra kontot. Eller så kan du ge dem mer tid att gå vidare till nästa steg.   
    * Efter en viss tids period lägger du tillbaka kontot till den lista som används (med antagandet att regionen har återställts).
    
Om du hittar MRU-antalet är nedskräpning upp och ned en hel del, flytta den minsknings bara logiken till den periodiska aktiviteten. Se till att det finns för hands krav för att skicka synlighetssekvensnummer till det aktuella MRU-antalet för att se om det behövs för att uppdatera MRUs.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Så här skapar du strömning på begäran mellan flera regioner 

* Video-on-demand-strömning mellan regioner innebär att duplicera [till gångar](assets-concept.md), [innehålls nyckel principer](content-key-policy-concept.md) (om de används), [strömmande principer](streaming-policy-concept.md)och [strömmande positionerare](streaming-locators-concept.md). 
* Du måste skapa principerna i båda regionerna och se till att de är uppdaterade. 
* När du skapar strömmande positionerare ska du använda samma Locator ID-värde, ContentKey-ID-värde och ContentKey-värde.  
* Om du kodar innehållet, uppmanas du att koda innehållet i region A och publicera det och sedan kopiera det kodade innehållet till region B och publicera det med samma värden som i region A.
* Du kan använda Traffic Manager på värd namnen för ursprunget och nyckel leverans tjänsten (i Media Services konfiguration ser detta ut som en anpassad nyckel server-URL).

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto](create-account-cli-how-to.md)
* Kolla av [kod exempel](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
