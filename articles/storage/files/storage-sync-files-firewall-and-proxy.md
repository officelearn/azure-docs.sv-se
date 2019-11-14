---
title: Azure File Sync inställningar för lokal brand vägg och proxy | Microsoft Docs
description: Azure File Sync lokal nätverks konfiguration
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4f37c54699329f43a5bbdd5c4543ae3a7b2166f5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048834"
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
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | När servern laddar ned en fil utför servern den data flyttningen mer effektivt när den pratar direkt till Azure-filresursen i lagrings kontot. Servern har en SAS-nyckel som endast tillåter åtkomst till riktad fil resurs. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;. afs.azure.net | &ast;.afs.azure.us | Efter den första server registreringen får servern en regional URL för den Azure File Sync tjänst instansen i den regionen. Servern kan använda URL: en för att kommunicera direkt och effektivt med den instans som hanterar synkroniseringen. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | När Azure File Sync-agenten har installerats används PKI-URL: en för att hämta mellanliggande certifikat som krävs för att kommunicera med Azure File Sync-tjänsten och Azure-filresursen. OCSP-URL: en används för att kontrol lera status för ett certifikat. |

> [!Important]
> När du tillåter trafik till &ast;. one.microsoft.com är det möjligt att trafik till mer än bara synkroniseringstjänsten är möjlig från-servern. Det finns många fler Microsoft-tjänster som är tillgängliga under under domäner.

Om &ast;. one.microsoft.com är för bred kan du begränsa serverns kommunikation genom att tillåta kommunikation enbart till explicita regionala instanser av Azure Files Sync-tjänsten. Vilken eller vilka instanser som ska väljas beror på den region i lagrings tjänsten för synkronisering som du har distribuerat och registrerat servern på. Regionen kallas "primär slut punkts-URL" i tabellen nedan.

För affärs kontinuitet och haveri beredskap (BCDR) kan du ha angett Azure-filresurser i ett globalt redundant (GRS) lagrings konto. Om så är fallet växlar Azure-filresurserna över till den kopplade regionen vid ett varaktigt regionalt avbrott. Azure File Sync använder samma regionala par som lagring. Så om du använder GRS lagrings konton måste du aktivera ytterligare URL: er så att servern kan kommunicera med den kopplade regionen för Azure File Sync. Tabellen nedan anropar denna "parad region". Det finns dessutom en Traffic Manager-profils-URL som måste aktive ras. Detta säkerställer att nätverks trafiken kan dirigeras sömlöst till den kopplade regionen i händelse av redundans och kallas "identifierings-URL" i tabellen nedan.

