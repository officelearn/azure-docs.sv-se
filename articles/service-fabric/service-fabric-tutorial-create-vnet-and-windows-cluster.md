---
title: Skapa ett Windows Service Fabric-kluster i Azure | Microsoft Docs
description: "Lär dig hur du distribuerar ett Windows Service Fabric-kluster till befintlig Azure-nätverk med hjälp av PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: b06d0196f1f911f2f6cf87242d70455ba22b1f88
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Distribuera ett Service Fabric Windows-kluster till ett virtuellt Azure-nätverk
Den här kursen ingår i en serie. Du kommer lära dig hur du distribuerar ett Windows Service Fabric-kluster till ett befintligt Azure virtuellt nätverk (VNET) och underordnad net med hjälp av PowerShell. När du är klar kan har du ett kluster som körs i molnet som du kan distribuera program till.  För att skapa ett Linux-kluster med Azure CLI, se [skapa en säker Linux-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med hjälp av PowerShell
> * Skapa ett nyckelvalv och överföra ett certifikat
> * Skapa en säker Service Fabric-kluster i Azure PowerShell
> * Skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa en säker kluster i Azure
> * [Skala ett kluster in eller ut](/service-fabric-tutorial-scale-cluster.md)
> * [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Fabric-SDK och PowerShell-modul](service-fabric-get-started.md)
- Installera den [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Följande procedurer skapa ett Service Fabric-kluster med fem noder. Att beräkna kostnaden genom att köra ett Service Fabric-kluster i Azure används den [Priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välja din prenumeration
Den här guiden använder Azure PowerShell. När du startar en ny PowerShell-session, logga in på ditt Azure-konto och välja din prenumeration innan du kan köra kommandon för Azure.
 
Kör följande skript för att logga in på kontot väljer din prenumeration:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp för din distribution och ge det ett namn och en plats.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Distribuera nätverkets topologi
Därefter konfigurera nätverkets topologi som API Management och Service Fabric-klustret kommer att distribueras. Den [network.json] [ network-arm] Resource Manager-mall har konfigurerats för att skapa ett virtuellt nätverk (VNET) och en undernät och nätverket säkerhetsgrupp (NSG) för Service Fabric och ett undernät, och NSG för API Management . Lär dig mer om Vnet, undernät och NSG: er [här](../virtual-network/virtual-networks-overview.md).

Den [network.parameters.json] [ network-parameters-arm] parameterfilen innehåller namnen på undernät och NSG: er som Service Fabric och API-hantering distribuera till.  API Management har distribuerats i det [följande kursen](service-fabric-tutorial-deploy-api-management.md). Den här guiden behöver inte parametervärden som ska ändras. Service Fabric Resource Manager-mallar använder dessa värden.  Om värdena ändras här, måste du ändra dem i de andra Resource Manager-mallar används i den här kursen och [distribuera API Management kursen](service-fabric-tutorial-deploy-api-management.md). 

Hämta följande Resource Manager-mall och parametrar filen:
- [Network.JSON][network-arm]
- [Network.parameters.JSON][network-parameters-arm]

Använd följande PowerShell-kommando för att distribuera Resource Manager-mall och parametern-filer för installation av nätverk:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Distribuera Service Fabric-kluster
När nätverksresurser är klar för distribution, är nästa steg att distribuera ett Service Fabric-kluster till VNET i undernät och NSG: N för Service Fabric-klustret. Distribuera ett kluster till ett befintligt VNET och undernät (distribuerad tidigare i den här artikeln) kräver en Resource Manager-mall.  För den här självstudiekursen serien är mallen förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående steg.  

Hämta följande Resource Manager-mall och parametrar filen:
- [cluster.JSON][cluster-arm]
- [cluster.parameters.JSON][cluster-parameters-arm]

Du kan använda mallen för att skapa en säker kluster.  Ett certifikat för klustret är ett X.509-certifikat som används för att skydda kommunikationen nod till nod och autentisera att klustret management slutpunkter management-klienten.  Certifikat för klustret ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS. Azure Key Vault används för att hantera certifikat för Service Fabric-kluster i Azure.  När ett kluster distribueras i Azure, Azure resursprovidern ansvarar för att skapa Service Fabric-kluster hämtar certifikat från Nyckelvalvet och installerar dem på klustret virtuella datorer. 

Du kan använda ett certifikat från en certifikatutfärdare (CA) som certifikatet klustret eller för testning, skapa ett självsignerat certifikat. Certifikat för klustret måste:

- innehåller en privat nyckel.
- skapas för nyckelutbyte, som kan exporteras till en Personal Information Exchange (.pfx)-fil.
- ha ett ämnesnamn som matchar den domän som du använder för att få åtkomst till Service Fabric-klustret. Den här matchar krävs SSL för att klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för det. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Fyll i följande tomma parametrar i den *cluster.parameters.json* filen för din distribution:

|Parameter|Värde|
|---|---|
|adminPassword|Lösenordet #1234|
|adminUserName|vmadmin|
|Klusternamn|mysfcluster|
|location|usasödracentrala|

Lämna den *certificateThumbprint*, *certificateUrlValue*, och *sourceVaultValue* parametrar tomt om du vill skapa ett självsignerat certifikat.  Om du vill använda ett befintligt certifikat som tidigare har överförts till ett nyckelvalv kan fylla i de parametervärdena.

Följande skript använder den [ny AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet och mallen för att distribuera ett nytt kluster i Azure. Cmdlet även skapar ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat till nyckelvalvet och hämtar certifikatfilen lokalt. Du kan ange ett befintligt certifikat och/eller nyckelvalv med andra parametrar för den [ny AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet.

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det säkra klustret
Anslut till klustret med hjälp av Service Fabric PowerShell-modulen installerad med Fabric-SDK.  Först installera certifikatet i arkivet för personliga (min) för den aktuella användaren på datorn.  Kör följande PowerShell-kommando:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Du är nu redo att ansluta till det säkra klustret.

Den **Service Fabric** PowerShell-modulen ger många cmdlet: ar för hantering av Service Fabric-kluster, program och tjänster.  Använd den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) för att ansluta till den säkra klustret. Tumavtrycket och anslutning endpoint certifikatinformation hittades i utdata från föregående steg.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Kontrollera att du är ansluten och att klustret är felfri med hjälp av den [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Rensa resurser
De andra artiklarna i den här självstudiekursen serien använda kluster som du nyss skapade. Om du inte omedelbart flytta in i nästa artikel kanske du vill ta bort klustret och nyckelvalv för att undvika kostnader. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Ta bort resursgruppen och alla klusterresurser med hjälp av den [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Också ta bort resursgruppen som innehåller nyckelvalvet.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med hjälp av PowerShell
> * Skapa ett nyckelvalv och överföra ett certifikat
> * Skapa en säker Service Fabric-kluster i Azure med hjälp av PowerShell
> * Skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med PowerShell
> * Ta bort ett kluster

Gå sedan till följande kursen lär dig hur du skala ditt kluster.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
