---
title: Översikt över Azure Media Services-slutpunkt för direktuppspelning | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services slutpunkter för direktuppspelning.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: b40a2bbfa7bbab7ffbdd4c47684eda7c954e7b77
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996971"
---
# <a name="streaming-endpoints-overview"></a>Översikt över slutpunkter för direktuppspelning  

## <a name="overview"></a>Översikt

I Microsoft Azure Media Services (AMS), en **Strömningsslutpunkt** representerar en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN Content Delivery Network () för vidare distribution. Media Services tillhandahåller också en smidig integrering med Azure CDN. Den utgående dataströmmen från en StreamingEndpoint-tjänst kan vara en direktsänd dataström, en video på begäran eller progressiv nedladdning av din tillgång i Media Services-kontot. Varje Azure Media Services-konto innehåller en standard StreamingEndpoint. Du kan skapa ytterligare Strömningsslutpunkter under kontot. Det finns två versioner av Strömningsslutpunkter, 1.0 och 2.0. Från och med januari, 10 2017, eventuella nyligen skapade AMS-konton innehåller version 2.0 **standard** StreamingEndpoint. Ytterligare slutpunkter för direktuppspelning som du lägger till det här kontot kommer också att version 2.0. Den här ändringen påverkar inte befintliga konton; befintliga Strömningsslutpunkter blir version 1.0 och kan uppgraderas till version 2.0. Med den här ändringen finns beteende, fakturering och funktionen ändringar (Mer information finns i den **Streaming typer och versioner** avsnitt beskrivs nedan).

