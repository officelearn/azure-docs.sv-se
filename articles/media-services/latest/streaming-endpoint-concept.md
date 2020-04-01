---
title: Slutpunkter för direktuppspelning (ursprung)
titleSuffix: Azure Media Services
description: Lär dig mer om Strömmande slutpunkter (Origin), en dynamisk paketerings- och streamingtjänst som levererar innehåll direkt till en klientspelarapp eller till ett CDN (Content Delivery Network).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: a8674714451f3cc686acb74d6c66ac6800b75936
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478077"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Strömmande slutpunkter (Origin) i Azure Media Services

I Microsoft Azure Media Services representerar en slutpunkt för [direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints) en dynamisk (just-in-time)-förpackning och ursprungstjänst som kan leverera ditt live- och on-demand-innehåll direkt till en klientspelarapp med hjälp av ett av de vanliga HLS- eller DASH-protokollen (streamingmedia). Dessutom ger **slutpunkten för direktuppspelning** dynamisk (just-in-time) kryptering till branschledande DRM-moduler. 

När du skapar ett Media Services-konto skapas en **standardslutpunkt** för direktuppspelning för dig i ett stoppat tillstånd. Du kan inte ta bort **standardslutpunkten** för direktuppspelning. Fler slutpunkter för direktuppspelning kan skapas under kontot (se [Kvoter och begränsningar](limits-quotas-constraints.md)).

> [!NOTE]
> För att börja strömma videor måste du starta den slutpunkt för **direktuppspelning** som du vill strömma videon från.
>
> Du faktureras bara när slutpunkten för direktuppspelning körs.

