---
title: Lokala brandväggs- och proxyinställningar | Azure File Sync | Microsoft-dokument
description: Azure File Sync lokalt nätverk konfiguration
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294529"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Inställningar för Azure File Sync-proxy och brandväggar
Azure File Sync ansluter dina lokala servrar till Azure Files, vilket möjliggör funktioner för synkronisering på flera platser och molnnivådelning. Därför måste en lokal server vara ansluten till internet. En IT-administratör måste bestämma den bästa sökvägen för servern att nå in i Azure-molntjänster.

Den här artikeln ger insikt i specifika krav och alternativ som är tillgängliga för att ansluta servern till Azure File Sync på ett säkert sätt.

## <a name="overview"></a>Översikt
Azure File Sync fungerar som en orchestration-tjänst mellan din Windows Server, din Azure-filresurs och flera andra Azure-tjänster för att synkronisera data enligt beskrivningen i din synkroniseringsgrupp. För att Azure File Sync ska fungera korrekt måste du konfigurera dina servrar så att de kommunicerar med följande Azure-tjänster:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Autentiseringstjänster

> [!Note]  
> Azure File Sync-agenten på Windows Server initierar alla begäranden till molntjänster som resulterar i att endast behöva överväga utgående trafik från ett brandväggsperspektiv. <br /> Ingen Azure-tjänst initierar en anslutning till Azure File Sync-agenten.

## <a name="ports"></a>Portar
Azure File Sync flyttar fildata och metadata exklusivt via HTTPS och kräver port 443 för att vara öppen utgående.
Som ett resultat av detta krypteras all trafik.

