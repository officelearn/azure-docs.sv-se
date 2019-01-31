---
title: Azure File Sync lokala brandvägg och proxyinställningar | Microsoft Docs
description: Azure File Sync lokala nätverkskonfiguration
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6c8a0a0db419546a49670b1f34e58ead7cebeaa1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459510"
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

**Appspecifika proxyinställningar** tillåter konfiguration av proxy specifikt för Azure File Sync-trafik. Appspecifika proxyinställningar stöds på agentversion 4.0.1.0 eller senare och kan konfigureras under agentinstallationen av eller med hjälp av cmdleten Set-StorageSyncProxyConfiguration PowerShell.

PowerShell-kommandon för att konfigurera proxyinställningar för appspecifika:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Datoromfattande proxy-inställningar** är transparent för Azure File Sync-agenten som hela trafiken på servern dirigeras via proxy.

Följ stegen nedan om du vill konfigurera datoromfattande proxy-inställningar: 

1. Konfigurera proxyinställningar för .NET-program 

  - Redigera dessa två filer:  
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
    C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

  - Lägg till avsnittet < system.net > i machine.config-filer (under avsnittet < system.serviceModel >).  Ändra 127.0.01:8888 till IP-adressen och porten för proxyservern. 
  ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
  ```

2. Ange WinHTTP-proxyinställningar 

  - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att visa befintliga Proxyinställningen:   

    Netsh winhttp show proxy

  - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att ange Proxyinställningen (ändra 127.0.01:8888 till IP-adressen och porten för proxyservern):  

    Netsh winhttp ange proxy 127.0.0.1:8888

3. Starta om tjänsten Storage Sync-agenten genom att köra följande kommando från en upphöjd kommandotolk eller PowerShell: 

      net stop filesyncsvc

      Obs! Storage Sync-agenten (filesyncsvc)-tjänsten ska starta automatiskt när den stoppats.

## <a name="firewall"></a>Brandvägg
Port 443 måste vara öppna utgående som nämns i föregående avsnitt. Baserat på principer i ditt datacenter, gren eller din region kan kan ytterligare begränsa trafik via den här porten till vissa domäner vara önskade eller krävs.

I följande tabell beskrivs domänerna som krävs för kommunikation:

| Tjänst | Domain | Användning |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Ett anrop för användaren (till exempel PowerShell) går till/igenom den här URL: en, inklusive inledande server registrering anropet. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager-anrop måste göras av en autentiserad användare. Ska lyckas, används den här URL: en för autentisering av användare. |
| **Azure Active Directory** | https://graph.windows.net/ | Som en del av att distribuera Azure File Sync, skapas ett huvudnamn för tjänsten i prenumerationens Azure Active Directory. Den här URL: en används för detta. Den här huvudnamn används för att delegera en minimal uppsättning rättigheter till Azure File Sync-tjänsten. Användaren som utför installationen av Azure File Sync måste vara en autentiserad användare med behörighet för ägare av prenumerationen. |
| **Azure Storage** | &ast;.core.windows.net | När servern hämtar en fil, utför servern sedan den dataförflyttning mer effektivt när man talar direkt till Azure-filresursen i Lagringskontot. Servern har en SAS-nyckel som endast tillåter för åtkomst till resursen för filen. |
| **Azure File Sync** | &ast;.one.microsoft.com | Efter den inledande serverregistrering servern tar emot en regional URL för Azure File Sync-tjänstinstansen i den regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt med hantering av dess sync-instans. |
| **Microsoft PKI** | http://ocsp.msocsp.com | När Azure File Sync-agenten har installerats, används PKI-URL: en för att ladda ned mellanliggande certifikat som krävs för att kommunicera med Azure File Sync-tjänsten och Azure-filresurs. OCSP-URL: en används för att kontrollera status för ett certifikat. |

> [!Important]
> När så att trafik kan &ast;. one.microsoft.com, trafik till mer än bara synkroniseringstjänsten går från servern. Det finns många fler Microsoft-tjänster som är tillgängliga under underdomäner.

Om &ast;. one.microsoft.com är för breda, du kan begränsa serverns kommunikation genom att tillåta kommunikation till endast explicit regionala instanser av Azure Files Sync-tjänsten. Vilka instanser att välja beror på regionen som den lagringssynkroniseringstjänst som du har distribuerat och registrerat servern till. Regionen kallas ”primär slutpunkts-URL” i tabellen nedan.

För affärskontinuitet och disaster recovery (BCDR) orsaker har du angett din Azure-filresurser i ett globalt redundant lagringskonto (GRS). Om så är fallet, kommer sedan Azure-filresurser växlar över till den parade regionen i händelse av ett bestående regionalt strömavbrott. Azure File Sync använder samma regionala par som lagring. Så om du använder GRS-lagringskonton kan behöva du aktivera ytterligare URL: er så att din server kan kommunicera med den parade regionen för Azure File Sync. Tabellen nedan anropar den här ”Paired region”. Det finns också en traffic manager-URL som måste aktiveras också. Detta garanterar nätverkstrafik sömlöst igen dirigeras till den parade regionen i händelse av en redundansväxling och kallas ”identifiering URL” i tabellen nedan.

| Region | Primär slutpunkts-URL | Länkad region | Identifierings-URL |
|--------|---------------------------------------|--------|---------------------------------------|
| Östra Australien | https://kailani-aue.one.microsoft.com | Australien Souteast | https://kailani-aue.one.microsoft.com |
| Sydöstra Australien | https://kailani-aus.one.microsoft.com | Östra Australien | https://tm-kailani-aus.one.microsoft.com |
| Centrala Kanada | https://kailani-cac.one.microsoft.com | Östra Kanada | https://tm-kailani-cac.one.microsoft.com |
| Östra Kanada | https://kailani-cae.one.microsoft.com | Centrala Kanada | https://tm-kailani.cae.one.microsoft.com |
| Centrala USA | https://kailani-cus.one.microsoft.com | USA, östra 2 | https://tm-kailani-cus.one.microsoft.com |
| Östasien | https://kailani11.one.microsoft.com | Sydostasien | https://tm-kailani11.one.microsoft.com |
| Östra USA | https://kailani1.one.microsoft.com | Västra USA | https://tm-kailani1.one.microsoft.com |
| USA, östra 2 | https://kailani-ess.one.microsoft.com | Centrala USA | https://tm-kailani-ess.one.microsoft.com |
| Norra Europa | https://kailani7.one.microsoft.com | Västra Europa | https://tm-kailani7.one.microsoft.com |
| Sydostasien | https://kailani10.one.microsoft.com | Östasien | https://tm-kailani10.one.microsoft.com |
| Storbritannien, södra | https://kailani-uks.one.microsoft.com | Storbritannien, västra | https://tm-kailani-uks.one.microsoft.com |
| Storbritannien, västra | https://kailani-ukw.one.microsoft.com | Storbritannien, södra | https://tm-kailani-ukw.one.microsoft.com |
| Västra Europa | https://kailani6.one.microsoft.com | Norra Europa | https://tm-kailani6.one.microsoft.com |
| Västra USA | https://kailani.one.microsoft.com | Östra USA | https://tm-kailani.one.microsoft.com |

- Om du använder lokalt redundant (LRS) eller zonen redundant (ZRS)-lagringskonton, behöver du bara aktivera den URL som visas under ”primär slutpunkts-URL”.

- Om du använder globalt redundant (GRS)-lagringskonton kan du aktivera tre URL: er.

**Exempel:** Du distribuerar en tjänst för lagringssynkronisering i `"West US"` och registrera servern med den. URL: er så att servern kan kommunicera till för det här fallet är:

> - https://kailani.one.microsoft.com (primära slutpunkten: Västra USA)
> - https://kailani1.one.microsoft.com (parad region för redundans: Östra USA)
> - https://tm-kailani.one.microsoft.com (identifierings-URL för den primära regionen)

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risk begränsning
Listorna tidigare i det här dokumentet innehåller URL: er med Azure File Sync för närvarande kommunicerar med. Brandväggar måste kunna tillåta trafik utgående trafik till dessa domäner. Microsoft strävar efter att den här listan har uppdaterats.

Konfigurera domänen att begränsa brandväggsregler kan vara ett mått för att förbättra säkerheten. Om dessa brandväggskonfigurationer används, måste en Tänk på att URL: er kommer att läggas till och även kan ändras över tid. Kontrollera den här artikeln regelbundet.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
