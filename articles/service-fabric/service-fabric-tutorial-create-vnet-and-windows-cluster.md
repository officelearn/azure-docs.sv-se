---
title: Skapa ett Windows Service Fabric-kluster i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig att distribuera ett Windows Service Fabric-kluster till ett virtuellt Azure-nätverk och en nätverkssäkerhetsgrupp med PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/27/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 12880c3aada46b1656cf37b0cb539292cce930ef
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625716"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Självstudie: Distribuera ett Service Fabric Windows-kluster till ett virtuellt Azure-nätverk

Den här självstudien ingår i en serie. Du får lära dig att distribuera ett Windows Service Fabric-kluster till ett [virtuellt Azure-nätverk (VNET)](../virtual-network/virtual-networks-overview.md) och en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) med PowerShell och en mall. När du är färdig körs ett kluster i molnet som du kan distribuera program till.  Om du vill skapa ett Linux-kluster med Azure CLI kan du läsa [Skapa ett säkert Linux-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

I den här självstudien beskrivs ett produktionsscenario.  Om du snabbt vill skapa ett mindre kluster för testning kan du läsa [Skapa ett testkluster med tre noder](./scripts/service-fabric-powershell-create-test-cluster.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * skapa ett säkert Service Fabric-kluster i Azure med PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * ta bort ett kluster.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * skapa ett säkert kluster i Azure
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* installera [Service Fabric SDK och PowerShell-modulen](service-fabric-get-started.md)
* installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

I följande procedurer skapas ett Service Fabric-kluster med fem noder. Du kan beräkna kostnaden för att köra ett Service Fabric-kluster i Azure med [Azures prissättningsberäknare](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Viktiga begrepp

Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning virtuella datorer i klustret. Varje definierad nodtyp är konfigurerad som en [VM-skalningsuppsättning](/azure/virtual-machine-scale-sets/), en Azure-beräkningsresurs du använder till att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”.  Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster).  [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen.

Klustret skyddas med ett klustercertifikat. Ett klustercertifikat är ett X.509-certifikat som används för att skydda kommunikationen mellan noderna och att autentisera slutpunkterna för klusterhantering i en hanteringsklient.  Klustercertifikatet tillhandahåller också en SSL för API:t för HTTPS-hantering och för Service Fabric Explorer över HTTPS. Självsignerade certifikat är praktiska för testkluster.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat.

Klustercertifikatet måste:

* innehålla en privat nyckel
* vara skapat för nyckelutbyte som kan exporteras till en Personal Information Exchange-fil (.pfx)
* ha ett ämnesnamn som överensstämmer med domänen du använder för åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla SSL för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för domänen .cloudapp.azure.com. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Azure Key Vault används till att hantera certifikat för Service Fabric-kluster i Azure.  När ett kluster distribueras i Azure hämtar Azure-resursprovidern som ansvarar för att skapa Service Fabric-kluster certifikat från Key Vault och installerar dem på klustrets virtuella datorer.

I den här självstudien visas ett kluster med fem noder i en enda nodtyp. Vid distribution av kluster till produktion är det emellertid viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md). Här är några saker att tänka på i samband med den här processen.

* Antalet noder och nodtyper som behövs i klustret
* Egenskaperna för respektive nodtyp (exempelvis storlek, primär, offentlig och antal virtuella datorer)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Ladda ned följande mallfiler för Resource Manager:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Den här mallen distribuerar ett säkert kluster med fem virtuella datorer och en enda nodtyp till ett virtuellt nätverk och en nätverkssäkerhetsgrupp.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates).  [azuredeploy.json][template] distribuerar ett antal resurser, däribland följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/kluster** konfigureras ett Windows-kluster med följande egenskaper:

* en enda nodtyp
* fem noder av den primära nodtypen (kan konfigureras i mallparametrarna)
* Operativsystem: Windows Server 2016 Datacenter med containrar (kan konfigureras i mallparametrarna)
* skyddat med certifikat (kan konfigureras i mallparametrarna)
* [omvänd proxy](service-fabric-reverseproxy.md) är aktiverat
* [DNS-tjänsten](service-fabric-dnsservice.md) är aktiverad
* [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Brons (kan konfigureras i mallparametrarna)
* [Tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrarna)
* slutpunkt för klientanslutning: 19000 (kan konfigureras i mallparametrarna)
* slutpunkt för HTTP-gateway: 19080 (kan konfigureras i mallparametrarna)

### <a name="azure-load-balancer"></a>Azure-lastbalanserare

I resursen **Microsoft.Network/loadBalancers** har en belastningsutjämnare konfigurerats och avsökningar samt regler har konfigurerats för följande portar:

* klientanslutningsslutpunkt: 19000
* HTTP-gatewayslutpunkt: 19080
* programport: 80
* programport: 443
* omvänd proxy för Service Fabric: 19081

Om du behöver andra programportar måste du justera resursen **Microsoft.Network/loadBalancers** och **Microsoft.Network/networkSecurityGroups** för att låta trafiken komma in.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuellt nätverk, undernät och nätverkssäkerhetsgrupp

Namnen på det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen deklareras i mallparametrarna.  Adressutrymmen i det virtuella nätverket och undernätet deklareras också i mallparametrarna och konfigureras i resursen **Microsoft.Network/virtualNetworks**:

* det virtuella nätverkets adressutrymme: 172.16.0.0/20
* Service Fabric-undernätets adressutrymme: 172.16.2.0/23

Följande regler för inkommande trafik är aktiverade i resursen **Microsoft.Network/networkSecurityGroups**. Du kan ändra portvärdena genom att ändra mallvariablerna.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB : 445
* Internodecommunication - 1025, 1026, 1027
* Tillfälligt portintervall – 49152 till 65534 (minst 256 portar behövs)
* Portar för programanvändning: 80 och 443
* Portintervall för program – 49152 till 65534 (används för kommunikation mellan tjänster och öppnas inte i lastbalanseraren)
* Blockera alla andra portar

Om du behöver andra programportar måste du justera resursen **Microsoft.Network/loadBalancers** och **Microsoft.Network/networkSecurityGroups** för att låta trafiken komma in.

## <a name="set-template-parameters"></a>Ställa in mallparametrar

Parameterfilen [azuredeploy.parameters.json][parameters] deklarerar många värden som används till att distribuera klustret och associerade resurser. Här är några av parametrarna du kan behöva ändra för distributionen:

|Parameter|Exempelvärde|Anteckningar|
|---|---||
|adminUserName|vmadmin| Administratörsanvändarnamnet för virtuella klusterdatorer. [Användarnamnkrav för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret. [Lösenordskrav för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| Namnet på klustret. Får endast innehålla bokstäver och siffror. Längden ska vara mellan 3 och 23 tecken.|
|location|southcentralus| Klustrets placering. |
|certificateThumbprint|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets SHA1-tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”</p>. |
|certificateUrlValue|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil. </p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Exempel: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. Resource Manager-mallen [azuredeploy.json][template] skapar ett virtuellt nätverk (VNET), ett undernät och en nätverkssäkerhetsgrupp (NSG) för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Skapa ett kluster med ett befintligt certifikat

I följande skript används cmdleten [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) och en mall till att distribuera ett nytt kluster i Azure. Cmdleten skapar även ett nytt nyckelvalv i Azure och laddar upp certifikatet.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Skapa ett kluster med ett nytt, självsignerat certifikat

I följande skript används cmdleten [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) och en mall till att distribuera ett nytt kluster i Azure. Cmdleten skapar även ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat i nyckelvalvet och laddar ned certifikatfilen lokalt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det skyddade klustret

Anslut till klustret med Service Fabric PowerShell-modulen installerad med Service Fabric SDK.  Först installerar du certifikatet i det personliga arkivet för den aktuella användaren på datorn.  Kör följande PowerShell-kommando:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Du är nu redo att ansluta till det säkra klustret.

PowerShell-modulen **Service Fabric** har många cmdletar för hantering av Service Fabric-kluster, program och tjänster.  Använd cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) till att ansluta till det säkra klustret. Certifikatets SHA1-tumavtryck och information om slutpunkten för anslutning finns i utdata från föregående steg.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Kontrollera att du är ansluten och att klustret är felfritt med cmdleten [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra artiklarna i självstudieserien använder klustret du nyss skapade. Om du inte genast fortsätter till nästa artikel kanske du vill [ta bort klustret](service-fabric-cluster-delete.md) för att undvika kostnader.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * skapa ett säkert Service Fabric-kluster i Azure med hjälp av PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * ta bort ett kluster.

Fortsätt sedan till följande självstudie för att lära dig att skala klustret.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure-NSG/azuredeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Windows-1-NodeTypes-Secure-NSG/azuredeploy.parameters.json