Se till att även granska avsnittet [Dynamisk förpackning.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Namnkonvention

Värdnamnsformatet för den `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`strömmande `servicename` URL:en är: , där = slutpunktsnamnet för direktuppspelning eller livehändelsenamnet.

När du använder standardslutpunkten `servicename` för direktuppspelning utelämnas `{accountname}-{regionname}.streaming.azure.net`så att webbadressen är: .

### <a name="limitations"></a>Begränsningar

* Slutpunktsnamnet för direktuppspelning har ett maxvärde på 24 tecken.
* Namnet ska följa detta [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) mönster: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typer

Det finns två **slutpunktstyper för direktuppspelning:** **Standard** (förhandsversion) och **Premium**. Typen definieras av antalet skalenheter (`scaleUnits`) som du allokerar för slutpunkten för direktuppspelning.

I tabellen beskrivs typerna:

|Typ|Skalningsenheter|Beskrivning|
|--------|--------|--------|  
|**Standard**|0|Standardslutpunkten för direktuppspelning är en **standardtyp** – den kan `scaleUnits`ändras till Premium-typen genom att justera .|
|**Premium**|> 0|**Premie** Slutpunkter för direktuppspelning är lämpliga för avancerade arbetsbelastningar och ger dedikerad och skalbar bandbreddskapacitet. Du flyttar till en **Premium-typ** genom att `scaleUnits` justera (strömningsenheter). `scaleUnits`förse dig med dedikerad utgående kapacitet som kan köpas i steg om 200 Mbps. När du använder **Premium-typen** ger varje aktiverad enhet ytterligare bandbreddskapacitet till appen. |

> [!NOTE]
> För kunder som vill leverera innehåll till stora internetpublik rekommenderar vi att du aktiverar CDN på slutpunkten för direktuppspelning.

SLA-information finns i [Prissättning och SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Jämföra direktuppspelningstyper

Funktion|Standard|Premium
---|---|---
Dataflöde |Upp till 600 Mbit/s och kan ge ett mycket högre effektivt dataflöde när ett CDN används.|200 Mbit/s per streamingenhet (SU). Kan ge ett mycket högre effektivt dataflöde när ett CDN används.
CDN|Azure CDN, CDN från tredje part eller ingen CDN.|Azure CDN, CDN från tredje part eller ingen CDN.
Faktureringen är proportionell| Varje dag|Varje dag
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Auto skalas upp till det riktade dataflödet.|Ytterligare SUs
IP-filtrering/G20/Anpassad värd <sup>1</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderad användning |Rekommenderas för de allra flesta streamingscenarier.|Professionell användning.

<sup>1</sup> Används endast direkt på slutpunkten för direktuppspelning när CDN inte är aktiverat på slutpunkten.<br/>

## <a name="streaming-endpoint-properties"></a>Egenskaper för direktuppspelning av slutpunkt

I det här avsnittet finns information om några av slutpunkten för direktuppspelning. Exempel på hur du skapar en ny slutpunkt för direktuppspelning och beskrivningar av alla egenskaper finns i [Slutpunkten för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Används för att konfigurera följande säkerhetsinställningar för den här slutpunkten för direktuppspelning: Akamai Signature Header Authentication keys och IP-adresser som tillåts ansluta till den här slutpunkten. Den här egenskapen `cdnEnabled` kan bara ställas in när den är inställd på false.

- `cdnEnabled`: Anger om Azure CDN-integreringen för den här slutpunkten för direktuppspelning är aktiverad (inaktiverad som standard). Om du `cdnEnabled` ställer in på true inaktiveras följande konfigurationer: `customHostNames` och `accessControl`.

    Alla datacenter stöder inte Azure CDN-integrering. Så här kontrollerar du om ditt datacenter har Azure CDN-integreringen tillgänglig:

  - Försök att `cdnEnabled` ställa in den till true.
  - Kontrollera det returnerade `HTTP Error Code 412` resultatet för ett (Förutsättningstyrt) med ett meddelande om "Egenskapen Streaming endpoint CdnEnabled can't be set to true as CDN-kapacitet är inte tillgänglig i den aktuella regionen."Check the returned result for an (Förutsättningarstyrt) med ett meddelande om "Egenskapen Streaming endpoint CdnEnabled can't be set to true as CDN-capability is not available in the current region".

    Om du får det här felet stöder inte datacentret det. Prova ett annat datacenter.

- `cdnProfile`: `cdnEnabled` När är inställt på true `cdnProfile` kan du också skicka värden. `cdnProfile`är namnet på CDN-profilen där CDN-slutpunktspunkten ska skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är `cdnEnabled` NULL och är sant används standardvärdet "AzureMediaStreamingPlatformCdnProfile". Om den `cdnProfile` angivna redan finns skapas en slutpunkt under den. Om profilen inte finns skapas en ny profil automatiskt.
- `cdnProvider`: När CDN är aktiverat kan `cdnProvider` du också skicka värden. `cdnProvider`kontroller som leverantören kommer att användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" och "StandardAkamai". Om inget värde `cdnEnabled` anges och är sant används "StandardVerizon" (det är standardvärdet).
- `crossSiteAccessPolicies`: Används för att ange principer för åtkomst mellan webbplatser för olika klienter. Mer information finns i [Filspecifikationen för principen mellan domäner](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [göra en tjänst tillgänglig över domängränser](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Inställningarna gäller endast för jämn direktuppspelning.
- `customHostNames`: Används för att konfigurera en slutpunkt för direktuppspelning för att acceptera trafik som dirigeras till ett anpassat värdnamn. Den här egenskapen gäller för standard- och premiumstreamingslutpunkter och kan ställas in när: `cdnEnabled`falskt.

    Äganderätten till domännamnet måste bekräftas av Media Services. Media Services verifierar domännamnsägarskapet genom att kräva en `CName` post som innehåller Media Services-konto-ID som en komponent som ska läggas till i den domän som används. För att "sports.contoso.com" ska användas som ett anpassat värdnamn för slutpunkten för `<accountId>.contoso.com` direktuppspelning måste en post för konfigureras så att den pekar på ett av Medie services-verifieringsvärdnamn. Verifieringsvärdnamnet består av verifierade. \<mediaservices-dns-zon>.

    Följande är de förväntade DNS-zoner som ska användas i verifiera posten för olika Azure-regioner.
  
  - Nordamerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Kina:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    En `CName` post som mappar "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" till "verifydns.media.azure.net" bevisar till exempel att Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad har äganderätten till contoso.com domänen, vilket gör att alla namn under contoso.com kan användas som ett anpassat värdnamn för en slutpunkt för direktuppspelning under det kontot. Om du vill hitta värdet för medietjänst-ID går du till [Azure-portalen](https://portal.azure.com/) och väljer ditt Media Service-konto. **Konto-ID:et** visas längst upp till höger på sidan.

    Om det finns ett försök att ange ett anpassat `CName` värdnamn utan en korrekt verifiering av posten misslyckas DNS-svaret och cachelagras sedan under en viss tid. När en korrekt post är på plats kan det ta ett tag tills det cachelagrade svaret har återställts. Beroende på DNS-providern för den anpassade domänen tar det allt från några minuter till en timme att förnya posten.

    Förutom `CName` den som `<accountId>.<parent domain>` mappar `verifydns.<mediaservices-dns-zone>`till måste `CName` du skapa en annan som `sports.contoso.com`mappar det anpassade värdnamnet (till exempel) till `amstest-usea.streaming.media.azure.net`din Media Services Streaming Endpoints värdnamn (till exempel ).

    > [!NOTE]
    > Strömmande slutpunkter som finns i samma datacenter kan inte dela samma anpassade värdnamn.

    För närvarande stöder Media Services inte TLS med anpassade domäner.

- `maxCacheAge`- Åsidosätter standard max-age HTTP-cache kontroll huvudet som anges av strömmande slutpunkt på mediefragment och on-demand manifest. Värdet anges på några sekunder.
- `resourceState` -

    - Stoppad: det ursprungliga tillståndet för en slutpunkt för direktuppspelning efter att ha skapats
    - Från och med: övergår till körtillståndet
    - Köra: kan strömma innehåll till klienter
    - Skalning: skalenheterna ökas eller minskas
    - Stoppa: övergår till det stoppade tillståndet
    - Ta bort: tas bort

- `scaleUnits`: Ge dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. Om du behöver flytta till en `scaleUnits` **Premium-typ** justerar du .

## <a name="why-use-multiple-streaming-endpoints"></a>Varför använda flera slutpunkter för direktuppspelning?

En enda slutpunkt för direktuppspelning kan strömma både live- och on-demand-videor och de flesta kunder använder bara en slutpunkt för direktuppspelning. Det här avsnittet innehåller några exempel på varför du kan behöva använda flera slutpunkter för direktuppspelning.

* Varje reserverad enhet tillåter 200 Mbit/s bandbredd. Om du behöver mer än 2 000 Mbit/s bandbredd kan du använda den andra slutpunkten för direktuppspelning och belastningsbalansen för att ge dig ytterligare bandbredd.

    CDN är dock det bästa sättet att uppnå skala ut för strömmande innehåll, men om du levererar så mycket innehåll att CDN drar mer än 2 Gbps kan du lägga till ytterligare slutpunkter för direktuppspelning (ursprung). I det här fallet måste du dela ut innehållsadresser som är balanserade över de två slutpunkterna för direktuppspelning. Den här metoden ger bättre cachelagring än att slumpmässigt försöka skicka förfrågningar till varje ursprung (till exempel via en trafikhanterare). 
    
    > [!TIP]
    > Vanligtvis om CDN drar mer än 2 Gbps då något kan vara felkonfigurerad (till exempel inget ursprung skärmning).
    
* Belastningsutjämning av olika CDN-providers. Du kan till exempel ställa in standardslutpunkten för direktuppspelning så att den använder Verizon CDN och skapar en andra för att använda Akamai. Lägg sedan till lite belastningsutjämning mellan de två för att uppnå multi-CDN-balansering. 

    Kunden gör dock ofta belastningsutjämning mellan flera CDN-leverantörer med ett enda ursprung.
* Strömmande blandat innehåll: Live och Video on Demand. 

    Åtkomstmönstren för live- och on-demand-innehåll är mycket olika. Det levande innehållet tenderar att få en stor efterfrågan på samma innehåll på en gång. Videoinnehållet på begäran (till exempel långt arkivinnehåll) har låg användning på samma innehåll. Således caching fungerar mycket bra på levande innehåll men inte lika bra på den långa svansen innehåll.

    Tänk dig ett scenario där dina kunder främst tittar på liveinnehåll men bara ibland tittar på innehåll på begäran och det visas från samma slutpunkt för direktuppspelning. Den låga användningen av innehåll på begäran skulle uppta cacheutrymme som skulle vara bättre sparas för live-innehåll. I det här fallet rekommenderar vi att du visar liveinnehållet från en slutpunkt för direktuppspelning och det långa svansinnehållet från en annan slutpunkt för direktuppspelning. Detta kommer att förbättra prestanda för live-händelseinnehållet.
    
## <a name="scaling-streaming-with-cdn"></a>Skala direktuppspelning med CDN

Se följande artiklar:

- [CDN-översikt](../../cdn/cdn-overview.md)
- [Skala direktuppspelning med CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Ställ frågor och få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="see-also"></a>Se även

[Dynamisk paketering](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Nästa steg

[Hantera slutpunkter för direktuppspelning](manage-streaming-endpoints-howto.md)
