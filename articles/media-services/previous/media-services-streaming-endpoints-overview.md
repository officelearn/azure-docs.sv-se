---
title: Översikt över Direktuppspelning av Azure Media Services - Microsoft-dokument
description: Den här artikeln innehåller en översikt över Azure Media Services-slutpunkter för direktuppspelning av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885644"
---
# <a name="streaming-endpoints-overview"></a>Översikt över slutpunkter för direktuppspelning  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

I Microsoft Azure Media Services (AMS) representerar en slutpunkt för **direktuppspelning** en strömningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN (Content Delivery Network) för vidare distribution. Media Services tillhandahåller också sömlös Azure CDN-integrering. Utgående ström från en StreamingEndpoint-tjänst kan vara en livestream, en video på begäran eller progressiv nedladdning av din tillgång i ditt Media Services-konto. Varje Azure Media Services-konto innehåller en standardstreamingEndpoint. Ytterligare StreamingEndpoints kan skapas under kontot. Det finns två versioner av StreamingEndpoints, 1.0 och 2.0. Från och med den 10 januari 2017 kommer alla nyskapade **default** AMS-konton att innehålla version 2.0 standardstreamingEndpoint. Ytterligare slutpunkter för direktuppspelning som du lägger till i det här kontot kommer också att vara version 2.0. Den här ändringen kommer inte att påverka de befintliga kontona. befintliga StreamingEndpoints kommer att vara version 1.0 och kan uppgraderas till version 2.0. Med den här ändringen kommer det att finnas beteende, fakturering och funktionsändringar (mer information finns i avsnittet **Strömmande typer och versioner** som dokumenteras nedan).

