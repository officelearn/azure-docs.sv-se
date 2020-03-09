---
title: Azure File Sync inställningar för lokal brand vägg och proxy | Microsoft Docs
description: Azure File Sync lokal nätverks konfiguration
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcf6160c3650975431bf50fcf5bcba67f833a717
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381226"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Inställningar för Azure File Sync-proxy och brandväggar
Azure File Sync ansluter dina lokala servrar till Azure Files, vilket möjliggör synkronisering av flera platser och moln nivåer. En lokal server måste därför vara ansluten till Internet. En IT-administratör måste bestämma den bästa sökvägen för att servern ska kunna komma åt Azure Cloud Services.

I den här artikeln får du information om särskilda krav och alternativ som är tillgängliga för att ansluta servern till Azure File Sync på ett säkert sätt.

## <a name="overview"></a>Översikt
Azure File Sync fungerar som en Orchestration-tjänst mellan Windows Server, Azure-filresursen och flera andra Azure-tjänster för att synkronisera data som beskrivs i din Sync-grupp. För att Azure File Sync ska fungera korrekt måste du konfigurera servrarna så att de kommunicerar med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Authentication Services

> [!Note]  
> Azure File Sync agenten på Windows Server initierar alla förfrågningar till moln tjänster, vilket resulterar i att endast ta hänsyn till utgående trafik från ett brand Väggs perspektiv. <br /> Ingen Azure-tjänst initierar en anslutning till Azure File Sync agenten.

## <a name="ports"></a>Portar
Azure File Sync flyttar fildata och metadata exklusivt över HTTPS och kräver att port 443 är öppen för utgående data.
Det innebär att all trafik krypteras.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Azure File Sync agenten har inga krav avseende särskilda kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md)osv. till Azure.

Azure File Sync kommer att fungera på något sätt som ger till gång till Azure, automatisk anpassning till olika nätverks egenskaper, t. ex. bandbredd, latens och administrations kontroll för fin justering. Alla funktioner är inte tillgängliga för tillfället. Om du vill konfigurera ett speciellt beteende kan du meddela oss via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync har stöd för app-/regionsspecifika och datorövergripande proxyinställningar.

**Appar-specifika proxyinställningar** tillåter konfiguration av en proxy specifikt för Azure File Sync trafik. Programspecificerade proxyinställningar stöds på agent version 4.0.1.0 eller senare och kan konfigureras under Agent installationen eller med hjälp av cmdleten Set-StorageSyncProxyConfiguration PowerShell.

PowerShell-kommandon för att konfigurera app-Specific proxy-inställningar:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
De **datorövergripande proxyinställningarna** är transparenta för Azure File Sync agenten eftersom hela trafiken på servern dirigeras via proxyservern.

Följ stegen nedan om du vill konfigurera inställningar för datorövergripande proxy: 

1. Konfigurera proxyinställningar för .NET-program 

   - Redigera dessa två filer:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Lägg till avsnittet < system. net > i Machine. config-filerna (under avsnittet < system. serviceModel >).  Ändra 127.0.01:8888 till IP-adressen och porten för proxyservern. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ange inställningar för WinHTTP-proxy 

   - Kör följande kommando från en upphöjd kommando tolk eller PowerShell för att se den befintliga inställningen för proxy:   

     Netsh WinHTTP show proxy

   - Kör följande kommando från en upphöjd kommando tolk eller PowerShell för att ange proxyinställningar (Change 127.0.01:8888 till IP-adressen och porten för proxyservern):  

     Netsh WinHTTP set proxy 127.0.0.1:8888

3. Starta om tjänsten Storage Sync agent genom att köra följande kommando från en upphöjd kommando tolk eller PowerShell: 

      net stop-filesyncsvc

      Obs! tjänsten Storage Sync agent (filesyncsvc) startar automatiskt när den har stoppats.

## <a name="firewall"></a>Brandvägg
Som anges i föregående avsnitt måste port 443 vara öppen för utgående trafik. Baserat på principer i ditt data Center, din gren eller region, kan ytterligare begränsa trafik via den här porten till vissa domäner eller obligatoriskt.

I följande tabell beskrivs de domäner som krävs för kommunikation:

