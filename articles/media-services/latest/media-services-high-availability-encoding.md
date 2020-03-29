---
title: Azure Media Services hög tillgänglighetskodning
description: Lär dig hur du redundans till ett sekundärt Media Services-konto om ett regionalt datacenter avbrott eller fel inträffar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934202"
---
# <a name="media-services-high-availability-encoding"></a>Kodning av hög tillgänglighet för mediatjänster 

Azure Media Services-kodningstjänst är en regional batchbearbetningsplattform och är för närvarande inte utformad för hög tillgänglighet inom en enda region. Kodningstjänsten tillhandahåller för närvarande inte omedelbar redundans av tjänsten om det finns ett regionalt datacenteravbrott eller fel på underliggande komponent eller beroende tjänster (till exempel lagring, SQL). I den här artikeln beskrivs hur du distribuerar Media Services för att upprätthålla en arkitektur med hög tillgänglighet med redundans och säkerställa optimal tillgänglighet för dina program.

Genom att följa de riktlinjer och bästa praxis som beskrivs i artikeln, kommer du att minska risken för kodning fel, förseningar och minimera återställningstiden om ett avbrott inträffar i en enda region.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Hur man bygger ett tvärregionalt kodningssystem

* [Skapa](create-account-cli-how-to.md) två (eller flera) Azure Media Services-konton.

    De två kontona måste finnas i olika regioner. Mer information finns [i regioner där Azure Media Services-tjänsten distribueras](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Ladda upp dina media till samma region som du planerar att skicka jobbet från. Mer information om hur du startar kodning finns i [Skapa en jobbinmatning från en HTTPS-URL](job-input-from-http-how-to.md) eller [Skapa en jobbinmatning från en lokal fil](job-input-from-local-file-how-to.md).

    Om du sedan behöver skicka [om jobbet](transforms-jobs-concept.md) till en annan region kan du använda JobInputHttp eller använda [Kopia-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) för att kopiera data från källtillgångsbehållaren till en tillgångsbehållare i den alternativa regionen.
* Prenumerera på JobStateChange-meddelanden i varje konto via Azure Event Grid. Mer information finns i:

    * [Audio Analytics exempel](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) som visar hur du övervakar ett jobb med Azure Event Grid inklusive att lägga till en återgång om Azure Event Grid-meddelanden försenas av någon anledning.
    * [Azure Event Grid-scheman för Media Services-händelser](media-services-event-schemas.md)
    * [Registrera dig för händelser via Azure-portalen eller CLI](reacting-to-media-services-events.md) (du kan också göra det med EventGrid Management SDK)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (som stöder Media Services-händelser internt).

    Du kan också använda Event Grid-händelser via Azure Functions.
* När du skapar ett [jobb:](transforms-jobs-concept.md)

    * Välj slumpmässigt ett konto i listan över konton som används för närvarande (den här listan innehåller normalt båda kontona, men om problem upptäcks kan det bara innehålla ett konto). Om listan är tom höjer du en avisering så att en operatör kan undersöka.
    * Allmän vägledning är att du behöver en [mediereserverad enhet](media-reserved-units-cli-how-to.md) per [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (såvida du inte använder [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) där 3 mediereserverade enheter per JobOutput rekommenderas).
    * Hämta antalet mediereserverade enheter (MRUs) för det valda kontot. Om antalet aktuella **mediereserverade enheter** inte redan har det högsta värdet lägger du till numret på de MR:er som krävs av jobbet och uppdaterar tjänsten. Om din jobbinlämningshastighet är hög och du ofta frågar mrus för att hitta dig är som högst, använd en distribuerad cache för värdet med en rimlig timeout.
    * Räkna antalet jobb ombord.

* När jobstateChange-hanteraren får ett meddelande om att ett jobb har nått det schemalagda tillståndet registrerar du den tid då den anger schematillståndet och den region/konto som används.
* När jobstateChange-hanteraren får ett meddelande om att ett jobb har nått bearbetningstillståndet markerar du posten för jobbet som bearbetning.
* När JobStateChange-hanteraren får ett meddelande om att ett jobb har nått tillståndet Färdig/Fel/Avbruten markerar du posten för jobbet som slutlig och minskning av antalet inflight-jobb. Hämta antalet mediereserverade enheter för det valda kontot och jämför det aktuella MRU-numret mot antalet inflight-jobb. Om antalet ombord är mindre än antalet mru-program minskar du det och uppdaterar tjänsten.
* Har en separat process som regelbundet tittar på dina register över jobben
    
    * Om du har jobb i det schemalagda tillståndet som inte har avancerat till bearbetningstillståndet på en rimlig tid för en viss region tar du bort den regionen från listan över konton som används för närvarande.  Beroende på dina affärsbehov kan du välja att avbryta dessa jobb direkt och skicka dem till den andra regionen. Eller så kan du ge dem lite mer tid att flytta till nästa stat.
    * Beroende på antalet mediereserverade enheter som konfigurerats på kontot och inlämningsfrekvensen kan det också finnas jobb i kötillstånd som systemet inte har hämtat för bearbetning ännu.  Om listan över jobb i det köade tillståndet växer utöver en acceptabel gräns i en region kan dessa jobb avbrytas och skickas till den andra regionen.  Detta kan dock vara ett symptom på att inte ha tillräckligt med mediereserverade enheter konfigurerade på kontot för den aktuella belastningen.  Du kan begära en högre kvot för mediereserverade enheter via Azure Support om det behövs.
    * Om en region har tagits bort från kontolistan övervakar du den för återställning innan du lägger tillbaka den i listan.  Den regionala hälso- och sjukvården kan övervakas via befintliga jobb i regionen (om de inte har annullerats och skickas in på nytt), genom att lägga till kontot i listan igen efter en viss tid och av operatörer som övervakar Azure-kommunikation om avbrott som kan påverka Azure Media Services.
    
Om du hittar mru-antalet är stryk upp och ner en hel del, flytta decrement logik till den periodiska uppgiften. Låt logiken för skicka förjobb jämföra inflight-antalet med det aktuella MRU-antalet för att se om det behöver uppdatera MRUs.

## <a name="next-steps"></a>Nästa steg

* [Skapa direktuppspelning av flera regioner på begäran](media-services-high-availability-streaming.md)
* Kolla in [kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
