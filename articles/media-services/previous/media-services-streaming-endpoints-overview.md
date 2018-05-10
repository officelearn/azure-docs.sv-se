---
title: Översikt över Azure Media Services-Strömningsslutpunkt | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services strömningsslutpunkter.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 7b4077747d4c9ec581496786063fc8fbe0c6b247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="streaming-endpoints-overview"></a>Översikt över strömmande slutpunkter 

## <a name="overview"></a>Översikt

I Microsoft Azure Media Services (AMS), en **Strömningsslutpunkt** representerar en strömmande tjänst som kan leverera innehåll direkt till ett klientprogram player eller till en innehåll innehållsleveransnätverk (CDN) för vidare distribution. Media Services tillhandahåller också sömlös integration av Azure CDN. Utgående dataströmmen från en StreamingEndpoint-tjänst kan vara en direktsänd dataström, en video på begäran eller progressiv hämtning av dina tillgångar i Media Services-kontot. Varje Azure Media Services-konto innehåller standard StreamingEndpoint. Du kan skapa ytterligare Strömningsslutpunkter under kontot. Det finns två versioner av Strömningsslutpunkter 1.0 och 2.0. Från och med januari 10 2017 alla konton som nyligen skapade AMS innehåller version 2.0 **standard** StreamingEndpoint. Ytterligare strömmande slutpunkter som du lägger till det här kontot kommer också att version 2.0. Den här ändringen påverkar inte befintliga konton; befintliga Strömningsslutpunkter blir version 1.0 och kan uppgraderas till version 2.0. Med den här ändringen kommer det ske beteende, fakturering och funktionen förändringar (Mer information finns i **strömning typer och versioner** avsnitt beskrivs nedan).

