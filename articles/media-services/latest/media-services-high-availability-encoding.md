---
title: Kodning för Azure Media Services hög tillgänglighet
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78934202"
---
# <a name="media-services-high-availability-encoding"></a>Kodning för Media Services hög tillgänglighet 

Azure Media Services Encoding service är en regional bearbetnings plattform för batch och är för närvarande inte avsedd för hög tillgänglighet inom en enda region. Kodnings tjänsten tillhandahåller för närvarande inte omedelbar redundansväxling av tjänsten om det uppstår ett avbrott i regionala Data Center eller fel i en underliggande komponent eller beroende tjänster (till exempel lagring, SQL). Den här artikeln förklarar hur du distribuerar Media Services för att upprätthålla en arkitektur med hög tillgänglighet med redundans och säkerställa optimal tillgänglighet för dina program.

Genom att följa rikt linjerna och metod tipsen som beskrivs i artikeln kan du minska risken för kodnings fel, fördröjningar och minimera återställnings tiden om ett avbrott inträffar i en enda region.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Så här skapar du ett regionalt kodnings system

* [Skapa](create-account-cli-how-to.md) två (eller fler) Azure Media Services-konton.

    De två kontona måste finnas i olika regioner. Mer information finns [i regioner där Azure Media Servicess tjänsten har distribuerats](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Ladda upp mediet till samma region som du planerar att skicka jobbet från. Mer information om hur du startar kodning finns i [skapa ett jobb indata från en HTTPS-URL](job-input-from-http-how-to.md) eller [skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).

    Om du sedan behöver skicka [jobbet](transforms-jobs-concept.md) igen till en annan region kan du använda JobInputHttp eller använda [copy-BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) för att kopiera data från käll till gångs behållaren till en till gångs behållare i den alternativa regionen.
* Prenumerera på JobStateChange-meddelanden i varje konto via Azure Event Grid. Mer information finns i:

    * [Ljud analys exempel](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) som visar hur du övervakar ett jobb med Azure Event Grid inklusive att lägga till en återställning om Azure Event Grid meddelanden fördröjs av någon anledning.
    * [Azure Event Grid scheman för Media Services händelser](media-services-event-schemas.md)
    * [Registrera dig för händelser via Azure Portal eller CLI](reacting-to-media-services-events.md) (du kan också göra det med EVENTGRID Management SDK)
    * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (som stöder Media Services-händelser internt).

    Du kan också använda Event Grid händelser via Azure Functions.
* När du skapar ett [jobb](transforms-jobs-concept.md):

    * Välj slumpmässigt ett konto i listan över konton som används för närvarande (den här listan innehåller vanligt vis båda kontona, men om problem upptäcks kan det bara innehålla ett konto). Om listan är tom kan du generera en avisering så att en operatör kan undersöka den.
    * Allmän vägledning är att du behöver en [reserverad enhet](media-reserved-units-cli-how-to.md) per [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (om du inte använder [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) där 3 reserverade enheter per JobOutput rekommenderas).
    * Hämta antalet medie reserverade enheter (MRUs) för det valda kontot. Om det aktuella **medie reserverade enhets** antalet inte redan är vid det högsta värdet, lägger du till antalet MRUs som krävs av jobbet och uppdaterar tjänsten. Om din jobb sändnings frekvens är hög och du ofta skickar frågor till MRUs för att upptäcka att du har maximalt, använder du ett distribuerat cacheminne för värdet med en rimlig tids gräns.
    * Behåll antalet synlighetssekvensnummer-jobb.

* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått det schemalagda läget, registrerar du den tid det anger schemats tillstånd och regionen/kontot som används.
* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått bearbetnings statusen markerar du posten för jobbet som bearbetning.
* När din JobStateChange-hanterare får ett meddelande om att ett jobb har nått statusen slutfört/fel/avbruten, markerar du posten för jobbet som slutgiltig och minskar antalet synlighetssekvensnummer-jobb. Hämta antalet enheter som är reserverade för det valda kontot och jämför det aktuella MRU-numret mot antalet synlighetssekvensnummer-jobb. Om antalet synlighetssekvensnummer är mindre än det senast använda värdet, kan du minska det och uppdatera tjänsten.
* Ha en separat process som regelbundet tittar på dina poster i jobben
    
    * Om du har jobb med schemalagt tillstånd som inte har avancerat till bearbetnings tillstånd under en bestämd tid för en specifik region, tar du bort den regionen från listan över konton som används för närvarande.  Beroende på dina affärs behov kan du välja att avbryta dessa jobb omedelbart och skicka dem igen till den andra regionen. Eller så kan du ge dem mer tid att gå vidare till nästa steg.
    * Beroende på hur många enheter som kon figurer ATS för kontot och sändnings takten kan det också finnas jobb i det köade läget. systemet har inte hämtats för bearbetning ännu.  Om listan över jobb i det köade läget växer över en acceptabel gräns i en region kan dessa jobb avbrytas och skickas till den andra regionen.  Detta kan dock vara ett symtom på att det inte finns tillräckligt med konfigurerade enhets enheter som kon figurer ATS i kontot för den aktuella belastningen.  Du kan begära en högre enhets kvot för en enhet med hjälp av Azure-supporten om det behövs.
    * Om en region har tagits bort från konto listan övervakar du den för återställning innan du lägger tillbaka den i listan.  Det regionala hälso tillståndet kan övervakas via de befintliga jobben i regionen (om de inte har avbrutits och skickats igen), genom att lägga till kontot i listan efter en viss tids period och av operatörer som övervakar Azure Communications om avbrott som kan påverka Azure Media Services.
    
Om du hittar MRU-antalet är nedskräpning upp och ned en hel del, flytta den minsknings bara logiken till den periodiska aktiviteten. Se till att det finns för hands krav för att skicka synlighetssekvensnummer till det aktuella MRU-antalet för att se om det behövs för att uppdatera MRUs.

## <a name="next-steps"></a>Nästa steg

* [Bygg strömning på begäran mellan flera regioner](media-services-high-availability-streaming.md)
* Kolla av [kod exempel](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
