---
title: Strömmande slutpunkter i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad slutpunkter för direktuppspelning är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: eb7f368100269c4e47076bb6b78bafc23e7a6089
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845611"
---
# <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

I Microsoft Azure Media Services (AMS), den [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints) entitet representerar en direktuppspelningstjänst som kan leverera innehåll direkt till ett klientspelarprogram eller till ett CDN Content Delivery Network () för ytterligare distribution. Den utgående dataströmmen från en **Strömningsslutpunkt** tjänsten kan vara en direktsänd dataström eller en video på begäran-tillgång i Media Services-kontot. När du skapar ett Media Services-konto, en **standard** slutpunkt för direktuppspelning skapas åt dig i ett stoppat tillstånd. Du kan inte ta bort den **standard** slutpunkt för direktuppspelning. Du kan skapa ytterligare slutpunkter för direktuppspelning under kontot. 

> [!NOTE]
> Om du vill starta direktuppspelning av videor, måste du starta den **Strömningsslutpunkt** som du vill att strömma videon. 

## <a name="naming-convention"></a>Namngivningskonventioner

För standardslutpunkt: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

För alla ytterligare slutpunkter: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Typer  

Det finns två **Strömningsslutpunkt** typer: **Standard** och **Premium**. Typen har definierats av antalet skalningsenheter (`scaleUnits`) du allokera för slutpunkten för direktuppspelning. 

Tabellen beskrivs typerna:  

|Type|Skalningsenheter|Beskrivning|
|--------|--------|--------|  
|**Standard-slutpunkt för direktuppspelning** (rekommenderas)|0|Den **Standard** typ är det rekommenderade alternativet för i stort sett alla strömmande scenarier och Publiker. Den **Standard** typ skalar utgående bandbredd automatiskt. <br/>För kunder med mycket stora krav för Media Services erbjuder **Premium** slutpunkter för direktuppspelning, som kan användas för att skala ut kapacitet för de största internet målgrupperna. Om du förväntar dig stora målgrupper och samtidiga läsare du kontakta oss på amsstreaming\@microsoft.com anvisningar om du behöver flytta till den **Premium** typen. |
|**Premium-slutpunkt för direktuppspelning**|>0|**Premium**-slutpunkter för direktuppspelning passar för avancerade arbetsbelastningar och tillhandahåller dedikerad och skalbar bandbreddskapacitet. Du flyttar till en **Premium** typ genom att justera `scaleUnits`. `scaleUnits` ger dig särskild egresskapacitet som kan köpas i steg om 200 Mbit/s. När du använder den **Premium** typ, varje aktiverad enhet tillhandahåller ytterligare bandbreddskapacitet till programmet. |

## <a name="working-with-cdn"></a>Arbeta med CDN

I de flesta fall bör du ha CDN har aktiverats. Men om du förutser max samtidighet som är lägre än 500 användare rekommenderas sedan det att inaktivera CDN eftersom CDN skalas bästa med samtidighet.

> [!NOTE]
> Slutpunkt för direktuppspelning `hostname` och strömnings-URL är densamma, oavsett om du aktiverar CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Detaljerad förklaring av så här fungerar cachelagring

Det finns ingen specifik bandbredd värde när du lägger till CDN eftersom mängden bandbredd som krävs för ett CDN aktiverat slutpunkten för direktuppspelning varierar. Mycket beror på vilken typ av innehåll, hur populär är det, olika bithastigheter och protokollen. CDN endast cachelagring vad som efterfrågas. Det innebär att populära innehållet ska tillhandahållas direkt från CDN – så länge video fragment cachelagras. Direktsänt innehåll är troligt att cachelagras eftersom du vanligtvis har många människor som tittar på exakt samma sak. Innehåll på begäran kan vara lite svårare, eftersom du kan ha innehåll som är populära inte och vissa. Om du har flera miljoner videotillgångar där ingen av dem är populära (endast 1 eller 2 användare per vecka), men du har tusentals människor som tittar på alla olika videor blir CDN mycket mindre effektiva. Med det här cacheminnet du missar, ökar belastningen på slutpunkten för direktuppspelning.
 