Dessutom från och med 2.15 version (som publicerades i januari 2017), Azure Media Services läggs följande egenskaper till entiteten Strömningsslutpunkt: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Detaljerad översikt över de här egenskaperna finns [detta](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

När du skapar ett Azure Media Services-konto ett standardvärde som standard strömmande slutpunkten skapas för dig i den **stoppad** tillstånd. Du kan inte ta bort standard strömmande slutpunkten. Beroende på Azure CDN-tillgänglighet i det aktuella området som standard nyskapad standard strömningsslutpunkt även ”StandardVerizon” CDN provider-integration. 

>[!NOTE]
>Azure CDN-integrering kan inaktiveras innan du startar den strömmande slutpunkten.

Det här avsnittet ger en översikt över de huvudsakliga funktionerna som tillhandahålls av strömningsslutpunkter.

## <a name="streaming-types-and-versions"></a>Strömning typer och versioner

### <a name="standardpremium-types-version-20"></a>Standard/Premium typer (version 2.0)

Från och med januari 2017 av Media Services, du har två typer av strömmande: **Standard** och **Premium**. Dessa typer är en del av Streaming endpoint version ”2.0”.

Typ|Beskrivning
---|---
**Standard**|Detta är standardalternativet som skulle fungera för flesta scenarier.<br/>Med det här alternativet får du fast/begränsad SERVICENIVÅAVTAL, första 15 dagar efter att du startar den strömmande slutpunkten är ledig.<br/>Om du skapar fler än en strömningsslutpunkter, endast den första är gratis för de första 15 dagarna, debiteras de andra när du startar. <br/>Observera att kostnadsfri utvärderingsversion gäller endast nyskapade media services-konton och standard strömmande slutpunkten. Befintliga strömningsslutpunkter och dessutom skapade strömningsslutpunkter inte innehåller utvärderingsperioden även de uppgraderas till version 2.0 eller de skapas som version 2.0.
**Premium**|Det här alternativet är lämpligt för professional scenarier som kräver högre skala eller kontroll.<br/>Variabeln SLA som är baserad på premium strömmande enhet (SU) kapacitet köpt dedikerade strömningsslutpunkter live i isolerad miljö och konkurrerar inte om resurser.

Mer information finns i **jämför strömning typer** efter avsnittet.

### <a name="classic-type-version-10"></a>Klassisk typ (version 1.0)

För användare som skapade AMS konton före januari 10 2017-versionen som du har en **klassiska** typ av en strömmande slutpunkt. Den här typen är en del av den strömmande slutpunkt versionen ”1.0”.

Om din **version ”1.0”** strömmande slutpunkten har > = 1 premium strömningsenheter (SU), den kommer att vara premium strömmande slutpunkt och ger alla AMS-funktioner (precis som de **Standard/Premium** typen) utan några ytterligare konfigurationssteg.

>[!NOTE]
>**Klassiska** strömningsslutpunkter (version ”1.0” och 0 SU), tillhandahåller begränsade funktioner och inte innehåller ett SERVICENIVÅAVTAL. Det rekommenderas att migrera till **Standard** typ att få en bättre upplevelse och använda funktioner som dynamisk paketering eller kryptering och andra funktioner som ingår i den **Standard** typen. Att migrera till den **Standard** typ, gå till den [Azure-portalen](https://portal.azure.com/) och välj **Opt-in-standarden**. Mer information om migrering finns i [migrering](#migration-between-types) avsnitt.
>
>Tänk på att den här åtgärden inte kan återställas och påverkar priset.
>
 
## <a name="comparing-streaming-types"></a>Jämföra strömmande typer

### <a name="versions"></a>Versioner

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Fakturering|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Klassisk|1.0|0|Ej tillämpligt|Kostnadsfri|Ej tillämpligt|
|Standard-slutpunkt för direktuppspelning|2.0|0|Ja|Avgiftsbelagt|Ja|
|Premium-enheter för direktuppspelning|1.0|>0|Ja|Avgiftsbelagt|Ja|
|Premium-enheter för direktuppspelning|2.0|>0|Ja|Avgiftsbelagt|Ja|

### <a name="features"></a>Funktioner

Funktion|Standard|Premium
---|---|---
Ledigt första 15 dagar| Ja |Nej
Dataflöde |Upp till 600 Mbps när Azure CDN inte används. Skalas med CDN.|200 Mbit/s per streaming unit (SU). Skalas med CDN.
SLA | 99.9|99,9 (200 Mbit/s per SU).
CDN|Azure CDN tredjeparts CDN eller inga CDN.|Azure CDN tredjeparts CDN eller inga CDN.
Fakturering är linjärt| Dagligen|Dagligen
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Automatisk skalar upp till det riktade dataflödet.|Ytterligare enheter för strömning
IP-filtrering/G20/Custom värd|Ja|Ja
Progressiv hämtning|Ja|Ja
Rekommenderade användning |Vi rekommenderar för det stora flertalet av strömning scenarier.|Professional användning.<br/>Om du tror att kan du ha behov utöver Standard. Kontakta oss (amsstreaming@microsoft.com) om du tror att en samtidiga målgruppen storlek är större än 50 000 användare.


## <a name="migration-between-types"></a>Migrering mellan typer

Från | Till | Åtgärd
---|---|---
Klassisk|Standard|Behovet av att delta i
Klassisk|Premium| Skala (ytterligare enheter för strömning)
Standard/Premium|Klassisk|Inte tillgänglig (om den strömmande slutpunkten version är 1.0. Det är tillåtet att ändra till klassiskt med att ange scaleunits ”0”)
Standard (med eller utan ett CDN)|Premium med samma konfiguration|Tillåten i den **igång** tillstånd. (via Azure portal)
Premium (med eller utan ett CDN)|Standard med samma konfiguration|Tillåten i den **igång** tillstånd (via Azure portal)
Standard (med eller utan ett CDN)|Premium med olika konfig|Tillåten i den **stoppats** tillstånd (via Azure portal). Inte körs.
Premium (med eller utan ett CDN)|Standard med olika konfig|Tillåten i den **stoppats** tillstånd (via Azure portal). Inte körs.
Version 1.0 med SU > = 1 CDN|Standard/Premium inga CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd.
Version 1.0 med SU > = 1 CDN|Standard med eller utan ett CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd. Version 1.0 CDN kommer att tas bort och nya som skapas och igång.
Version 1.0 med SU > = 1 CDN|Premium med eller utan ett CDN|Tillåten i den **stoppats** tillstånd. Tillåts inte i den **igång** tillstånd. Klassiska CDN kommer att tas bort och nya som skapas och igång.

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