Azure Media Services har lagt till följande egenskaper i slutpunktsentiteten För direktuppspelning: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Detaljerad översikt över dessa egenskaper finns i [den här](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

När du skapar ett Azure Media Services-konto skapas en standardstandardslutpunkt för direktuppspelning för dig i tillståndet **Stoppad.** Du kan inte ta bort standardslutpunkten för direktuppspelning. Beroende på Azure CDN-tillgängligheten i den inriktade regionen innehåller som standard nyligen skapade standardslutpunkt för direktuppspelning även "StandardVerizon" CDN-providerintegration. 
                
> [!NOTE]
> Azure CDN-integrering kan inaktiveras innan du startar slutpunkten för direktuppspelning. Och `hostname` direktuppspelnings-URL:en förblir densamma oavsett om du aktiverar CDN eller inte.

Det här avsnittet innehåller en översikt över de viktigaste funktionerna som tillhandahålls av slutpunkter för direktuppspelning.

## <a name="naming-conventions"></a>Namngivningskonventioner

För standardslutpunkten:`{AccountName}.streaming.mediaservices.windows.net`

För ytterligare slutpunkter:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Strömmande typer och versioner

### <a name="standardpremium-types-version-20"></a>Standard/Premium-typer (version 2.0)

Från och med lanseringen av Media Services i januari 2017 har du två direktuppspelningstyper: **Standard** (förhandsversion) och **Premium**. Dessa typer är en del av slutpunktsversionen "2.0".


|Typ|Beskrivning|
|--------|--------|  
|**Standard**|Standardslutpunkten för direktuppspelning är en **standardtyp,** kan ändras till Premium-typen genom att justera strömningsenheter.|
|**Premium** |Det här alternativet är lämpligt för professionella scenarier som kräver högre skala eller kontroll. Du flyttar till en **Premium-typ** genom att justera strömningsenheter.<br/>Dedikerade slutpunkter för direktuppspelning lever i en isolerad miljö och konkurrerar inte om resurser.|

För kunder som vill leverera innehåll till stora internetpublik rekommenderar vi att du aktiverar CDN på slutpunkten för direktuppspelning.

Mer detaljerad information finns i avsnittet [Jämför direktuppspelningstyper.](#comparing-streaming-types)

### <a name="classic-type-version-10"></a>Klassisk typ (version 1.0)

För användare som skapade AMS-konton före utgivningen den 10 januari 2017 har du en **klassisk** typ av en slutpunkt för direktuppspelning. Den här typen är en del av slutpunktsversionen "1.0".

Om din **version "1.0"** streaming slutpunkt har >= 1 premium streaming enheter (SU), kommer det att vara premium streaming slutpunkt och kommer att ge alla AMS-funktioner (precis som **Standard / Premium** typ) utan några ytterligare konfigurationssteg.

>[!NOTE]
>**Klassiska** slutpunkter för direktuppspelning (version "1.0" och 0 SU), innehåller begränsade funktioner och innehåller inte ett serviceavtal. Vi rekommenderar att du migrerar till **standardtyp** för att få en bättre upplevelse och för att använda funktioner som dynamisk förpackning eller kryptering och andra funktioner som medar **standardtypen.** Om du vill migrera till **standardtypen** går du till [Azure-portalen](https://portal.azure.com/) och väljer **Opt-in till Standard**. Mer information om migrering finns i [migreringsavsnittet.](#migration-between-types)
>
>Se upp för att den här åtgärden inte kan återställas och har en prispåverkan.
>
 
## <a name="comparing-streaming-types"></a>Jämföra direktuppspelningstyper

### <a name="versions"></a>Versioner

|Typ|StreamingEndpointVersion|SkalaEnheter|CDN|Fakturering|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassisk|1.0|0|Ej tillämpligt|Kostnadsfri|
|Slutpunkt för standardstreaming (förhandsgranskning)|2.0|0|Ja|Betalas|
|Premium streamingenheter|1.0|> 0|Ja|Betalas|
|Premium streamingenheter|2.0|> 0|Ja|Betalas|

### <a name="features"></a>Funktioner

Funktion|Standard|Premium
---|---|---
Dataflöde |Upp till 600 Mbit/s och kan ge ett mycket högre effektivt dataflöde när ett CDN används.|200 Mbit/s per streamingenhet (SU). Kan ge ett mycket högre effektivt dataflöde när ett CDN används.
CDN|Azure CDN, CDN från tredje part eller ingen CDN.|Azure CDN, CDN från tredje part eller ingen CDN.
Faktureringen är proportionell| Varje dag|Varje dag
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skalning|Auto skalas upp till det riktade dataflödet.|Ytterligare strömningsenheter.
IP-filtrering/G20/Anpassad värd <sup>1</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderad användning |Rekommenderas för de allra flesta streamingscenarier.|Professionell användning. 

<sup>1</sup> Används endast direkt på slutpunkten för direktuppspelning när CDN-programmet inte är aktiverat på slutpunkten.<br/>

SLA-information finns i [Prissättning och SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrering mellan typer

Från | Till | Åtgärd
---|---|---
Klassisk|Standard|Behovet av att anmäla dig
Klassisk|Premium| Skala(ytterligare strömningsenheter)
Standard/Premium|Klassisk|Inte tillgängligt(Om slutpunktsversionen för direktuppspelning är 1.0. Det är tillåtet att byta till klassisk med inställning scaleunits till "0")
Standard (med/utan CDN)|Premium med samma konfigurationer|Tillåtet i **started** starttillståndet. (via Azure-portalen)
Premium (med/utan CDN)|Standard med samma konfigurationer|Tillåtet i **starttillståndet** (via Azure-portalen)
Standard (med/utan CDN)|Premium med olika config|Tillåts i **stoppat** tillstånd (via Azure-portalen). Inte tillåtet i körtillstånd.
Premium (med/utan CDN)|Standard med olika config|Tillåts i **stoppat** tillstånd (via Azure-portalen). Inte tillåtet i körtillstånd.
Version 1.0 med SU >= 1 med CDN|Standard/Premium utan CDN|Tillåts i **stoppat** tillstånd. Inte tillåtet **started** i starttillståndet.
Version 1.0 med SU >= 1 med CDN|Standard med/utan CDN|Tillåts i **stoppat** tillstånd. Inte tillåtet **started** i starttillståndet. Version 1.0 CDN kommer att tas bort och ny skapas och startas.
Version 1.0 med SU >= 1 med CDN|Premium med/utan CDN|Tillåts i **stoppat** tillstånd. Inte tillåtet **started** i starttillståndet. Classic CDN kommer att tas bort och ny skapas och startas.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

