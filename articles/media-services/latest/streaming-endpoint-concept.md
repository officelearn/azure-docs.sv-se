---
title: Slut punkter för direkt uppspelning (ursprung) i Azure Media Services | Microsoft Docs
description: I Azure Media Services representerar en strömmande slut punkt (ursprung) en dynamisk paketerings-och strömnings tjänst som kan leverera innehåll direkt till ett klient Player-program eller till en Content Delivery Network (CDN) för ytterligare distribution.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd1dc7b55060e8262b300022f5ffd1b4da5f7922
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350358"
---
# <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning 

I Microsoft Azure Media Services representerar en [slut punkt för direkt uppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints) en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och på begäran-innehåll direkt till ett klient Player-program med ett av de vanliga strömmande medierna protokoll (HLS eller tank streck). Dessutom tillhandahåller **direkt uppspelnings slut punkten** dynamisk kryptering (just-in-Time) för att branschledande DRM: er.

När du skapar ett Media Services konto skapas en **standard** slut punkt för direkt uppspelning när du har stoppat tillstånd. Det går inte att ta bort **standard** slut punkten för direkt uppspelning. Ytterligare slut punkter för direkt uppspelning kan skapas under kontot (se [kvoter och begränsningar](limits-quotas-constraints.md)). 

> [!NOTE]
> Om du vill starta strömmande videor måste du starta den **strömnings slut punkt** från vilken du vill strömma videon. 
>  
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

## <a name="naming-convention"></a>Namngivnings konvention

Värd namn formatet för strömnings-URL: en är: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, där `servicename` = strömmande slut punkts namn eller Live-händelseloggen. 

När du använder standard slut punkten för direkt uppspelning `servicename` utelämnas så att URL: en är: `{accountname}-{regionname}.streaming.azure.net`. 

### <a name="limitations"></a>Begränsningar

