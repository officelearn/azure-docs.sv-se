---
title: Skapa ett Service Fabric-kluster i Azure | Microsoft Docs
description: "Lär dig hur du skapar ett Windows- eller Linux Service Fabric-kluster i Azure med hjälp av PowerShell"
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
ms.date: 10/03/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 217b9f2f0dfed5b095e1bac1c8146abf4753fadc
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>Skapa en säker kluster i Azure med hjälp av PowerShell
Den här artikeln är först i en serie kurser som visar hur du flyttar ett .NET-program till molnet med hjälp av Azure Service Fabric-kluster och behållare. I följande steg ska du lära dig hur du skapar ett Service Fabric-kluster (Windows eller Linux) som körs i Azure. När du är klar kan har du en säker kluster som körs i molnet som du kan distribuera program.

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera den [Service Fabric SDK](service-fabric-get-started.md).
- Installera [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). (Om det behövs, [installera Azure PowerShell](/powershell/azure/overview) eller [uppdatera till en nyare version](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps).)


## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Det här skriptet skapar en enda nod, förhandsgranska Service Fabric klustret. Ett självsignerat certifikat skyddar klustret. Skriptet skapar certifikat tillsammans med klustret och placerar certifikatet i en nyckelvalvet. Du kan skala nod kluster utöver en virtuell dator och du uppgradera inte kluster förhandsgranskning till nyare versioner.

För att beräkna kostnaden genom att köra ett Service Fabric-kluster i Azure, Använd den [Azure prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/).
Mer information om hur du skapar Service Fabric-kluster finns [skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Logga in på Azure
Öppna ett PowerShell-konsol, logga in på Azure och välj den prenumeration som du vill distribuera den till klustret i:

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Klusterparametrar

   Det här skriptet använder följande parametrar och begrepp. Anpassa parametrarna så att de passar dina behov.

   | Parameter       | Beskrivning | Föreslaget värde |
   | --------------- | ----------- | --------------- |
   | Plats | Azure-regionen där du distribuerade klustret. | Till exempel *westeurope*, *eastasia*, eller *eastus* |
   | Namn     | Namnet på det kluster du vill skapa. Namnet måste innehålla 4-23 tecken och kan bara ha gemena bokstäver, siffror och bindestreck. | Till exempel *bobs sfpreviewcluster* |
   | resourceGroupName   | Namnet på resursgruppen där du vill skapa klustret. | Till exempel *myresourcegroup* |
   | VmSku  | Den virtuella datorn SKU för noderna. | En giltig virtuell dator SKU |
   | Operativsystem  | Den virtuella datorn ska användas för noder. | En giltig virtuell dator OS |
   | keyVaultName | Namnet på det nya nyckelvalvet ska associeras med klustret. | Till exempel *mykeyvault* |
   | ClusterSize | Antalet virtuella datorer i klustret (kan vara *1* eller *3-99*).| Ange endast en virtuell dator i ett kluster med förhandsgranskning |
   | CertificateSubjectName | Ämnesnamnet för certifikatet som ska skapas. | I formatet:  *<name>* . *<location>* . cloudapp.azure.com |

### <a name="default-parameter-values"></a>Standardparametervärden
**Virtuella**: valfria inställningar. Om du inte anger dem administratörsanvändarnamnet standard *vmadmin* och PowerShell frågar efter ett lösenord för virtuell dator innan klustret skapas.

**Portar**: portarna 80 och 8081 som standard. Du kan ange ytterligare portar genom att följa riktlinjerna för [portar i Service Fabric-kluster](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Diagnostik**: aktiverat som standard.

**DNS-tjänsten**: inte aktiverat som standard.

**Omvänd proxy**: inte aktiverat som standard.

## <a name="create-the-cluster-with-your-parameters"></a>Skapa klustret med parametrarna

När du väljer de parametrar som passar dina behov, kör du följande kommando för att skapa en säker Service Fabric-klustret och dess certifikat.

Du kan ändra det här skriptet för att inkludera ytterligare parametrar. Mer information om parametrar för att skapa kluster finns i [ny AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) cmdlet.

>[!NOTE]
>Innan du kör det här kommandot måste du skapa en mapp där du kan lagra certifikat för klustret.

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

Processen kan ta flera minuter. När konfigurationen är klar visar information om klustret som skapats i Azure. Den kopierar även klustret certifikat till katalogen - CertificateOutputFolder på sökvägen för den här parametern.

Anteckna den **ManagementEndpoint** URL för klustret, vilket du kanske t.ex. följande URL: https://mycluster.westeurope.cloudapp.azure.com:19080.

## <a name="import-the-certificate"></a>Importera certifikat

När klustret har skapats, kör du följande kommando för att kontrollera att du använder ett självsignerat certifikat:

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

Det här kommandot installerar certifikatet på den aktuella användaren av datorn. Du måste certifikatet för att komma åt Service Fabric Explorer, hälsotillståndet för klustret.


## <a name="view-your-cluster-optional"></a>Visa ditt kluster (valfritt)

När du har både klustret och det importerade certifikatet, kan du ansluta till klustret och visa dess hälsa. Det finns flera sätt att ansluta via Service Fabric-Utforskaren eller PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Du kan visa hälsotillståndet för klustret via Service Fabric Explorer. Om du vill göra det, bläddra till den **ManagementEndpoint** URL för klustret och välj sedan den certifikatfil som du sparade på datorn.

>[!NOTE]
>När du öppnar Service Fabric Explorer finns ett certifikat som du använder ett självsignerat certifikat. I Microsoft Edge du måste klicka på **information**, och klicka sedan på den **går du vidare till webbsidan** länk. I Chrome, du måste klicka på **Avancerat**, och klicka sedan på den **fortsätta** länk.

### <a name="powershell"></a>PowerShell

Service Fabric PowerShell-modulen innehåller många cmdlets för att hantera Service Fabric-kluster, program och tjänster. Använd den [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) för att ansluta till den säkra klustret. Tumavtrycket och anslutning endpoint certifikatinformation kan hittas i utdata från föregående steg.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

Du kan också kontrollera att du är ansluten och att klustret är felfri med hjälp av den [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur du skapar en säker Service Fabric-kluster i Azure med hjälp av PowerShell.

Gå sedan till följande guiden för att lära dig hur du distribuerar ett befintligt program:
> [!div class="nextstepaction"]
> [Distribuera ett befintligt .NET-program med Docker Compose](service-fabric-host-app-in-a-container.md)

 