| Tjänst | Offentlig moln slut punkt | Azure Government slut punkt | Användning |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Alla användar anrop (som PowerShell) går till/via denna URL, inklusive det första server registrerings anropet. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Azure Resource Manager-anrop måste göras av en autentiserad användare. För att lyckas används den här URL: en för användarautentisering. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Som en del av distributionen Azure File Sync skapas ett huvud namn för tjänsten i prenumerationens Azure Active Directory. Den här URL: en används för det. Den här huvud gruppen används för att delegera en minimal uppsättning rättigheter till tjänsten Azure File Sync. Användaren som utför den första installationen av Azure File Sync måste vara en autentiserad användare med prenumerations ägarens behörigheter. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | När servern laddar ned en fil utför servern den data flyttningen mer effektivt när den pratar direkt till Azure-filresursen i lagrings kontot. Servern har en SAS-nyckel som endast tillåter åtkomst till riktad fil resurs. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Efter den första server registreringen får servern en regional URL för den Azure File Sync tjänst instansen i den regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt med den instans som hanterar synkroniseringen. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | När Azure File Sync-agenten har installerats används PKI-URL: en för att hämta mellanliggande certifikat som krävs för att kommunicera med Azure File Sync-tjänsten och Azure-filresursen. OCSP-URL: en används för att kontrol lera status för ett certifikat. |

> [!Important]
> När du tillåter trafik till &ast;. one.microsoft.com är det möjligt att trafik till mer än bara synkroniseringstjänsten är möjlig från-servern. Det finns många fler Microsoft-tjänster som är tillgängliga under under domäner.

Om &ast;. one.microsoft.com är för bred kan du begränsa serverns kommunikation genom att tillåta kommunikation enbart till explicita regionala instanser av Azure Files Sync-tjänsten. Vilken eller vilka instanser som ska väljas beror på den region i lagrings tjänsten för synkronisering som du har distribuerat och registrerat servern på. Regionen kallas "primär slut punkts-URL" i tabellen nedan.

För affärs kontinuitet och haveri beredskap (BCDR) kan du ha angett Azure-filresurser i ett globalt redundant (GRS) lagrings konto. Om så är fallet växlar Azure-filresurserna över till den kopplade regionen vid ett varaktigt regionalt avbrott. Azure File Sync använder samma regionala par som lagring. Så om du använder GRS lagrings konton måste du aktivera ytterligare URL: er så att servern kan kommunicera med den kopplade regionen för Azure File Sync. Tabellen nedan anropar denna "parad region". Det finns dessutom en Traffic Manager-profils-URL som måste aktive ras. Detta säkerställer att nätverks trafiken kan dirigeras sömlöst till den kopplade regionen i händelse av redundans och kallas "identifierings-URL" i tabellen nedan.

