---
title: Azure File Sync lokala brandvägg och proxyinställningar | Microsoft Docs
description: Azure File Sync lokala nätverkskonfiguration
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: fauhse
ms.component: files
ms.openlocfilehash: 44bfdd192f846b710e378b1f00799eda304cec1e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522772"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Inställningar för Azure File Sync-proxy och brandväggar
Azure File Sync ansluter dina lokala servrar till Azure Files, aktivering av multisite synkronisering och molnlagringsnivåer funktioner. Därför måste måste en lokal server vara ansluten till internet. IT-administratör måste avgöra den bästa vägen för att servern ska få åtkomst till Azure-molntjänster.

Den här artikeln ger insyn i specifika krav och tillgängliga alternativ för att kunna och på ett säkert sätt ansluta servern till Azure File Sync.

> [!Important]
> Azure File Sync stöder ännu inte brandväggar och virtuella nätverk för ett lagringskonto.

## <a name="overview"></a>Översikt
Azure File Sync fungerar som en tjänst för samordning mellan Windows Server, Azure-filresursen och flera andra Azure-tjänster för att synkronisera data enligt beskrivningen i dina synkroniseringsgruppen. Du behöver för Azure File Sync ska fungera korrekt, att konfigurera dina servrar att kommunicera med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Autentiseringstjänster

> [!Note]  
> Azure File Sync-agenten på Windows Server initierar alla förfrågningar till molntjänster, vilket leder till att bara behöva tänka på utgående trafik från ett perspektiv för brandväggen. <br /> Inga Azure-tjänsten initierar en anslutning till Azure File Sync-agenten.

## <a name="ports"></a>Portar
Azure File Sync flyttar data från filer och metadata exklusivt över HTTPS och kräver att port 443 till att öppna utgående.
Därmed är all trafik krypterad.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Azure File Sync-agenten har inga krav angående särskilda kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. till Azure.