* Slut punktens namn för direkt uppspelning har ett max värde på 24 tecken.
* Namnet ska följa detta [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -mönster: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typer  

Det finns två typer av **slutpunkter för direktuppspelning**: **Standard** (för hands version) och **Premium**. Typen definieras av antalet skalnings enheter (`scaleUnits`) som du allokerar för slut punkten för direkt uppspelning. 

I tabellen beskrivs typerna:  

|type|Skalningsenheter|Beskrivning|
|--------|--------|--------|  
|**Standard**|0|Standard slut punkten för direkt uppspelning är en **standard** typ, kan ändras till Premium-typen genom `scaleUnits`att justera.|
|**Premium**|>0|**Premium** Slut punkter för direkt uppspelning passar för avancerade arbets belastningar och ger dedikerad och skalbar bandbredds kapacitet. Du flyttar till en **Premium** typ genom att `scaleUnits` justera (enheter för strömning). `scaleUnits`ge dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. När du använder typen **Premium** ger varje aktiverad enhet ytterligare bandbreddskapacitet till programmet. |

> [!NOTE]
> För kunder som vill leverera innehåll till stora Internet-åhörare rekommenderar vi att du aktiverar CDN på slut punkten för direkt uppspelning.

Information om SLA finns i [prissättning och service avtal](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Jämföra strömnings typer

Funktion|Standard|Premium
---|---|---
Dataflöde |Upp till 600 Mbit/s och kan ge ett mycket mer effektivt data flöde när ett CDN används.|200 Mbit/s per strömnings enhet (SU). Kan ge ett mycket högre effektivt data flöde när ett CDN används.
CDN|Azure CDN, CDN för tredje part eller ingen CDN.|Azure CDN, CDN för tredje part eller ingen CDN.
Faktureringen beräknas proportionellt| Dagligen|Dagligen
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Skalar upp till det riktade data flödet automatiskt.|Ytterligare SUs
IP-filtrering/G20/anpassad värd <sup>1</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderad användning |Rekommenderas för de flesta strömnings scenarier.|Professional-användning.

<sup>1</sup> används endast direkt på slut punkten för direkt uppspelning när CDN inte är aktiverat på slut punkten.<br/>

## <a name="properties"></a>properties 

Det här avsnittet innehåller information om några av egenskaperna för strömnings slut punkten. Exempel på hur du skapar en ny slut punkt för direkt uppspelning och beskrivningar av alla egenskaper finns i [direkt uppspelnings slut punkt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl`– Används för att konfigurera följande säkerhets inställningar för den här slut punkten för direkt uppspelning: Akamai för signatur rubrik och IP-adresser som tillåts att ansluta till den här slut punkten.<br />Den här egenskapen kan bara anges när `cdnEnabled` har angetts till false.
- `cdnEnabled`-Anger om Azure CDN-integrering för den här slut punkten för direkt uppspelning är aktive rad (inaktive rad som standard). Om du anger `cdnEnabled` till sant inaktive ras följande konfigurationer: `customHostNames` och `accessControl`.
  
    Alla data Center stöder inte Azure CDN-integrering. Gör så här för att kontrol lera om ditt data Center har Azure CDN-integrering tillgänglig:
 
  - Försök att ange `cdnEnabled` värdet sant.
  - Kontrol lera det returnerade resultatet `HTTP Error Code 412` för en (PreconditionFailed) med ett meddelande om att det inte går att ange egenskapen CdnEnabled för strömmande slut punkt till True eftersom CDN-funktionen inte är tillgänglig i den aktuella regionen. 

    Om du får det här felet stöder inte data centret det. Du bör testa ett annat data Center.
- `cdnProfile`– Om `cdnEnabled` är inställt på Sant kan du också `cdnProfile` skicka värden. `cdnProfile`är namnet på CDN-profilen där CDN-slutpunkten kommer att skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är null och `cdnEnabled` sant används standardvärdet "AzureMediaStreamingPlatformCdnProfile". Om den angivna `cdnProfile` redan finns skapas en slut punkt under den. Om profilen inte finns skapas en ny profil automatiskt.
- `cdnProvider`– När CDN har Aktiver ATS kan du också `cdnProvider` skicka värden. `cdnProvider`styr vilken provider som ska användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" och "StandardAkamai". Om inget värde anges och `cdnEnabled` är sant används "StandardVerizon" (det är standardvärdet).
- `crossSiteAccessPolicies`– Används för att ange åtkomst principer mellan platser för olika klienter. Mer information finns i [Specifikation över domän princip fil](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [göra en tjänst tillgänglig över domän gränser](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Inställningarna gäller endast för Smooth Streaming.
- `customHostNames`– Används för att konfigurera en slut punkt för direkt uppspelning för att acceptera trafik som dirigerats till ett anpassat värdnamn.  Den här egenskapen är giltig för slut punkter för standard-och Premium-direktuppspelning och `cdnEnabled`kan ställas in när: false.
    
    Ägande rätten till domän namnet måste bekräftas av Media Services. Media Services verifierar domän namnets ägarskap genom att kräva en `CName` post som innehåller Media Services-konto-ID som en komponent som ska läggas till i domänen som används. Som exempel, för att "Sports.contoso.com" ska användas som ett anpassat värdnamn för strömnings slut punkten måste en post för `<accountId>.contoso.com` vara konfigurerad för att peka på ett av Media Services verifierings värd namn. Verifierings värd namnet består av verifydns. \<Media Services-> för DNS-zon. 

    Följande är de DNS-zoner som förväntas användas i verifiera-posten för olika Azure-regioner.
  
  - Nordamerika, Europa, Singapore, Hong Kong SAR, Japan:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Porslin
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Till exempel bevisar en `CName` post som mappar "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" till "verifydns.Media.Azure.net" att Media Services-ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad har ägarskapet till contoso.com-domänen, så att aktivera alla namn under contoso.com som ska användas som ett anpassat värdnamn för en strömnings slut punkt under det kontot. Om du vill hitta medie tjänstens ID-värde går du till [Azure Portal](https://portal.azure.com/) och väljer ditt medie tjänst konto. **Konto-ID** visas längst upp till höger på sidan.
        
    Om det finns ett försök att ange ett anpassat värdnamn utan en korrekt verifiering av `CName` posten, kommer DNS-svaret att Miss lyckas och cachelagras under en viss tid. När en riktig post är på plats kan det ta en stund tills det cachelagrade svaret har verifierats igen. Beroende på DNS-providern för den anpassade domänen kan det ta var som helst från några minuter till en timme för att omverifiera posten.
        
    `CName` Förutom det `sports.contoso.com`som mappar `<accountId>.<parent domain>` till `verifydns.<mediaservices-dns-zone>`måste du skapa en annan `CName` som mappar det anpassade värd namnet (till exempel) till din Media Services direkt slut punkts värdnamn (till exempel `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Slut punkter för direkt uppspelning som finns i samma data Center kan inte dela samma anpassade värd namn.

    Media Services stöder för närvarande inte SSL med anpassade domäner. 
    
- `maxCacheAge`– Åsidosätter standardvärdet för max-ålder i HTTP-cachen som anges av slut punkten för direkt uppspelning av medie fragment och manifest på begäran. Värdet anges i sekunder.
- `resourceState` -

    - Stoppad – ursprunglig status för en strömmande slut punkt efter skapandet
    - Startar-övergår till körnings tillstånd
    - Körs – kan strömma innehåll till klienter
    - Skalning – skalnings enheterna ökas eller minskas
    - Stoppar-övergår till stoppat tillstånd
    - Tar bort-tas bort
    
- `scaleUnits`– Ger dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. Om du behöver flytta till en **Premium** -typ justerar `scaleUnits`du.

## <a name="working-with-cdn"></a>Arbeta med CDN

I de flesta fall bör du ha CDN aktiverat. Men om du förväntar dig en maximal samtidighet som är lägre än 500 användare rekommenderas det att du inaktiverar CDN eftersom CDN skalas bäst med samtidighet.

### <a name="considerations"></a>Överväganden

* Slut punkten för direkt uppspelning `hostname` och direkt uppspelnings-URL: en är oförändrad oavsett om du aktiverar CDN eller inte.
* Om du behöver kunna testa ditt innehåll med eller utan CDN kan du skapa en annan slut punkt för direkt uppspelning som inte är CDN-aktiverad.

### <a name="detailed-explanation-of-how-caching-works"></a>Detaljerad förklaring av hur cachelagring fungerar

Det finns inget visst bandbredds värde när CDN läggs till, eftersom mängden bandbredd som behövs för en CDN-aktiverad strömnings slut punkt varierar. Ett stort parti beror på typen av innehåll, hur populära den är, bit hastigheter och protokollen. CDN cachelagrar bara det som begärs. Det innebär att populära innehåll kommer att betjänas direkt från CDN – så länge video fragmentet cachelagras. Live-innehåll cachelagras förmodligen på grund av att många personer tittar på exakt samma sak. Innehåll på begäran kan vara en bit trickier eftersom du kan ha visst innehåll som är populärt och en del som inte är det. Om du har miljon tals video till gångar där ingen av dem är populära (endast 1 eller 2 användare per vecka), men du har tusentals människor som tittar på alla olika videor, blir CDN mycket mindre effektivt. Med dessa Cachemissar ökar belastningen på slut punkten för direkt uppspelning.
 
Du måste också fundera över hur anpassningsbar strömning fungerar. Varje enskilt video fragment cachelagras i sin egen entitet. Till exempel, om den första gången en viss video bevakas, hoppar personen över bara några sekunder här och bara de videofragment som är associerade med vad den person som bevakar cachelagras i CDN. Med anpassningsbar strömning har du normalt 5 till 7 olika bit hastigheter för video. Om en person tittar på en bit hastighet och en annan person tittar på en annan bit hastighet, är de båda cachelagrade separat i CDN. Även om två personer tittar på samma bit hastighet kan de strömmas över olika protokoll. Varje protokoll (HLS, MPEG-streck, Smooth Streaming) cachelagras separat. Varje bit hastighet och protokoll cachelagras separat och endast de video fragment som har begärts cachelagras.

### <a name="enable-azure-cdn-integration"></a>Aktivera Azure CDN-integrering

När en slut punkt för direkt uppspelning har tillhandahållits med CDN aktiverat, finns en definierad vänte tid på Media Services innan DNS-uppdateringen görs för att mappa slut punkten för direkt uppspelning till CDN-slutpunkten.

Om du senare vill inaktivera/aktivera CDN måste slut punkten för direkt uppspelning vara i **stoppat** tillstånd. Det kan ta upp till två timmar innan Azure CDN-integreringen har Aktiver ATS och för att ändringarna ska vara aktiva över alla CDN pop. Men du kan starta slut punkten för direkt uppspelning och strömma utan avbrott från slut punkten för direkt uppspelning och när integrationen är klar levereras data strömmen från CDN. Under etablerings perioden kommer slut punkten för direkt uppspelning att vara i **Start** läge och du kan observera försämrade prestanda.

När standard slut punkten för direkt uppspelning skapas, konfigureras den som standard med standard-Verizon. Du kan konfigurera Premium Verizon-eller standard Akamai-providers med hjälp av REST API: er. 

CDN-integrering är aktiverat i alla Azure-datacenter förutom Kina och federala myndighets regioner.

> [!IMPORTANT]
> Azure Media Services integration med Azure CDN implementeras på **Azure CDN från Verizon** för standard slut punkter för direkt uppspelning. Premium-slutpunkter för direkt uppspelning kan konfigureras med alla **Azure CDN pris nivåer och leverantörer**. Mer information om Azure CDN funktioner finns i CDN- [översikten](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Avgöra om DNS-ändring har gjorts

Du kan avgöra om DNS-ändring har gjorts på en strömmande slut punkt (trafiken dirigeras till Azure CDN) med hjälp https://www.digwebinterface.com av. Om resultatet har azureedge.net domän namn i resultaten kommer trafiken nu att pekas mot CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar standard slut punkten för direkt uppspelning med .net.

