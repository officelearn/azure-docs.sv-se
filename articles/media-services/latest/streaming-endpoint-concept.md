---
title: Strömmande slutpunkter (ursprungliga) i Azure Media Services | Microsoft Docs
description: I Azure Media Services representerar en slutpunkt för direktuppspelning (ursprungliga) en dynamisk paketering och en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN Content Delivery Network () för vidare distribution.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: 3f939154d2b34e6dc043e505ab89897221bcfe23
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149238"
---
# <a name="streaming-endpoints-origin"></a>Slutpunkter för direktuppspelning (ursprungliga)

I Microsoft Azure Media Services, en [Strömningsslutpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints) representerar en dynamisk (just-in-time) paketering och det ursprungliga tjänst som kan leverera ditt innehåll live och på begäran direkt till ett klientspelarprogram med någon av de vanliga media strömningsprotokoll (HLS eller DASH). Dessutom kan den **slutpunkt för direktuppspelning** erbjuder kryptering på dynamiska (just-in-time) till branschledande DRM: er.

När du skapar ett Media Services-konto, en **standard** slutpunkt för direktuppspelning skapas åt dig i ett stoppat tillstånd. Du kan inte ta bort den **standard** slutpunkt för direktuppspelning. Kan skapa ytterligare slutpunkter för direktuppspelning under kontot (se [kvoter och begränsningar](limits-quotas-constraints.md)). 

> [!NOTE]
> Om du vill starta direktuppspelning av videor, måste du starta den **Strömningsslutpunkt** som du vill att strömma videon. 
>  
> Du debiteras endast när din slutpunkt för direktuppspelning som är i körningstillstånd.

## <a name="naming-convention"></a>Namngivningskonventioner

För standardslutpunkt: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

För alla ytterligare slutpunkter: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Typer  

Det finns två typer av **slutpunkter för direktuppspelning**: **Standard** (förhandsversion) och **Premium**. Typen har definierats av antalet skalningsenheter (`scaleUnits`) du allokera för slutpunkten för direktuppspelning. 

I tabellen beskrivs typerna:  

|Typ|Skalningsenheter|Beskrivning|
|--------|--------|--------|  
|**Standard**|0|Standard-slutpunkt för direktuppspelning är en **Standard** skriver, kan ändras till typen Premium genom att justera `scaleUnits`.|
|**Premium**|> 0|**Premium** -slutpunkter för direktuppspelning som är lämpliga för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Du flyttar till en **Premium** typ genom att justera `scaleUnits` (enheter för strömning). `scaleUnits` ger dig särskild egresskapacitet som kan köpas i steg om 200 Mbit/s. När du använder typen **Premium** ger varje aktiverad enhet ytterligare bandbreddskapacitet till programmet. |

> [!NOTE]
> För kunder som planerar för att leverera innehåll till stora målgrupper för internet, rekommenderar vi att du aktiverar CDN på slutpunkt för direktuppspelning.