Azure Media Services har lagts till följande egenskaper till entiteten slutpunkt för direktuppspelning: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Detaljerad översikt över de här egenskaperna finns i [detta](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

När du skapar ett Azure Media Services-konto en standard som standard-slutpunkt för direktuppspelning skapas åt dig i den **stoppad** tillstånd. Du kan inte ta bort standardslutpunkten för direktuppspelning. Beroende på tillgänglighet för Azure CDN i målregionen, som standard som du skapade nyss standard slutpunkt för direktuppspelning innehåller också ”StandardVerizon” CDN provider-integrering. 
                
> [!NOTE]
> Azure CDN-integreringen kan inaktiveras innan du startar slutpunkten för direktuppspelning. Den `hostname` och strömnings-URL är densamma, oavsett om du aktiverar CDN.

Det här avsnittet ger en översikt över de viktigaste funktioner som tillhandahålls av slutpunkter för direktuppspelning.

## <a name="naming-conventions"></a>Namngivningskonventioner

För standardslutpunkt: `{AccountName}.streaming.mediaservices.windows.net`

För alla ytterligare slutpunkter: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Strömmande typer och versioner

### <a name="standardpremium-types-version-20"></a>Standard/Premium-typer (version 2.0)

Från och med januari 2017-versionen av Media Services kan ha du två strömmande typer: **Standard** och **Premium**. Typerna är en del av strömning endpoint version ”2.0”.

Type|Beskrivning
---|---
**Standard**|Det här är standardalternativet som skulle fungera för flesta scenarier.<br/>Med det här alternativet kan du få fast/begränsad SLA, första 15 dagarna efter att du startar slutpunkten för direktuppspelning är kostnadsfri.<br/>Om du skapar flera slutpunkter för direktuppspelning, endast den första som är kostnadsfri under de första 15 dagarna, faktureras de andra när du startar. <br/>Observera att kostnadsfria utvärderingsversionen bara gäller för nya media services-konton och standardslutpunkt för direktuppspelning. Befintliga slutpunkter för direktuppspelning och dessutom skapade slutpunkter för direktuppspelning inte innehåller kostnadsfria utvärderingsperioden även de uppgraderas till version 2.0 eller de skapas som version 2.0.
**Premium**|Det här alternativet är lämpligt för professionella scenarier som kräver högre skala eller kontroll.<br/>Variabeln serviceavtal som baseras på strömmande enhet (SU) premiumkapacitet köpt, dedikerad slutpunkter för direktuppspelning live i isolerad miljö och konkurrerar inte om resurser.

Mer information finns i den **jämför Streaming typer** följande avsnitt.

### <a name="classic-type-version-10"></a>Klassisk typ (version 1.0)

För användare som skapade AMS-konton innan januari, 10 2017-versionen kan du ha en **klassiska** typ av en slutpunkt för direktuppspelning. Den här typen är en del av den strömmande slutpunkt versionen ”1.0”.

Om din **version ”1.0”** slutpunkt för direktuppspelning har > = 1 premium strömningsenheter (SU) meddelandet kommer slutpunkten för direktuppspelning av premium och ger alla AMS-funktioner (precis som den **Standard/Premium** typ) utan ytterligare konfigurationssteg.

>[!NOTE]
>**Klassiska** slutpunkter för direktuppspelning (version ”1.0” och 0 SU), tillhandahåller funktioner för begränsad och innehåller inte en SLA. Vi rekommenderar att migrera till **Standard** typ att få en bättre upplevelse och använda funktioner som dynamisk paketering och kryptering och andra funktioner som finns i den **Standard** typen. Att migrera till den **Standard** typ, gå till den [Azure-portalen](https://portal.azure.com/) och välj **valbar till Standard**. Mer information om migrering finns i den [migrering](#migration-between-types) avsnittet.
>
>Tänk på att den här åtgärden inte kan återställas och påverkas priset.
>
 
## <a name="comparing-streaming-types"></a>Jämföra strömmande typer

### <a name="versions"></a>Versioner

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Fakturering|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Klassisk|1.0|0|Ej tillämpligt|Kostnadsfri|Ej tillämpligt|
|Standard-slutpunkt för direktuppspelning|2.0|0|Ja|Avgiftsbelagt|Ja|
|Premium-enheter för direktuppspelning|1.0|>0|Ja|Avgiftsbelagt|Ja|
|Premium-enheter för direktuppspelning|2.0|>0|Ja|Avgiftsbelagt|Ja|

### <a name="features"></a>Funktioner

Funktion|Standard|Premium
---|---|---
Kostnadsfria första 15 dagarna| Ja |Nej
Dataflöde |Upp till 600 Mbit/s när Azure CDN inte används. Skalning med CDN.|200 Mbit/s per enhet (SU) för strömning. Skalning med CDN.
SLA | 99.9|99,9 (200 Mbit/s per SU).
CDN|Azure CDN från tredje part CDN eller inga CDN.|Azure CDN från tredje part CDN eller inga CDN.
Fakturering beräknas| Dagligen|Dagligen
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Automatiskt skalar upp det riktade dataflödet.|Ytterligare enheter för strömning
IP-filtrering/G20/anpassat värd|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderade användningen |Rekommenderas för merparten av scenarier för direktuppspelning.|Professionella användning.<br/>Om du tror att kan du ha behov utöver Standard. Kontakta oss (amsstreaming@microsoft.com) om du tror att en samtidiga målgrupp storlek är större än 50 000 användare.


## <a name="migration-between-types"></a>Migrering mellan typer

Från | Till | Åtgärd
---|---|---
Klassisk|Standard|Behovet av att delta i
Klassisk|Premium| Skala (ytterligare enheter för strömning)
Standard/Premium|Klassisk|Inte tillgänglig (om den strömmande slutpunkten versionen är 1.0. Det är tillåtet att ändra till klassiskt med att scaleunits ”0”)
Standard (med eller utan ett CDN)|Premium med samma konfigurationer|Tillåten i den **igång** tillstånd. (via Azure portal)
Premium (med eller utan ett CDN)|Standard med samma konfigurationer|Tillåten i den **igång** tillstånd (via Azure portal)
Standard (med eller utan ett CDN)|Premium med olika config|Tillåten i den **stoppats** tillstånd (via Azure portal). Inte tillåtet i körläge.
Premium (med eller utan ett CDN)|Standard med olika config|Tillåten i den **stoppats** tillstånd (via Azure portal). Inte tillåtet i körläge.
Version 1.0 med SU > = 1 med CDN|Standard/Premium med inga CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd.
Version 1.0 med SU > = 1 med CDN|Standard med eller utan ett CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd. Version 1.0 CDN kommer att tas bort och nya som skapas och startas.
Version 1.0 med SU > = 1 med CDN|Premium med eller utan ett CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd. Klassiska CDN kommer att tas bort och nya som skapas och startas.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