Du måste också tänka på hur anpassningsbar direktuppspelning fungerar. Varje enskild video fragment cachelagras eftersom den egna enheten. Till exempel om första gången en viss video har sett personen som hoppar över runt tittar på bara några sekunder få här och där endast video fragment som är associerade med personen sett cachelagras i CDN. Med adaptiv direktuppspelning har du normalt 5 till 7 olika bithastighet video. Om en person på en bithastighet och en annan person tittar på en annan bithastighet, sedan cachelagras de var separat i CDN. Även om två personer tittar på samma bithastighet kan de direktuppspelning via olika protokoll. Varje protokoll (HLS, MPEG-DASH, Smooth Streaming) cachelagras separat. Så varje bithastighet och protokoll cachelagras separat och dessa video fragment som har begärt cachelagras.
 
## <a name="properties"></a>Egenskaper 

Det här avsnittet innehåller information om några av slutpunkt för direktuppspelning egenskaper. Exempel på hur du skapar en ny slutpunkt för direktuppspelning och beskrivningar av alla egenskaper finns i [Strömningsslutpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` – Används för att konfigurera följande säkerhetsinställningarna för den här slutpunkten för direktuppspelning: Akamai signatur huvudnycklarna autentisering och IP-adresser som tillåts ansluta till den här slutpunkten.<br />Den här egenskapen kan anges när `cdnEnabled` är inställd på false.
- `cdnEnabled` -Anger om Azure CDN-integreringen för den här slutpunkten för direktuppspelning är aktiverad (inaktiverad som standard). Om du ställer in `cdnEnabled` true, följande konfigurationer inaktiveras: `customHostNames` och `accessControl`.
  
    Inte alla Datacenter stöd för Azure CDN-integrering. Du kan kontrollera om ditt datacenter har den Azure CDN-integreringen som är tillgängliga genom att göra följande:
 
  - Försök att ställa in den `cdnEnabled` till true.
  - Kontrollera det returnerade resultatet för en `HTTP Error Code 412` (PreconditionFailed) med ett meddelande om ”strömmande slutpunkt CdnEnabled egenskapen kan inte anges som SANT när CDN-funktionen inte är tillgänglig i den aktuella regionen”. 

    Om du får det här felet kan stöder inte datacentret den. Du bör försöka ett annat datacenter.
- `cdnProfile` – När `cdnEnabled` har angetts till SANT, du kan även skicka `cdnProfile` värden. `cdnProfile` är namnet på CDN-profil där CDN endpoint plats kommer att skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är NULL och `cdnEnabled` är true, ”AzureMediaStreamingPlatformCdnProfile” används standardvärdet. Om de angivna `cdnProfile` finns redan en slutpunkt har skapats under den. Om profilen inte finns, hämtar en ny profil skapas automatiskt.
- `cdnProvider` -När CDN är aktiverat, kan du även skicka `cdnProvider` värden. `cdnProvider` styr vilken provider som ska användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Om inget värde anges och `cdnEnabled` är true, ”StandardVerizon” används (det vill säga standardvärdet).
- `crossSiteAccessPolicies` – Används för att ange mellan åtkomstprinciper för olika klienter. Mer information finns i [domänerna filen principspecifikationen](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [att göra en tjänst tillgänglig över domängränser](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).
- `customHostNames` – Används för att konfigurera en Strömningsslutpunkt om du vill ta emot trafik dirigeras till ett anpassat värdnamn.  Den här egenskapen gäller för Standard och Premium-slutpunkter för direktuppspelning och kan anges när `cdnEnabled`: false.
    
    Ägare till domännamnet måste bekräftas av Media Services. Media Services kontrollerar domännamnägarskapet genom att kräva en `CName` post som innehåller Media Services-konto-ID som en komponent som ska läggas till domän används. Till exempel för ”sports.contoso.com” som ska användas som ett anpassat värdnamn för slutpunkten för direktuppspelning, en post för `<accountId>.contoso.com` måste konfigureras för att peka på något av värdnamn för Media Services-verifiering. Verifiering värdnamn består av verifydns. \<mediaservices-dns-zon >. 

    Följande är de förväntade DNS-zonerna som ska användas i Kontrollera posten för olika Azure-regioner.
  
  - North America, Europe, Singapore, Hong Kong, Japan:
      
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
    
- `scaleUnits ` – Ger dig särskild egresskapacitet som kan köpas i steg om 200 Mbit/s. Om du vill flytta till en **Premium** skriver, justera `scaleUnits`.

## <a name="next-steps"></a>Nästa steg

Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar den standardslutpunkt för direktuppspelning med .NET.