SLA-information, se [priser och SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Jämföra strömmande typer

Funktion|Standard|Premium
---|---|---
Kostnadsfri första 15 dagarna <sup>1</sup>| Ja |Nej
Dataflöde |Upp till 600 Mbit/s och kan tillhandahålla en mycket högre effektiva dataflöde när ett CDN används.|200 Mbit/s per enhet (SU) för strömning. Kan tillhandahålla en mycket högre effektiva dataflöde när ett CDN används.
CDN|Azure CDN från tredje part CDN eller inga CDN.|Azure CDN från tredje part CDN eller inga CDN.
Fakturering beräknas| Dagligen|Dagligen
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Automatiskt skalar upp det riktade dataflödet.|Ytterligare SUs
IP-filtrering/G20/anpassat värd <sup>2</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderade användningen |Rekommenderas för merparten av scenarier för direktuppspelning.|Professionella användning.

<sup>1</sup> den kostnadsfria utvärderingsversionen gäller endast för nya media services-konton och standardvärdet slutpunkten för direktuppspelning.<br/>
<sup>2</sup> endast används direkt på den slutpunkt för direktuppspelning när CDN inte är aktiverat på slutpunkten.<br/>

## <a name="properties"></a>Egenskaper 

Det här avsnittet innehåller information om några av slutpunkt för direktuppspelning egenskaper. Exempel på hur du skapar en ny slutpunkt för direktuppspelning och beskrivningar av alla egenskaper finns i [Strömningsslutpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` – Används för att konfigurera följande säkerhetsinställningarna för den här slutpunkten för direktuppspelning: Akamai signatur huvudnycklarna autentisering och IP-adresser som tillåts ansluta till den här slutpunkten.<br />Den här egenskapen kan bara anges när `cdnEnabled` är inställd på false.
- `cdnEnabled` -Anger om Azure CDN-integreringen för den här slutpunkten för direktuppspelning är aktiverad (inaktiverad som standard). Om du ställer in `cdnEnabled` true, följande konfigurationer inaktiveras: `customHostNames` och `accessControl`.
  
    Inte alla Datacenter stöd för Azure CDN-integrering. Du kan kontrollera om ditt datacenter har den Azure CDN-integreringen som är tillgängliga genom att göra följande:
 
  - Försök att ställa in den `cdnEnabled` till true.
  - Kontrollera det returnerade resultatet för en `HTTP Error Code 412` (PreconditionFailed) med ett meddelande om ”strömmande slutpunkt CdnEnabled egenskapen kan inte anges som SANT när CDN-funktionen inte är tillgänglig i den aktuella regionen”. 

    Om du får det här felet kan stöder inte datacentret den. Du bör försöka ett annat datacenter.
- `cdnProfile` – När `cdnEnabled` har angetts till SANT, du kan även skicka `cdnProfile` värden. `cdnProfile` är namnet på CDN-profil där CDN endpoint plats kommer att skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är NULL och `cdnEnabled` är true, ”AzureMediaStreamingPlatformCdnProfile” används standardvärdet. Om de angivna `cdnProfile` finns redan en slutpunkt har skapats under den. Om profilen inte finns, hämtar en ny profil skapas automatiskt.
- `cdnProvider` -När CDN är aktiverat, kan du även skicka `cdnProvider` värden. `cdnProvider` styr vilken provider som ska användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Om inget värde anges och `cdnEnabled` är true, ”StandardVerizon” används (det vill säga standardvärdet).
- `crossSiteAccessPolicies` – Används för att ange mellan åtkomstprinciper för olika klienter. Mer information finns i [domänerna filen principspecifikationen](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [att göra en tjänst tillgänglig över domängränser](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Inställningarna gäller endast för Smooth Streaming.
- `customHostNames` – Används för att konfigurera en Strömningsslutpunkt om du vill ta emot trafik dirigeras till ett anpassat värdnamn.  Den här egenskapen gäller för Standard och Premium-slutpunkter för direktuppspelning och kan anges när `cdnEnabled`: false.
    
    Ägare till domännamnet måste bekräftas av Media Services. Media Services kontrollerar domännamnägarskapet genom att kräva en `CName` post som innehåller Media Services-konto-ID som en komponent som ska läggas till domän används. Till exempel för ”sports.contoso.com” som ska användas som ett anpassat värdnamn för slutpunkten för direktuppspelning, en post för `<accountId>.contoso.com` måste konfigureras för att peka på något av värdnamn för Media Services-verifiering. Verifiering värdnamn består av verifydns. \<mediaservices-dns-zon >. 

    Följande är de förväntade DNS-zonerna som ska användas i Kontrollera posten för olika Azure-regioner.
  
  - Nordamerika, Europa, Singapore, Hongkong, Japan:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - I Kina:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Till exempel en `CName` post som mappar ”945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com” till ”verifydns.media.azure.net” bevisar att Media Services-ID-945a4c4e-28ea-45cd-8ccb-a519f6b700ad har ägarskap för domänen contoso.com, därför aktiverar vilket namn som helst under contoso.com som ska användas som ett anpassat värdnamn för en slutpunkt för direktuppspelning med det kontot. Om du vill hitta det Media Service-ID-värdet, går du till den [Azure-portalen](https://portal.azure.com/) och välj Media Service-konto. Den **konto-ID** visas längst upp till höger på sidan.
        
    Om det finns ett försök att ange ett anpassat värdnamn utan en korrekt kontroll av den `CName` posten, DNS-svaret misslyckas och sedan cachelagras under en viss tid. När en rätt post är på plats, kan det ta en stund tills det cachelagrade svaret har förnyats. Beroende på DNS-providern för den anpassade domänen, kan det ta var som helst från ett par minuter till en timme att verifiera posten.
        
    Förutom den `CName` som mappar `<accountId>.<parent domain>` till `verifydns.<mediaservices-dns-zone>`, måste du skapa en annan `CName` som mappar det anpassa värdnamnet (till exempel `sports.contoso.com`) till ditt Media Services Streaming slutpunktens värdnamn (till exempel `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Strömmande slutpunkter som finns i samma datacenter, kan inte dela samma anpassade värdnamn.

    För närvarande stöder Media Services inte SSL med anpassade domäner. 
    
- `maxCacheAge` -Åsidosättningar standard maxåldern HTTP cache styra rubrik som anges av slutpunkten för direktuppspelning på media fragment och på begäran manifest. Värdet anges i sekunder.
- `resourceState` -

    - Stoppat – det ursprungliga tillståndet för en Strömningsslutpunkt när du har skapat
    - Startar - övergår till tillståndet körs
    - Körs - ska kunna strömma innehåll till klienter
    - Skala - skalan enheter som ska ökas eller minskas
    - Stoppa - övergår i ett stoppat tillstånd
    - Tar bort - tas bort
    
- `scaleUnits` – Ger dig särskild egresskapacitet som kan köpas i steg om 200 Mbit/s. Om du vill flytta till en **Premium** skriver, justera `scaleUnits`.

## <a name="working-with-cdn"></a>Arbeta med CDN

I de flesta fall bör du ha CDN aktiverat. Men om du förväntar dig en maximal samtidighet som är lägre än 500 användare rekommenderas det att du inaktiverar CDN eftersom CDN skalas bäst med samtidighet.

### <a name="considerations"></a>Överväganden

* Slutpunkt för direktuppspelning `hostname` och strömnings-URL är densamma, oavsett om du aktiverar CDN.
* Om du behöver kunna testa ditt innehåll med eller utan CDN kan skapa du en annan slutpunkt för direktuppspelning som inte är CDN har aktiverats.

### <a name="detailed-explanation-of-how-caching-works"></a>Detaljerad förklaring av så här fungerar cachelagring

Det finns ingen specifik bandbredd värde när du lägger till CDN eftersom mängden bandbredd som krävs för ett CDN aktiverat slutpunkten för direktuppspelning varierar. Mycket beror på vilken typ av innehåll, hur populär är det, olika bithastigheter och protokollen. CDN endast cachelagring vad som efterfrågas. Det innebär att populära innehållet ska tillhandahållas direkt från CDN – så länge video fragment cachelagras. Direktsänt innehåll är troligt att cachelagras eftersom du vanligtvis har många människor som tittar på exakt samma sak. Innehåll på begäran kan vara lite svårare, eftersom du kan ha innehåll som är populära inte och vissa. Om du har flera miljoner videotillgångar där ingen av dem är populära (endast 1 eller 2 användare per vecka), men du har tusentals människor som tittar på alla olika videor blir CDN mycket mindre effektiva. Med det här cacheminnet du missar, ökar belastningen på slutpunkten för direktuppspelning.
 
Du måste också tänka på hur anpassningsbar direktuppspelning fungerar. Varje enskild video fragment cachelagras eftersom den egna enheten. Till exempel om första gången en viss video har sett personen som hoppar över runt tittar på bara några sekunder få här och där endast video fragment som är associerade med personen sett cachelagras i CDN. Med adaptiv direktuppspelning har du normalt 5 till 7 olika bithastighet video. Om en person på en bithastighet och en annan person tittar på en annan bithastighet, sedan cachelagras de var separat i CDN. Även om två personer tittar på samma bithastighet kan de direktuppspelning via olika protokoll. Varje protokoll (HLS, MPEG-DASH, Smooth Streaming) cachelagras separat. Så varje bithastighet och protokoll cachelagras separat och dessa video fragment som har begärt cachelagras.

### <a name="enable-azure-cdn-integration"></a>Aktivera Azure CDN-integrering

När en slutpunkt för direktuppspelning har etablerats med är CDN aktiverat det en definierad väntetid på Media Services innan DNS har uppdaterats för att mappa slutpunkt för direktuppspelning till CDN-slutpunkten.

Om du senare vill aktivera/inaktivera CDN slutpunkten för direktuppspelning måste finnas i den **stoppats** tillstånd. Det kan ta upp till två timmar för Azure CDN-integreringen ska aktiveras och för att ändringarna ska vara aktiva för alla innehållsleveransnätverk. Dock kan starta din strömmande slutpunkt och stream utan avbrott från slutpunkten för direktuppspelning och när integrationen är slutförd, dataströmmen levereras från CDN. Under etablering blir slutpunkten för direktuppspelning i **startar** tillstånd och du kan se försämrade prestanda.

När strömningsslutpunkt som Standard skapas är den konfigurerad som standard med Standard Verizon. Du kan konfigurera Verizon Premium eller Standard Akamai providers med hjälp av REST API: er. 

CDN-integreringen har aktiverats i alla Azure-Datacenter förutom Kina och federala myndigheter regioner.

> [!IMPORTANT]
> Azure Media Services-integrering med Azure CDN har implementerats på **Azure CDN från Verizon** för standard slutpunkter för direktuppspelning. Premium-slutpunkter för direktuppspelning kan konfigureras med alla **Azure CDN priser nivåerna och providers**. Mer information om Azure CDN-funktioner finns i den [översikt över CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Avgöra om DNS-ändring har gjorts

Du kan fastställa om DNS-ändring har gjorts på en slutpunkt för direktuppspelning (är att dirigeras trafiken till Azure CDN) genom att använda https://www.digwebinterface.com. Om resultatet har azureedge.net domännamn i resultatet, är trafiken nu att riktas till CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar den standardslutpunkt för direktuppspelning med .NET.

