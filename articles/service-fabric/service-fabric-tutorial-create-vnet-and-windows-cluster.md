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
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 64ba17053179d428f5ef7e5ce9685240bde6665f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822999"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Självstudier: Distribuera ett Service Fabric Windows-kluster till ett virtuellt Azure-nätverk

Den här självstudien ingår i en serie. Du får lära dig att distribuera ett Windows Service Fabric-kluster till ett [virtuellt Azure-nätverk (VNET)](../virtual-network/virtual-networks-overview.md) och en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) med PowerShell och en mall. När du är färdig körs ett kluster i molnet som du kan distribuera program till.  Om du vill skapa ett Linux-kluster med Azure CLI kan du läsa [Skapa ett säkert Linux-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

I den här självstudien beskrivs ett produktionsscenario.  Om du snabbt vill skapa ett mindre kluster för testning kan du läsa [Skapa ett testkluster](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa ett VNET i Azure med PowerShell
> * skapa ett nyckelvalv och ladda upp ett certifikat
> * Konfigurera Azure Active Directory-autentisering
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
* installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Granska viktiga begrepp för [Azure-kluster](service-fabric-azure-clusters-overview.md)

Följande procedurer skapar ett Service Fabric-kluster med sju noder. Du kan beräkna kostnaden för att köra ett Service Fabric-kluster i Azure med [Azures prissättningsberäknare](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Ladda ned följande mallfiler för Resource Manager:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Den här mallen distribuerar ett säkert kluster med sju virtuella datorer och tre nodtyper till ett virtuellt nätverk och en nätverkssäkerhetsgrupp.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates).  [azuredeploy.json][template] distribuerar ett antal resurser, däribland följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/kluster** konfigureras ett Windows-kluster med följande egenskaper:

* tre nodtyper
* fem noder av den primära nodtypen (kan konfigureras i mallparametrarna), en nod vardera i de andra två nodtyperna
* Operativsystem: Windows Server 2016 Datacenter med containrar (kan konfigureras i mallparametrarna)
* skyddat med certifikat (kan konfigureras i mallparametrarna)
* [omvänd proxy](service-fabric-reverseproxy.md) är aktiverat
* [DNS-tjänsten](service-fabric-dnsservice.md) är aktiverad
* [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Brons (kan konfigureras i mallparametrarna)
* [Tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrarna)
* klientanslutningsslutpunkt: 19000 (kan konfigureras i mallparametrarna)
* HTTP-gatewayslutpunkt: 19080 (kan konfigureras i mallparametrarna)

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

### <a name="windows-defender"></a>Windows Defender
Som standard är [Windows Defender Antivirus](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) installerat och fungerar på Windows Server 2016. Användargränssnittet installeras som standard på vissa SKU:er, men det krävs inte.  För varje nodtyp/VM-skalningsuppsättning som deklareras i mallen används [Azure VM Antimalware-tillägget](/azure/virtual-machines/extensions/iaas-antimalware-windows) för att utesluta Service Fabric-katalogerna och -processerna:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

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

## <a name="set-up-azure-active-directory-client-authentication"></a>Konfigurera Azure Active Directory-klientautentisering
För Service Fabric-kluster som distribueras i ett offentligt nätverk som hanteras i Azure är rekommendationen för ömsesidig klient-till-nod-autentisering:
* Använd Azure Active Directory för klientidentitet
* Ett certifikat för serveridentitet och SSL-kryptering av http-kommunikation

konfiguration av Azure AD för att autentisera klienter för ett Service Fabric-kluster måste göras innan [klustret skapas](#createvaultandcert).  Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. 

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) och [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.  När programmen har skapats tilldelar du användare till roller som skrivskyddad och administratör.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar sig klusternamn och slutpunkter bör värdena vara planerade och inte värden som du redan har skapat.

I den här artikeln förutsätter vi att du redan har skapat en klientorganisation. Om du inte har gjort det börjar du med att läsa avsnittet om [hur du skaffar en Azure Active Directory-klientorganisation](../active-directory/develop/quickstart-create-new-tenant.md).

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av de steg som används för att konfigurera Azure AD med Service Fabric-kluster. [Ladda ned skripten](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) till datorn.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Skapa Azure AD-program och tilldela användare till roller
Skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program. När du har skapat programmen för att representera klustret tilldelar du dina användare till de [roller som stöds av Service Fabric](service-fabric-cluster-security-roles.md): skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar.  Ange även användarnamn och lösenord för användarna.  Exempel:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure Kina och Azure Tyskland) bör du även ange parametern `-Location`.

*TenantId* eller katalog-ID finns i [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory -> Egenskaper** och kopiera värdet för **Katalog-ID**.

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsett att göra det enklare att mappa Azure AD-artefakter till det Service Fabric-kluster som de används med.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilken som standard är:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klientorganisationen. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. Om du tittar på klientorganisationens program i [Azure-portalen](https://portal.azure.com) bör du se två nya poster:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Skriptet skriver ut det JSON som krävs av Azure Resource Manager-mallen när du skapar klustret, så det är en bra idé att fortsätta ha PowerShell-fönstret öppet.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägga till Azure AD-konfiguration för att använda Azure AD för klientåtkomst
I [azuredeploy.json][template] konfigurerar du Azure AD i avsnittet **Microsoft.ServiceFabric/clusters**.  Lägg till parametrar för klientorganisations-ID, klusterprogram-ID och klientprogram-ID.  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Lägg till parametervärdena i parameterfilen [azuredeploy.parameters.json][parameters].  Exempel:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. Resource Manager-mallen [azuredeploy.json][template] skapar ett virtuellt nätverk (VNET), ett undernät och en nätverkssäkerhetsgrupp (NSG) för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

Mallen i den här artikeln distribuerar ett kluster som använder certifikatets tumavtryck för att identifiera klustercertifikatet.  Två certifikat kan inte ha samma tumavtryck, vilket gör certifikathantering svårare. Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare.  Du kan lära dig att uppdatera klustret till att använda vanliga certifikatnamn för certifikathantering genom att läsa avsnittet om att [ändra kluster till vanligt certifikatnamn för hantering](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

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

Om du tidigare har konfigurerat AAD-klientautentisering kör du följande: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Om du inte har konfigurerat AAD-klientautentisering kör du följande:
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
> * Konfigurera Azure Active Directory-autentisering
> * skapa ett säkert Service Fabric-kluster i Azure med hjälp av PowerShell
> * skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * ta bort ett kluster.

Fortsätt sedan till följande självstudie för att lära dig att skala klustret.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