Azure File Sync fungerar på alla sätt att nå Azure automatiskt om anpassning till olika Nätverksegenskaper som bandbredd och latens samtidigt som den ger administratörskontroll för att finjustera. Inte alla funktioner är tillgängliga just nu. Om du vill konfigurera specifika beteende, berätta för oss [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync stöder appspecifika och datoromfattande proxy-inställningar.

Datoromfattande proxy-inställningar är transparent för Azure File Sync-agenten på servern hela trafiken dirigeras via proxy.

Appspecifika proxyinställningarna för att konfigurera proxy specifikt för Azure File Sync-trafik. Appspecifika proxyinställningar stöds på agentversion 3.0.12.0 eller senare och kan konfigureras under agentinstallationen av eller med hjälp av cmdleten Set-StorageSyncProxyConfiguration PowerShell.

PowerShell-kommandon för att konfigurera proxyinställningar för appspecifika:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>Brandvägg
Port 443 måste vara öppna utgående som nämns i föregående avsnitt. Baserat på principer i ditt datacenter, gren eller din region kan kan ytterligare begränsa trafik via den här porten till vissa domäner vara önskade eller krävs.

I följande tabell beskrivs domänerna som krävs för kommunikation:

| Tjänst | Domän | Användning |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Ett anrop för användaren (till exempel PowerShell) går till/igenom den här URL: en, inklusive inledande server registrering anropet. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager-anrop måste göras av en autentiserad användare. Ska lyckas, används den här URL: en för autentisering av användare. |
| **Azure Active Directory** | https://graph.windows.net/ | Som en del av att distribuera Azure File Sync, skapas ett huvudnamn för tjänsten i prenumerationens Azure Active Directory. Den här URL: en används för detta. Den här huvudnamn används för att delegera en minimal uppsättning rättigheter till Azure File Sync-tjänsten. Användaren som utför installationen av Azure File Sync måste vara en autentiserad användare med behörighet för ägare av prenumerationen. |
| **Azure Storage** | &ast;.core.windows.net | När servern hämtar en fil, utför servern sedan den dataförflyttning mer effektivt när man talar direkt till Azure-filresursen i Lagringskontot. Servern har en SAS-nyckel som endast tillåter för åtkomst till resursen för filen. |
| **Azure File Sync** | &ast;.one.microsoft.com | Efter den inledande serverregistrering servern tar emot en regional URL för Azure File Sync-tjänstinstansen i den regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt med hantering av dess sync-instans. |

> [!Important]
> När så att trafik kan &ast;. one.microsoft.com, trafik till mer än bara synkroniseringstjänsten går från servern. Det finns många fler Microsoft-tjänster som är tillgängliga under underdomäner.

Om &ast;. one.microsoft.com är för breda, du kan begränsa serverns kommunikation genom att tillåta kommunikation till endast explicit regionala instanser av Azure Files Sync-tjänsten. Vilka instanser att välja beror på regionen som den lagringssynkroniseringstjänst som du har distribuerat och registrerat servern till. Regionen kallas ”primär slutpunkts-URL” i tabellen nedan.

För affärskontinuitet och disaster recovery (BCDR) orsaker har du angett din Azure-filresurser i ett globalt redundant lagringskonto (GRS). Om så är fallet, kommer sedan Azure-filresurser växlar över till den parade regionen i händelse av ett bestående regionalt strömavbrott. Azure File Sync använder samma regionala par som lagring. Så om du använder GRS-lagringskonton kan behöva du aktivera ytterligare URL: er så att din server kan kommunicera med den parade regionen för Azure File Sync. Tabellen nedan anropar den här ”Paired region”. Det finns också en traffic manager-URL som måste aktiveras också. Detta garanterar nätverkstrafik sömlöst igen dirigeras till den parade regionen i händelse av en redundansväxling och kallas ”identifiering URL” i tabellen nedan.

| Region | Primär slutpunkts-URL | Länkad region | Identifierings-URL | |---|---|| --------|| ---------------------------------------| | Östra Australien | https://kailani-aue.one.microsoft.com | Australien Souteast | https://kailani-aue.one.microsoft.com | | Australien, sydöstra | https://kailani-aus.one.microsoft.com | Östra Australien | https://tm-kailani-aus.one.microsoft.com | | Kanada, centrala | https://kailani-cac.one.microsoft.com | Kanada, östra | https://tm-kailani-cac.one.microsoft.com | | Kanada, östra | https://kailani-cae.one.microsoft.com | Kanada, centrala | https://tm-kailani.cae.one.microsoft.com | | USA, centrala | https://kailani-cus.one.microsoft.com | Östra USA 2 | https://tm-kailani-cus.one.microsoft.com | | Östasien | https://kailani11.one.microsoft.com | Sydostasien | https://tm-kailani11.one.microsoft.com | | Östra USA | https://kailani1.one.microsoft.com | Västra USA | https://tm-kailani1.one.microsoft.com | | Östra USA 2 | https://kailani-ess.one.microsoft.com | USA, centrala | https://tm-kailani-ess.one.microsoft.com | | Nordeuropa | https://kailani7.one.microsoft.com | Västeuropa | https://tm-kailani7.one.microsoft.com | | Sydostasien | https://kailani10.one.microsoft.com | Östasien | https://tm-kailani10.one.microsoft.com | | Storbritannien, södra | https://kailani-uks.one.microsoft.com | Storbritannien, västra | https://tm-kailani-uks.one.microsoft.com | | Storbritannien, västra | https://kailani-ukw.one.microsoft.com | Storbritannien, södra | https://tm-kailani-ukw.one.microsoft.com | | Västeuropa | https://kailani6.one.microsoft.com | Nordeuropa | https://tm-kailani6.one.microsoft.com | | Västra USA | https://kailani.one.microsoft.com | Östra USA | https://tm-kailani.one.microsoft.com |

- Om du använder lokalt redundant (LRS) eller zonen redundant (ZRS)-lagringskonton, behöver du bara aktivera den URL som visas under ”primär slutpunkts-URL”.

- Om du använder globalt redundant (GRS)-lagringskonton kan du aktivera tre URL: er.

**Exempel:** du distribuerar en tjänst för lagringssynkronisering i `"West US"` och registrera servern med den. URL: er så att servern kan kommunicera till för det här fallet är:

> - https://kailani.one.microsoft.com (primära slutpunkten: USA, västra)
> - https://kailani1.one.microsoft.com (parad region för redundans: östra USA)
> - https://tm-kailani.one.microsoft.com (identifierings-URL för den primära regionen)

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risk begränsning
Listorna tidigare i det här dokumentet innehåller URL: er med Azure File Sync för närvarande kommunicerar med. Brandväggar måste kunna tillåta trafik utgående trafik till dessa domäner. Microsoft strävar efter att den här listan har uppdaterats.

Konfigurera domänen att begränsa brandväggsregler kan vara ett mått för att förbättra säkerheten. Om dessa brandväggskonfigurationer används, måste en Tänk på att URL: er kommer att läggas till och även kan ändras över tid. Kontrollera den här artikeln regelbundet.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)