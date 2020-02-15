---
title: Slut punkter för direkt uppspelning (ursprung)
titleSuffix: Azure Media Services
description: Lär dig mer om strömnings slut punkter (ursprung), en dynamisk paketerings-och strömnings tjänst som levererar innehåll direkt till en app i klient programmet eller till en Content Delivery Network (CDN).
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
ms.openlocfilehash: c1e9be605a6f01695f2472ae76a9e5a786388aa0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206114"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Slut punkter för direkt uppspelning (ursprung) i Azure Media Services

I Microsoft Azure Media Services representerar en [strömmande slut punkt](https://docs.microsoft.com/rest/api/media/streamingendpoints) en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och innehåll på begäran direkt till en app i klient spelaren med ett av de vanliga protokollen för strömnings mediet (HLS eller tank streck). Dessutom tillhandahåller **direkt uppspelnings slut punkten** dynamisk kryptering (just-in-Time) för att branschledande DRM: er.

När du skapar ett Media Services konto skapas en **standard** slut punkt för direkt uppspelning när du har stoppat tillstånd. Det går inte att ta bort **standard** slut punkten för direkt uppspelning. Fler slut punkter för direkt uppspelning kan skapas under kontot (se [kvoter och begränsningar](limits-quotas-constraints.md)).

> [!NOTE]
> Om du vill starta strömmande videor måste du starta den **strömnings slut punkt** från vilken du vill strömma videon.
>
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

## <a name="naming-convention"></a>Namngivnings konvention

Värd namn formatet för strömnings-URL: en är: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, där `servicename` = direkt uppspelningens slut punkts namn eller Live-händelsens namn.

När du använder standard slut punkten för direkt uppspelning utelämnas `servicename` så att URL: en är: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Begränsningar

* Slut punktens namn för direkt uppspelning har ett max värde på 24 tecken.
* Namnet ska följa detta [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -mönster: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typer

Det finns två typer av **direkt uppspelnings slut punkter** : **standard** (för hands version) och **Premium**. Typen definieras av antalet skalnings enheter (`scaleUnits`) som du allokerar för slut punkten för direkt uppspelning.

I tabellen beskrivs typerna:

|Typ|Skalningsenheter|Beskrivning|
|--------|--------|--------|  
|**Standard**|0|Standard slut punkten för direkt uppspelning är en **standard** typ – den kan ändras till Premium-typen genom att justera `scaleUnits`.|
|**Premium**|> 0|**Premium** Slut punkter för direkt uppspelning passar för avancerade arbets belastningar och tillhandahåller dedikerad och skalbar bandbredds kapacitet. Du flyttar till en **Premium** -typ genom att justera `scaleUnits` (enheter för strömning). `scaleUnits` ger dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. När du använder **Premium** -typen ger varje aktive rad enhet ytterligare bandbredds kapacitet till appen. |

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

## <a name="properties"></a>Egenskaper

Det här avsnittet innehåller information om några av egenskaperna för strömnings slut punkten. Exempel på hur du skapar en ny slut punkt för direkt uppspelning och beskrivningar av alla egenskaper finns i [direkt uppspelnings slut punkt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: används för att konfigurera följande säkerhets inställningar för den här strömnings slut punkten: Akamai för signatur huvud och IP-adresser som tillåts att ansluta till den här slut punkten. Den här egenskapen kan bara anges när `cdnEnabled` har angetts till false.

- `cdnEnabled`: anger om Azure CDN-integrering för slut punkten för direkt uppspelning är aktive rad (inaktive rad som standard). Om du anger `cdnEnabled` till sant inaktive ras följande konfigurationer: `customHostNames` och `accessControl`.

    Alla data Center stöder inte Azure CDN-integrering. Gör så här för att kontrol lera om data centret har Azure CDN-integrering tillgänglig:

  - Försök att ange `cdnEnabled` till sant.
  - Kontrol lera det returnerade resultatet för en `HTTP Error Code 412` (PreconditionFailed) med ett meddelande om att egenskapen CdnEnabled för strömnings slut punkt inte kan anges till True eftersom CDN-funktionen inte är tillgänglig i den aktuella regionen.

    Om du får det här felet stöder inte data centret det. Prova ett annat data Center.

- `cdnProfile`: när `cdnEnabled` har angetts till true kan du också skicka `cdnProfile` värden. `cdnProfile` är namnet på CDN-profilen där CDN-slutpunkten kommer att skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är NULL och `cdnEnabled` är sant används standardvärdet "AzureMediaStreamingPlatformCdnProfile". Om den tillhandahållna `cdnProfile` redan finns skapas en slut punkt under den. Om profilen inte finns skapas en ny profil automatiskt.
- `cdnProvider`: när CDN har Aktiver ATS kan du också skicka `cdnProvider` värden. `cdnProvider` styr vilken provider som ska användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" och "StandardAkamai". Om inget värde anges och `cdnEnabled` är sant används "StandardVerizon" (det är standardvärdet).
- `crossSiteAccessPolicies`: används för att ange åtkomst principer mellan platser för olika klienter. Mer information finns i [Specifikation över domän princip fil](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [göra en tjänst tillgänglig över domän gränser](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Inställningarna gäller endast för Smooth Streaming.
- `customHostNames`: används för att konfigurera en slut punkt för direkt uppspelning för att acceptera trafik som dirigerats till ett anpassat värdnamn. Den här egenskapen är giltig för slut punkter för standard-och Premium-direktuppspelning och kan ställas in när `cdnEnabled`: false.

    Ägande rätten till domän namnet måste bekräftas av Media Services. Media Services verifierar domän namnet ägarskap genom att kräva en `CName`s post som innehåller Media Services-konto-ID som en komponent som ska läggas till i domänen som används. Som exempel, för att "sports.contoso.com" ska användas som ett anpassat värdnamn för strömnings slut punkten, måste en post för `<accountId>.contoso.com` konfigureras för att peka på ett av Media Services verifierings värd namn. Verifierings värd namnet består av verifydns.\<Media Services-DNS-Zone >.

    Följande är de DNS-zoner som förväntas användas i verifiera-posten för olika Azure-regioner.
  
  - Nordamerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Porslin

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Till exempel bevisar en `CName` post som mappar "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" till "verifydns.media.azure.net" att Media Services-ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad har ägandet av contoso.com-domänen, vilket innebär att alla namn under contoso.com kan användas som ett anpassat värdnamn för en strömmande slut punkt under det kontot. Om du vill hitta medie tjänstens ID-värde går du till [Azure Portal](https://portal.azure.com/) och väljer ditt medie tjänst konto. **Konto-ID** visas längst upp till höger på sidan.

    Om det finns ett försök att ange ett anpassat värdnamn utan en korrekt verifiering av `CName`s posten, kommer DNS-svaret att Miss lyckas och cachelagras under en viss tid. När en riktig post är på plats kan det ta en stund tills det cachelagrade svaret har verifierats igen. Beroende på DNS-providern för den anpassade domänen tar det var som helst från några minuter till en timme för att omverifiera posten.

    Förutom `CName` som mappar `<accountId>.<parent domain>` till `verifydns.<mediaservices-dns-zone>`måste du skapa en annan `CName` som mappar det anpassade värd namnet (till exempel `sports.contoso.com`) till din Media Services streaming-slutpunkts värdnamn (till exempel `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Slut punkter för direkt uppspelning som finns i samma data Center kan inte dela samma anpassade värd namn.

    Media Services stöder för närvarande inte SSL med anpassade domäner.

- `maxCacheAge`-åsidosätter standardvärdet för max-ålder för HTTP-cachen som angetts av slut punkten för direkt uppspelning av medie fragment och manifest på begäran. Värdet anges i sekunder.
- `resourceState` -

    - Stoppades: initialt tillstånd för en strömmande slut punkt efter skapandet
    - Startar: övergår till körnings tillstånd
    - Körs: kan strömma innehåll till klienter
    - Skalning: skalnings enheterna ökas eller minskas
    - Stoppar: övergår till stoppat tillstånd
    - Tar bort: tas bort

- `scaleUnits`: ge dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. Om du behöver flytta till en **Premium** -typ justerar du `scaleUnits`.

## <a name="working-with-cdn"></a>Arbeta med CDN

I de flesta fall bör du ha CDN aktiverat. Men om du förutser att max samtidighet är lägre än 500 visnings program, rekommenderar vi att du inaktiverar CDN eftersom CDN skalar bäst med samtidighet.

### <a name="considerations"></a>Överväganden

* Slut punkten för direkt uppspelning `hostname` och strömnings-URL: en är oförändrad oavsett om du aktiverar CDN eller inte.
* Om du behöver kunna testa ditt innehåll med eller utan CDN, skapar du en annan slut punkt för direkt uppspelning som inte är CDN-aktiverad.

### <a name="detailed-explanation-of-how-caching-works"></a>Detaljerad förklaring av hur cachelagring fungerar

Det finns inget visst bandbredds värde när du lägger till CDN eftersom mängden bandbredd som behövs för en CDN-aktiverad strömnings slut punkt varierar. Ett stort parti beror på typen av innehåll, hur populära det är, bit hastigheterna och protokollen. CDN cachelagrar bara vad som begärs. Det innebär att populära innehåll kommer att betjänas direkt från CDN – så länge video fragmentet cachelagras. Live-innehåll cachelagras förmodligen på grund av att många personer tittar på exakt samma sak. Innehåll på begäran kan vara en bit trickier eftersom du kan ha ett visst innehåll som är populärt och en del som inte är det. Om du har miljon tals video till gångar där ingen av dem är populär (endast en eller två användare per vecka), men du har tusentals människor som tittar på alla olika videor, blir CDN mycket mindre effektivt. Med dessa Cachemissar ökar belastningen på slut punkten för direkt uppspelning.

Du måste också fundera över hur anpassningsbar strömning fungerar. Varje enskilt video fragment cachelagras i sin egen entitet. Anta till exempel att en viss video är bevakad första gången. Om visnings programmet hoppar över att titta bara några sekunder här och där finns det bara videofragment som är associerade med vad den person som bevakar cachelagrar i CDN. Med anpassningsbar strömning har du normalt 5 till 7 olika bit hastigheter för video. Om en person tittar på en bit hastighet och en annan person tittar på en annan bit hastighet, är de båda cachelagrade separat i CDN. Även om två personer tittar på samma bit hastighet kan de strömmas över olika protokoll. Varje protokoll (HLS, MPEG-streck, Smooth Streaming) cachelagras separat. Varje bit hastighet och protokoll cachelagras separat och endast de video fragment som har begärts cachelagras.

### <a name="enable-azure-cdn-integration"></a>Aktivera Azure CDN-integrering

> [!IMPORTANT]
> Du kan inte aktivera CDN för utvärderings-eller student Azure-konton.
>
> CDN-integrering är aktiverat i alla Azure-datacenter utom federala myndigheter och regioner i Kina.

När en slut punkt för direkt uppspelning har tillhandahållits med CDN aktiverat har en definierad vänte tid på Media Services innan DNS-uppdateringen görs för att mappa slut punkten för direkt uppspelning till CDN-slutpunkten.

Om du senare vill inaktivera/aktivera CDN måste slut punkten för direkt uppspelning vara i **stoppat** tillstånd. Det kan ta upp till två timmar innan Azure CDN-integreringen har Aktiver ATS och för att ändringarna ska vara aktiva över alla CDN pop. Du kan dock starta slut punkten för direkt uppspelning och strömma utan avbrott från slut punkten för direkt uppspelning och när integrationen är klar levereras data strömmen från CDN. Under etablerings perioden kommer slut punkten för direkt uppspelning att vara i **Start** läge och du kan observera försämrade prestanda.

När standard slut punkten för direkt uppspelning skapas, konfigureras den som standard med standard-Verizon. Du kan konfigurera Premium Verizon-eller standard Akamai-providers med hjälp av REST API: er.

Azure Media Services integration med Azure CDN implementeras på **Azure CDN från Verizon** för standard slut punkter för direkt uppspelning. Premium-slutpunkter för direkt uppspelning kan konfigureras med alla **Azure CDN pris nivåer och leverantörer**. 

> [!NOTE]
> Mer information om Azure CDN finns i [CDN-översikten](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Ta reda på om DNS-ändring gjorts

Du kan avgöra om DNS-ändring har gjorts på en strömmande slut punkt (trafiken dirigeras till Azure CDN) med hjälp av https://www.digwebinterface.com. Om resultatet har azureedge.net domän namn i resultaten kommer trafiken nu att pekas mot CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se även

[Översikt över CDN](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>Nästa steg

Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar standard slut punkten för direkt uppspelning med .net.
