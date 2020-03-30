---
title: Konfigurera DNS-vidarebefordran för Azure-filer | Microsoft-dokument
description: En översikt över nätverksalternativ för Azure-filer.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 35dfbcb274721049f2160719222ca89038c93356
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082502"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurera DNS-vidarebefordran för Azure-filer
Med Azure Files kan du skapa privata slutpunkter för lagringskonton som innehåller dina filresurser. Även om det är användbart för många olika program är privata slutpunkter särskilt användbara för att ansluta till dina Azure-filresurser från ditt lokala nätverk med hjälp av en VPN- eller ExpressRoute-anslutning med hjälp av privat peering. 

För att anslutningarna till ditt lagringskonto ska gå över nätverkstunneln måste det fullständigt kvalificerade domännamnet (FQDN) för ditt lagringskonto matchas till den privata slutpunktens privata IP-adress. För att uppnå detta måste du vidarebefordra suffixet för lagringsslutpunkter (för`core.windows.net` offentliga molnregioner) till azure-privata DNS-tjänsten som är tillgänglig från ditt virtuella nätverk. Den här guiden visar hur du konfigurerar och konfigurerar DNS-vidarebefordran för att matcha till ditt lagringskontos privata slutpunkts-IP-adress.

Vi rekommenderar starkt att du läser [Planering för en Azure Files-distribution](storage-files-planning.md) och Azure [Files-nätverksöverväganden](storage-files-networking-overview.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="overview"></a>Översikt
Azure Files innehåller två huvudtyper av slutpunkter för åtkomst till Azure-filresurser: 
- Offentliga slutpunkter, som har en offentlig IP-adress och kan nås från var som helst i världen.
- Privata slutpunkter, som finns i ett virtuellt nätverk och har en privat IP-adress inifrån adressutrymmet i det virtuella nätverket.

Offentliga och privata slutpunkter finns på Azure-lagringskontot. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer.

Varje lagringskonto har ett fullständigt kvalificerat domännamn (FQDN). För de offentliga molnområdena följer detta FQDN mönstret `storageaccount.file.core.windows.net` där `storageaccount` är namnet på lagringskontot. När du gör förfrågningar mot det här namnet, till exempel att montera resursen på din arbetsstation med SMB, utför operativsystemet en DNS-sökning för att matcha det fullständigt kvalificerade domännamnet till en IP-adress som det kan använda för att skicka SMB-begäranden till.

Som standard `storageaccount.file.core.windows.net` matchas till den offentliga slutpunktens IP-adress. Den offentliga slutpunkten för ett lagringskonto finns på ett Azure-lagringskluster som är värd för många andra lagringskontons offentliga slutpunkter. När du skapar en privat slutpunkt länkas en privat DNS-zon till det virtuella `storageaccount.file.core.windows.net` nätverk som den lades till i, med en CNAME-postmappning i en A-postpost för den privata IP-adressen för lagringskontots privata slutpunkt. På så sätt `storageaccount.file.core.windows.net` kan du använda FQDN i det virtuella nätverket och få den att lösa till den privata slutpunktens IP-adress.

Eftersom vårt slutmål är att komma åt Azure-filresurserna som finns i lagringskontot från lokala med hjälp av en nätverkstunnel, till exempel en VPN- eller ExpressRoute-anslutning, måste du konfigurera dina lokala DNS-servrar för att vidarebefordra begäranden som görs till Azure Filtjänst till Azure private DNS-tjänsten. För att åstadkomma detta måste du ställa `*.core.windows.net` in *villkorlig vidarebefordran* av (eller lämpligt lagringsslutpunktssuffix för den amerikanska regeringen, Tyskland eller Kina nationella moln) till en DNS-server som finns i ditt virtuella Azure-nätverk. Den här DNS-servern vidarebefordrar sedan begäran rekursivt till Azures privata DNS-tjänst som löser det fullständigt kvalificerade domännamnet för lagringskontot till lämplig privat IP-adress.

Konfigurera DNS-vidarebefordran för Azure-filer kräver att du kör en virtuell dator för att vara värd för en DNS-server för att vidarebefordra begäranden, men det här är ett engångssteg för alla Azure-filresurser som finns i ditt virtuella nätverk. Dessutom är detta inte ett exklusivt krav på Azure Files - alla Azure-tjänster som stöder privata slutpunkter som du vill komma åt från lokala kan använda den DNS-vidarebefordran som du konfigurerar i den här guiden: Azure Blob storage, SQL Azure, Cosmos DB, Etc. 

Den här guiden visar stegen för att konfigurera DNS-vidarebefordran för slutpunkten för Azure-lagring, så förutom Azure-filer kommer DNS-namnmatchningsbegäranden för alla andra Azure-lagringstjänster (Azure Blob-lagring, Azure Table Storage, Azure Queue Storage, etc.) att skickas till Azures privata DNS-tjänst. Ytterligare slutpunkter för andra Azure-tjänster kan också läggas till om så önskas. DNS-vidarebefordran tillbaka till dina lokala DNS-servrar konfigureras också, vilket gör att molnresurser inom det virtuella nätverket (till exempel en DFS-N-server) kan lösa lokala datornamn. 

## <a name="prerequisites"></a>Krav
Innan du kan konfigurera DNS-vidarebefordran till Azure-filer måste du ha slutfört följande steg:

- Ett lagringskonto som innehåller en Azure-filresurs som du vill montera. Mer information om hur du skapar ett lagringskonto och en Azure-filresurs finns i [Skapa en Azure-filresurs](storage-how-to-create-file-share.md).
- En privat slutpunkt för lagringskontot. Mer information om hur du skapar en privat slutpunkt för Azure-filer finns i [Skapa en privat slutpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint).
- Den [senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps) av Azure PowerShell-modulen.

> [!Important]  
> Den här guiden förutsätter att du använder DNS-servern i Windows Server i din lokala miljö. Alla steg som beskrivs i den här guiden är möjliga med alla DNS-servrar, inte bara Windows DNS Server.

## <a name="manually-configuring-dns-forwarding"></a>Konfigurera DNS-vidarebefordran manuellt
Om du redan har DNS-servrar på plats i ditt virtuella Azure-nätverk, eller om du helt enkelt föredrar att distribuera dina egna virtuella datorer till DNS-servrar oavsett vilken metod din organisation använder, kan du konfigurera DNS manuellt med den inbyggda DNS-servern PowerShell-cmdlets.

Skapa en villkorlig vidarebefordrare på dina lokala `Add-DnsServerConditionalForwarderZone`DNS-servrar med . Den här villkorliga vidarebefordraren måste distribueras på alla dina lokala DNS-servrar för att vara effektiv när trafik vidarebefordras till Azure på rätt sätt. Kom ihåg `<azure-dns-server-ip>` att ersätta med lämpliga IP-adresser för din miljö.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

På DNS-servrarna i ditt virtuella Azure-nätverk måste du också placera en vidarebefordrare på plats så att begäranden för DNS-zonen för `168.63.129.16`lagringskonto dirigeras till Azure private DNS-tjänsten, som är frontad av den reserverade IP-adressen . (Kom ihåg `$storageAccountEndpoint` att fylla i om du kör kommandona i en annan PowerShell-session.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Använda Azure Files Hybrid-modulen för att konfigurera DNS-vidarebefordran
För att göra det så enkelt som möjligt att konfigurera DNS-vidarebefordran har vi tillhandahållit automatisering i Azure Files Hybrid-modulen. De cmdletar som tillhandahålls för att manipulera DNS i den här modulen hjälper dig att distribuera DNS-servrar i ditt virtuella Azure-nätverk och uppdatera dina lokala DNS-servrar för att vidarebefordra till dem. 

Om du aldrig har använt Azure Files Hybrid-modulen måste du först installera den på din arbetsstation. Hämta den [senaste versionen](https://github.com/Azure-Samples/azure-files-samples/releases) av Azure Files Hybrid PowerShell-modulen:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Distribuera DNS-vidarebefordringslösningen har två steg, skapa en DNS-vidarebefordringsregeluppsättning, som definierar vilka Azure-tjänster du vill vidarebefordra begäranden till och den faktiska distributionen av DNS-vidarebefordrare. 

I följande exempel vidarebefordras begäranden till lagringskontot, inklusive begäranden till Azure-filer, Azure Blob-lagring, Azure Table Storage och Azure Queue storage. Om så önskas kan du lägga till vidarebefordran `-AzureEndpoints` för `New-AzDnsForwardingRuleSet` ytterligare Azure-tjänst till regeln via parametern för cmdlet. Kom ihåg `<virtual-network-resource-group>` `<virtual-network-name>`att `<subnet-name>` ersätta , och med lämpliga värden för din miljö.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Du kan dessutom finna det användbart / nödvändigt att tillhandahålla flera ytterligare parametrar:

| Parameternamn | Typ | Beskrivning |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Som standard distribueras DNS-servrarna till samma resursgrupp som det virtuella nätverket. Om detta inte är önskvärt kan du med den här parametern välja en alternativ resursgrupp som de ska distribueras till. |
| `DnsForwarderRootName` | `string` | Som standard har DNS-servrarna som distribueras i Azure namnen `DnsFwder-*`, där asterisken fylls i av en iterator. Den här parametern ändrar roten `DnsFwder`till det namnet (dvs. ). |
| `VmTemporaryPassword` | `SecureString` | Som standard väljs ett slumpmässigt lösenord för det tillfälliga standardkontot som en virtuell dator har innan den domänen är ansluten. När den är domänansluten inaktiveras standardkontot. |
| `DomainToJoin` | `string` | Domänen som dns-virtuella datorer ska ansluta till. Som standard väljs den här domänen baserat på domänen för den dator där du kör cmdleterna. |
| `DnsForwarderRedundancyCount` | `int` | Antalet virtuella DNS-datorer som ska distribueras för det virtuella nätverket. Som standard `New-AzDnsForwarder` distribuerar två DNS-servrar i ditt virtuella Azure-nätverk, i en tillgänglighetsuppsättning, för att säkerställa redundans. Detta nummer kan ändras efter behov. |
| `OnPremDnsHostNames` | `HashSet<string>` | En manuellt angiven lista över lokala DNS-värdnamn som du kan skapa vidarebefordrare på. Den här parametern är användbar när du inte vill använda vidarebefordrare på alla lokala DNS-servrar, till exempel när du har ett antal klienter med manuellt angivna DNS-namn. |
| `Credential` | `PSCredential` | En autentiseringsuppgifter som ska användas när DNS-servrarna uppdateras. Detta är användbart när användarkontot som du har loggat in med inte har behörighet att ändra DNS-inställningar. |
| `SkipParentDomain` | `SwitchParameter` | Som standard tillämpas DNS-vidarebefordrare på den domän på högsta nivå som finns i din miljö. Om till `northamerica.corp.contoso.com` exempel är en `corp.contoso.com`underordnad domän i skapas vidarebefordraren `corp.contoso.com`för DE DNS-servrar som är associerade med . Den här parametern gör att `northamerica.corp.contoso.com`vidarebefordrare skapas i . |

## <a name="confirm-dns-forwarders"></a>Bekräfta DNS-vidarebefordrare
Innan du testar för att se om DNS-vidarebefordrarna har tillämpats rekommenderar `Clear-DnsClientCache`vi att du rensar DNS-cachen på den lokala arbetsstationen med . Om du vill testa om du kan matcha det fullständigt kvalificerade `Resolve-DnsName` `nslookup`domännamnet för ditt lagringskonto använder eller .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Om namnmatchningen lyckas bör du se den lösta IP-adressen matcha IP-adressen för ditt lagringskonto.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Om du redan har konfigurerat en VPN- eller `Test-NetConnection` ExpressRoute-anslutning kan du också använda för att se att en TCP-anslutning kan göras till ditt lagringskonto.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Se även
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Överväganden för Azure Files-nätverk](storage-files-networking-overview.md)
- [Konfigurera slutpunkter för Azure Files-nätverk](storage-files-networking-endpoints.md)