| Molnet  | Region | Primär slut punkts-URL | Länkad region | URL för identifiering |
|--------|--------|----------------------|---------------|---------------|
| Offentligt |Östra Australien | https:\//kailani-aue.one.microsoft.com | Sydöstra Australien | https:\//tm-kailani-aue.one.microsoft.com |
| Offentligt |Sydöstra Australien | https:\//kailani-aus.one.microsoft.com | Östra Australien | https:\//tm-kailani-aus.one.microsoft.com |
| Offentligt | Södra Brasilien | https:\//brazilsouth01.afs.azure.net | Södra centrala USA | https:\//tm-brazilsouth01.afs.azure.net |
| Offentligt | Centrala Kanada | https:\//kailani-cac.one.microsoft.com | Östra Kanada | https:\//tm-kailani-cac.one.microsoft.com |
| Offentligt | Östra Kanada | https:\//kailani-cae.one.microsoft.com | Centrala Kanada | https:\//tm-kailani.cae.one.microsoft.com |
| Offentligt | Indien, centrala | https:\//kailani-cin.one.microsoft.com | Södra Indien | https:\//tm-kailani-cin.one.microsoft.com |
| Offentligt | Centrala USA | https:\//kailani-cus.one.microsoft.com | USA, östra 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Offentligt | Östasien | https:\//kailani11.one.microsoft.com | Sydostasien | https:\//tm-kailani11.one.microsoft.com |
| Offentligt | Östra USA | https:\//kailani1.one.microsoft.com | Västra USA | https:\//tm-kailani1.one.microsoft.com |
| Offentligt | USA, östra 2 | https:\//kailani-ess.one.microsoft.com | Centrala USA | https:\//tm-kailani-ess.one.microsoft.com |
| Offentligt | Östra Japan | https:\//japaneast01.afs.azure.net | Västra Japan | https:\//tm-japaneast01.afs.azure.net |
| Offentligt | Västra Japan | https:\//japanwest01.afs.azure.net | Östra Japan | https:\//tm-japanwest01.afs.azure.net |
| Offentligt | Sydkorea, centrala | https:\//koreacentral01.afs.azure.net/ | Sydkorea, södra | https:\//tm-koreacentral01.afs.azure.net/ |
| Offentligt | Sydkorea, södra | https:\//koreasouth01.afs.azure.net/ | Sydkorea, centrala | https:\//tm-koreasouth01.afs.azure.net/ |
| Offentligt | Norra centrala USA | https:\//northcentralus01.afs.azure.net | Södra centrala USA | https:\//tm-northcentralus01.afs.azure.net |
| Offentligt | Norra Europa | https:\//kailani7.one.microsoft.com | Västra Europa | https:\//tm-kailani7.one.microsoft.com |
| Offentligt | Södra centrala USA | https:\//southcentralus01.afs.azure.net | Norra centrala USA | https:\//tm-southcentralus01.afs.azure.net |
| Offentligt | Södra Indien | https:\//kailani-sin.one.microsoft.com | Indien, centrala | https:\//tm-kailani-sin.one.microsoft.com |
| Offentligt | Sydostasien | https:\//kailani10.one.microsoft.com | Östasien | https:\//tm-kailani10.one.microsoft.com |
| Offentligt | Storbritannien, södra | https:\//kailani-uks.one.microsoft.com | Storbritannien, västra | https:\//tm-kailani-uks.one.microsoft.com |
| Offentligt | Storbritannien, västra | https:\//kailani-ukw.one.microsoft.com | Storbritannien, södra | https:\//tm-kailani-ukw.one.microsoft.com |
| Offentligt | Västra centrala USA | https:\//westcentralus01.afs.azure.net | Västra USA 2 | https:\//tm-westcentralus01.afs.azure.net |
| Offentligt | Västra Europa | https:\//kailani6.one.microsoft.com | Norra Europa | https:\//tm-kailani6.one.microsoft.com |
| Offentligt | Västra USA | https:\//kailani.one.microsoft.com | Östra USA | https:\//tm-kailani.one.microsoft.com |
| Offentligt | Västra USA 2 | https:\//westus201.afs.azure.net | Västra centrala USA | https:\//tm-westus201.afs.azure.net |
| Government | Arizona (USA-förvaltad region) | https:\//usgovarizona01.afs.azure.us | Texas (USA-förvaltad region) | https:\//tm-usgovarizona01.afs.azure.us |
| Government | Texas (USA-förvaltad region) | https:\//usgovtexas01.afs.azure.us | Arizona (USA-förvaltad region) | https:\//tm-usgovtexas01.afs.azure.us |

- Om du använder lokalt redundant (LRS) eller zon redundant lagrings konto (ZRS) behöver du bara aktivera URL: en som anges under "primär slut punkts-URL".

- Om du använder globalt redundanta lagrings konton (GRS) aktiverar du tre URL: er.

**Exempel:** Du distribuerar en tjänst för synkronisering av lagring i `"West US"` och registrerar servern med den. URL: erna som gör att servern kan kommunicera till för det här fallet är:

> - https:\//kailani.one.microsoft.com (primär slut punkt: USA, västra)
> - https:\//kailani1.one.microsoft.com (parad failover-över region: USA, östra)
> - https:\//tm-kailani.one.microsoft.com (identifierings-URL för den primära regionen)

## <a name="summary-and-risk-limitation"></a>Sammanfattning och risk begränsning
Listorna ovan i det här dokumentet innehåller de URL: er Azure File Sync som för närvarande kommunicerar med. Brand väggar måste kunna tillåta utgående trafik till dessa domäner. Microsoft strävar efter att hålla listan uppdaterad.

Det kan vara ett mått för att förbättra säkerheten för att konfigurera domän begränsning av brand Väggs regler. Om dessa brand Väggs konfigurationer används måste en vara i åtanke att URL: er läggs till och kan ändras med tiden. Läs den här artikeln regelbundet.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