## <a name="networks-and-special-connections-to-azure"></a>Nätverk och särskilda anslutningar till Azure
Azure File Sync-agenten har inga krav på speciella kanaler som [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. till Azure.

Azure File Sync kommer att arbeta på alla tillgängliga sätt som gör att nå in i Azure, automatiskt anpassa sig till olika nätverksegenskaper som bandbredd, svarstid samt erbjuda administratörskontroll för finjustering. Alla funktioner är inte tillgängliga för tillfället. Om du vill konfigurera specifikt beteende meddelar du oss via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync stöder appspecifika och datoromfattande proxyinställningar.

**Appspecifika proxyinställningar** tillåter konfiguration av en proxy specifikt för Azure File Sync-trafik. Appspecifika proxyinställningar stöds på agentversion 4.0.1.0 eller nyare och kan konfigureras under agentinstallationen eller med hjälp av PowerShell-cmdleten Set-StorageSyncProxyConfiguration.

PowerShell-kommandon för att konfigurera appspecifika proxyinställningar:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Proxyinställningar för hela datorn** är transparenta för Azure File Sync-agenten eftersom hela serverns trafik dirigeras via proxyn.

Så här konfigurerar du proxyinställningar för hela datorn: 

1. Konfigurera proxyinställningar för .NET-program 

   - Redigera dessa två filer:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Lägg till avsnittet <system.net> i machine.config-filerna (under avsnittet <system.serviceModel>).  Ändra 127.0.01:8888 till IP-adressen och porten för proxyservern. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ange proxyinställningarna för WinHTTP 

   - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att se den befintliga proxyinställningen:   

     netsh winhttp visa proxy

   - Kör följande kommando från en upphöjd kommandotolk eller PowerShell för att ställa in proxyinställningen (ändra 127.0.01:8888 till IP-adressen och porten för proxyservern):  

     netsh-vinsthttp uppsättning proxy 127.0.0.1:8888

3. Starta om tjänsten Storage Sync Agent genom att köra följande kommando från en upphöjd kommandotolk eller PowerShell: 

      net stop filesyncsvc net stop filesyncsvc net stop filesyncsvc net stop

      Tjänsten Storage Sync Agent (filesyncsvc) startar automatiskt när den har stoppats.

## <a name="firewall"></a>Brandvägg
Som nämnts i ett tidigare avsnitt måste port 443 vara öppen utgående. Baserat på principer i ditt datacenter, gren eller region kan ytterligare begränsa trafiken över den här porten till specifika domäner önskas eller krävas.

I följande tabell beskrivs de domäner som krävs för kommunikation:

| Tjänst | Slutpunkt för offentligt moln | Slutpunkten för Azure Government | Användning |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Alla användarsamtal (som PowerShell) går till/via den här webbadressen, inklusive det första serverregistreringsanropet. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-anrop måste göras av en autentrad användare. För att lyckas används den här URL:en för användarautentisering. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Som en del av distributionen av Azure File Sync skapas ett tjänsthuvudnamn i prenumerationens Azure Active Directory. Den här webbadressen används för det. Det här huvudbeloppet används för att delegera en minimal uppsättning rättigheter till Azure File Sync-tjänsten. Användaren som utför den första installationen av Azure File Sync måste vara en autentiserad användare med prenumerationsägarebehörighet. |
| **Azure-lagring** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | När servern hämtar en fil utför servern den dataförflyttningen mer effektivt när du talar direkt med Azure-filresursen i lagringskontot. Servern har en SAS-nyckel som endast tillåter riktad filresursåtkomst. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Efter den första serverregistreringen får servern en regional URL för Azure File Sync-tjänstinstansen i den regionen. Servern kan använda URL:en för att kommunicera direkt och effektivt med den instans som hanterar synkroniseringen. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | När Azure File Sync-agenten har installerats används PKI-URL:en för att hämta mellanliggande certifikat som krävs för att kommunicera med Azure File Sync-tjänsten och Azure-filresursen. OCSP-URL:en används för att kontrollera status för ett certifikat. |

> [!Important]
> När trafik &ast;tillåts one.microsoft.com är det möjligt att trafikera till mer än bara synkroniseringstjänsten från servern. Det finns många fler Microsoft-tjänster tillgängliga under underdomäner.

Om &ast;.one.microsoft.com är för brett kan du begränsa serverns kommunikation genom att tillåta kommunikation till endast explicita regionala instanser av Azure Files Sync-tjänsten. Vilka instanser som ska väljas beror på vilken region för lagringssynkroniseringstjänsten som du har distribuerat och registrerat servern till. Den regionen kallas "Url för primär slutpunkt" i tabellen nedan.

För affärskontinuitet och bcdr-orsaker (business continuity och disaster recovery) kan du ha angett dina Azure-filresurser i ett globalt redundant (GRS) lagringskonto. Om så är fallet kommer dina Azure-filresurser att växla över till den parade regionen i händelse av ett varaktigt regionalt avbrott. Azure File Sync använder samma regionala parningar som lagring. Så om du använder GRS-lagringskonton måste du aktivera ytterligare webbadresser så att servern kan prata med den parkopplade regionen för Azure File Sync. Tabellen nedan kallar detta "Parkopplad region". Dessutom finns det en traffic manager-profil-URL som också måste aktiveras. Detta säkerställer att nätverkstrafiken sömlöst kan omdirigeras till den parade regionen i händelse av en överlämning och kallas "Discovery URL" i tabellen nedan.

| Molnet  | Region | URL för primär slutpunkt | Länkad region | Url för identifiering |
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

- Om du använder lokalt redundanta (LRS) eller zonundant (ZRS) lagringskonton behöver du bara aktivera webbadressen som anges under "Primär slutpunkts-URL".

- Om du använder globalt redundanta (GRS) lagringskonton aktiverar du tre webbadresser.

**Exempel:** Du distribuerar en `"West US"` lagringssynkroniseringstjänst i och registrerar servern med den. Webbadresserna som servern kan kommunicera med i det här fallet är:

> - https:\//kailani.one.microsoft.com (primärt effektmått: västra USA)
> - https:\//kailani1.one.microsoft.com (parat fail-over region: Östra USA)
> - https:\//tm-kailani.one.microsoft.com (identifierings-URL för den primära regionen)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Tillåt lista för IP-adresser för Azure File Sync
Azure File Sync stöder användningen av [tjänsttaggar](../../virtual-network/service-tags-overview.md), som representerar en grupp IP-adressprefix för en viss Azure-tjänst. Du kan använda tjänsttaggar för att skapa brandväggsregler som möjliggör kommunikation med Azure File Sync-tjänsten. Tjänsttag för Azure File `StorageSyncService`Sync är .

Om du använder Azure File Sync i Azure kan du använda namnet på tjänsttaggen direkt i nätverkssäkerhetsgruppen för att tillåta trafik. Mer information om hur du gör detta finns i [Nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Om du använder Azure File Sync lokalt kan du använda API:et för servicetag för att hämta specifika IP-adressintervall för brandväggens tillåt-lista. Det finns två metoder för att få den här informationen:

- Den aktuella listan över IP-adressintervall för alla Azure-tjänster som stöder tjänsttaggar publiceras varje vecka i Microsoft Download Center i form av ett JSON-dokument. Varje Azure-moln har sitt eget JSON-dokument med IP-adressintervallen som är relevanta för molnet:
    - [Azure, offentlig](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure amerikanska regeringen](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
- Api för identifiering av tjänsttagg (förhandsversion) gör det möjligt att hämta den aktuella listan över tjänsttaggar. I förhandsversionen kan API:et för identifiering av tjänsttagg returnerar information som är mindre aktuell än information som returneras från JSON-dokumenten som publicerats i Microsoft Download Center. Du kan använda API-ytan baserat på dina automatiseringsinställningar:
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Eftersom API:et för identifiering av tjänsttagg inte uppdateras lika ofta som JSON-dokumenten som publiceras i Microsoft Download Center rekommenderar vi att du använder JSON-dokumentet för att uppdatera den lokala brandväggens tillåt-lista. Detta kan göras på följande sätt:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Du kan sedan använda IP-adressintervallen i `$ipAddressRanges` för att uppdatera brandväggen. Kontrollera brandväggens/nätverksinstallationens webbplats om du vill ha information om hur du uppdaterar brandväggen.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testa nätverksanslutningen till tjänstslutpunkter
När en server har registrerats med Azure File Sync-tjänsten kan cmdlet och ServerRegistration.exe användas för att testa kommunikation med alla slutpunkter (URL:er) som är specifika för den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och den kan användas för att finjustera proxy- och brandväggskonfigurationer.

Om du vill köra nätverksanslutningstestet installerar du Azure File Sync agent version 9.1 eller senare och kör följande PowerShell-kommandon:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Sammanfattning och riskbegränsning
Listorna tidigare i det här dokumentet innehåller webbadresserna som Azure File Sync för närvarande kommunicerar med. Brandväggar måste kunna tillåta trafik utgående till dessa domäner. Microsoft strävar efter att hålla listan uppdaterad.

Att ställa in domänbegränsning brandväggsregler kan vara en åtgärd för att förbättra säkerheten. Om dessa brandväggskonfigurationer används måste man komma ihåg att webbadresser läggs till och till och med ändras med tiden. Kontrollera den här artikeln med jämna mellanrum.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