| Molnet  | Region | Primär slut punkts-URL | Länkad region | URL för identifiering |
|--------|--------|----------------------|---------------|---------------|
| Offentlig |Australien, östra | https:\//kailani-aue.one.microsoft.com | Australien, sydöstra | https:\//tm-kailani-aue.one.microsoft.com |
| Offentlig |Australien, sydöstra | https:\//kailani-aus.one.microsoft.com | Australien, östra | https:\//tm-kailani-aus.one.microsoft.com |
| Offentlig | Brasilien, södra | https:\//brazilsouth01.afs.azure.net | USA, södra centrala | https:\//tm-brazilsouth01.afs.azure.net |
| Offentlig | Kanada, centrala | https:\//kailani-cac.one.microsoft.com | Kanada, östra | https:\//tm-kailani-cac.one.microsoft.com |
| Offentlig | Kanada, östra | https:\//kailani-cae.one.microsoft.com | Kanada, centrala | https:\//tm-kailani.cae.one.microsoft.com |
| Offentlig | Indien, centrala | https:\//kailani-cin.one.microsoft.com | Indien, södra | https:\//tm-kailani-cin.one.microsoft.com |
| Offentlig | USA, centrala | https:\//kailani-cus.one.microsoft.com | USA, östra 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Offentlig | Asien, östra | https:\//kailani11.one.microsoft.com | Sydostasien | https:\//tm-kailani11.one.microsoft.com |
| Offentlig | USA, östra | https:\//kailani1.one.microsoft.com | USA, västra | https:\//tm-kailani1.one.microsoft.com |
| Offentlig | USA, östra 2 | https:\//kailani-ess.one.microsoft.com | USA, centrala | https:\//tm-kailani-ess.one.microsoft.com |
| Offentlig | Japan, östra | https:\//japaneast01.afs.azure.net | Japan, västra | https:\//tm-japaneast01.afs.azure.net |
| Offentlig | Japan, västra | https:\//japanwest01.afs.azure.net | Japan, östra | https:\//tm-japanwest01.afs.azure.net |
| Offentlig | Sydkorea, centrala | https:\//koreacentral01.afs.azure.net/ | Sydkorea, södra | https:\//tm-koreacentral01.afs.azure.net/ |
| Offentlig | Sydkorea, södra | https:\//koreasouth01.afs.azure.net/ | Sydkorea, centrala | https:\//tm-koreasouth01.afs.azure.net/ |
| Offentlig | USA, norra centrala | https:\//northcentralus01.afs.azure.net | USA, södra centrala | https:\//tm-northcentralus01.afs.azure.net |
| Offentlig | Europa, norra | https:\//kailani7.one.microsoft.com | Europa, västra | https:\//tm-kailani7.one.microsoft.com |
| Offentlig | USA, södra centrala | https:\//southcentralus01.afs.azure.net | USA, norra centrala | https:\//tm-southcentralus01.afs.azure.net |
| Offentlig | Indien, södra | https:\//kailani-sin.one.microsoft.com | Indien, centrala | https:\//tm-kailani-sin.one.microsoft.com |
| Offentlig | Sydostasien | https:\//kailani10.one.microsoft.com | Asien, östra | https:\//tm-kailani10.one.microsoft.com |
| Offentlig | Storbritannien, södra | https:\//kailani-uks.one.microsoft.com | Storbritannien, västra | https:\//tm-kailani-uks.one.microsoft.com |
| Offentlig | Storbritannien, västra | https:\//kailani-ukw.one.microsoft.com | Storbritannien, södra | https:\//tm-kailani-ukw.one.microsoft.com |
| Offentlig | USA, västra centrala | https:\//westcentralus01.afs.azure.net | USA, västra 2 | https:\//tm-westcentralus01.afs.azure.net |
| Offentlig | Europa, västra | https:\//kailani6.one.microsoft.com | Europa, norra | https:\//tm-kailani6.one.microsoft.com |
| Offentlig | USA, västra | https:\//kailani.one.microsoft.com | USA, östra | https:\//tm-kailani.one.microsoft.com |
| Offentlig | USA, västra 2 | https:\//westus201.afs.azure.net | USA, västra centrala | https:\//tm-westus201.afs.azure.net |
| Government | US Gov, Arizona | https:\//usgovarizona01.afs.azure.us | US Gov, Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Government | US Gov, Texas | https:\//usgovtexas01.afs.azure.us | US Gov, Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Om du använder lokalt redundant (LRS) eller zon redundant lagrings konto (ZRS) behöver du bara aktivera URL: en som anges under "primär slut punkts-URL".

- Om du använder globalt redundanta lagrings konton (GRS) aktiverar du tre URL: er.

**Exempel:** Du distribuerar en tjänst för synkronisering av lagring i `"West US"` och registrerar servern med den. URL: erna som gör att servern kan kommunicera till för det här fallet är:

> - https:\//kailani.one.microsoft.com (primär slut punkt: USA, västra)
> - https:\//kailani1.one.microsoft.com (parad failover-över region: USA, östra)
> - https:\//tm-kailani.one.microsoft.com (identifierings-URL för den primära regionen)

## <a name="test-network-connectivity-to-service-endpoints"></a>Testa nätverks anslutningen till tjänstens slut punkter
När en server har registrerats med den Azure File Sync tjänsten kan test-StorageSyncNetworkConnectivity-cmdleten och ServerRegistration. exe användas för att testa kommunikationen med alla URL: er som är speciella för den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och kan användas för att finjustera konfigurationer för proxy och brand väggar.

Om du vill köra testet för nätverks anslutning installerar du Azure File Sync agent version 9,1 eller senare och kör följande PowerShell-kommandon:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risk begränsning
Listorna ovan i det här dokumentet innehåller de URL: er Azure File Sync som för närvarande kommunicerar med. Brand väggar måste kunna tillåta utgående trafik till dessa domäner. Microsoft strävar efter att hålla listan uppdaterad.

Det kan vara ett mått för att förbättra säkerheten för att konfigurera domän begränsning av brand Väggs regler. Om dessa brand Väggs konfigurationer används måste en vara i åtanke att URL: er läggs till och kan ändras med tiden. Läs den här artikeln regelbundet.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
