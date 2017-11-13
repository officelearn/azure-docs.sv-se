---
title: Konfigurera ett Azure Service Fabric-kluster | Microsoft Docs
description: "Den här snabbstarten hjälper dig att skapa ett Service Fabric-kluster i Azure för Windows eller Linux."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Skapa ditt första Service Fabric-kluster i Azure
Ett [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. I den här snabbstarten får du hjälp att skapa ett kluster med fem noder som körs i antingen Windows eller Linux på bara några minuter via [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) eller [Azure Portal](http://portal.azure.com). Du kan även använda Azure CLI för den här uppgiften.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Skapa klustret

1. Välj **Nytt** i det övre vänstra hörnet i Azure Portal.
2. Sök efter **Service Fabric** och välj **Service Fabric-kluster** från de returnerade resultaten. Välj sedan **Skapa**.
3. Fyll i Service Fabric-formuläret **Grundläggande inställningar**. För **Operativsystem** väljer du den version av Windows eller Linux som du vill köra klusternoderna i. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. Välj **OK** när du är klar.

    ![Skärmbild av utdata efter klusterinstallationen][cluster-setup-basics]

4. Fyll i formuläret **Klusterkonfiguration**. För **Antal nodtyper** anger du **1**.

5. Välj **Node type 1 (Primary)** (Nodtyp 1 (Primär)) och fyll i formuläret **Konfiguration av nodtyp**. Ange ett nodtypnamn och ställ in [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) på **Brons**. Välj sedan en VM-storlek.

    Nodtypen definierar antalet virtuella datorer och deras storlek, anpassade slutpunkter och andra inställningar för virtuella datorer av samma typ. Varje definierad nodtyp konfigureras som en separat VM-skalningsuppsättning som används till att distribuera och hantera virtuella datorer som en uppsättning. Varje nodtyp kan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Den första, eller primära nodtypen används för systemtjänsterna för Service Fabric. Den här nodtypen måste ha fem eller fler virtuella datorer.

    Vid distribution till en produktionsmiljö är det viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md). I den här snabbstarten kör du däremot inga program. Välj därför VM-storleken *DS1_v2 Standard*. Välj **Silver** som [tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) och ange en inledande kapacitet på 5 för VM-skalningsuppsättningar.  

    Anpassade slutpunkter används för att öppna portar i Azure Load Balancer så att du kan ansluta med program som körs i klustret.  Ange **80, 8172** så att du öppnar portarna 80 och 8172.

    Markera inte kryssrutan **Konfigurera avancerade inställningar**, som används till att anpassa slutpunkter för TCP/HTTP-hantering, portintervall för program, [placeringsbegränsningar](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) och [kapacitetsegenskaper](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Skärmbild av konfiguration av nodtyp][node-type-config]

    Välj **OK**.

6. I formuläret **Klusterkonfiguration** ställer du in **Diagnostik** på **På**. I den här snabbstarten behöver du inte ange några anpassade [infrastrukturinställningar](service-fabric-cluster-fabric-settings.md).  Under **Fabric-version** väljer du uppgraderingsläget **Automatiskt** så att Microsoft automatiskt uppdaterar den Fabric-kod som kör klustret.  Ställ in läget som **Manuellt** om du vill [välja en version som stöds](service-fabric-cluster-upgrade.md) och uppgradera till den.     

    Välj **OK**.

7. Fyll i formuläret **Säkerhet**. För den här snabbstarten väljer du **Ta bort skydd**. Observera att du i allmänhet bör skapa ett säkert kluster för produktionsarbetsbelastningar. Vem som helst kan anonymt ansluta till ett oskyddat kluster och utföra hanteringsåtgärder.  

   Service Fabric använder certifikat för att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information finns i [Service Fabric cluster security scenarios](service-fabric-cluster-security.md) (Säkerhetsscenarier för Service Fabric-kluster). Läs om hur du [skapar ett kluster från en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) om du vill aktivera autentisering av användare via Azure Active Directory eller ställa in certifikat för programsäkerhet.

    Välj **OK**.

8. Granska sammanfattningen. Om du vill ladda ned en Azure Resource Manager-mall som bygger på de inställningar du har angett väljer du **Ladda ned mall och parametrar**. Välj **Skapa** så att klustret skapas.

    Du kan se förloppet bland aviseringarna. (Välj klockikonen nära statusfältet uppe till höger på skärmen.) Om du valde **Fäst på startsidan** när du skapade klustret ser du **Deploying Service Fabric Cluster** (Distribuerar Service Fabric-kluster) fäst på **startsidan**.

### <a name="connect-to-the-cluster-by-using-powershell"></a>Anslut till klustret med PowerShell
Kontrollera att klustret körs genom att ansluta till PowerShell. Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](service-fabric-get-started.md). Cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) upprättar en anslutning till klustret.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Fler exempel på hur du ansluter till ett kluster finns i [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md). När du har anslutit till klustret använder du cmdleten [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) för att visa en lista över noder i klustret och statusinformation för varje nod. **HealthState** bör vara *OK* för varje nod.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Ta bort klustret
Ett Service Fabric-kluster består av andra Azure-resurser förutom själva klusterresursen. Så om du vill ta bort ett Service Fabric-kluster helt måste du också ta bort alla resurser det består av. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen. Andra sätt att ta bort ett kluster eller borttagning av vissa (men inte alla) resurser i en resursgrupp beskrivs i [Ta bort ett kluster](service-fabric-cluster-delete.md).

Ta bort en resursgrupp på Azure Portal:
1. Bläddra till det Service Fabric-kluster du vill ta bort.
2. Välj namnet på **Resursgruppen** på sidan med klusterinformation.
3. Välj **Ta bort resursgrupp** på sidan **Resource Group Essentials** (Information om resursgrupp). Följ sedan anvisningarna på sidan för att ta bort resursgruppen.
    ![Skärmbild av sidan Information om resursgrupp med Ta bort resursgrupp markerat][cluster-delete]


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Ett annat sätt att skapa klustret är att använda PowerShell. Så här gör du:

1. Ladda ned [version 4.0 eller senare av Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) till datorn.

2. Kör cmdleten [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) för att skapa ett Service Fabric-kluster med fem noder som är skyddat med ett X.509-certifikat. Kommandot skapar ett självsignerat certifikat och laddar upp det till ett nytt nyckelvalv. Certifikatet kopieras även till en lokal katalog. Ange parametern *-OS* för att välja den version av Windows eller Linux som körs på klusternoderna. Anpassa parametrarna efter behov. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    Kommandot kan ta allt från 10 till 30 minuter att slutföra. Utdata innehåller information om certifikatet, nyckelvalvet som certifikatet laddades upp till och den lokala mapp som certifikatet kopierades till.     

3. Kopiera alla utdata och spara dem i en textfil (vi kommer att hänvisa till dem senare). Anteckna följande information från utdata: 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Installera certifikatet på din lokala dator
  
För att kunna ansluta till klustret installerar du certifikatet i det personliga arkivet för den aktuella användaren. 

Kör följande:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

Du är nu redo att ansluta till det säkra klustret.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster 

Kör cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) för att upprätta en anslutning till ett säkert kluster. Certifikatinformationen måste matcha certifikatet som användes för att konfigurera klustret. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

I följande exempel visas exempelparametrarna: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Kör följande kommando för att kontrollera att du är ansluten och att klustret är felfritt.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Ta bort klustret
Ett kluster består av andra Azure-resurser förutom själva klusterresursen. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Använda Azure CLI
Ett annat sätt att skapa klustret är att använda CLI. Så här gör du:

1. Installera [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) på datorn.
2. Logga in på Azure och välj den prenumeration som du vill skapa klustret i.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Kör kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) för att skapa ett Service Fabric-kluster med fem noder som är skyddat med ett X.509-certifikat. Kommandot skapar ett självsignerat certifikat och laddar upp det till ett nytt nyckelvalv. Certifikatet kopieras även till en lokal katalog. Ange parametern *-os* för att välja den version av Windows eller Linux som körs på klusternoderna. Anpassa parametrarna efter behov.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Anslut till klustret
Kör följande CLI-kommando för att ansluta till klustret med certifikatet.  Se till att certifikatinformationen matchar ett certifikat som distribuerats till klusternoderna när du använder ett klientcertifikat för autentisering. Använd alternativet `--no-verify` för ett självsignerat certifikat.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Kör följande kommando för att kontrollera att du är ansluten och att klustret är felfritt.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Anslut direkt till noderna 

Du kan använda SSH-kommandot för att ansluta till noderna i ett Linux-kluster. Ange ett portnummer från 3389 och uppåt. För de fem nodkluster som skapades tidigare skulle exempelvis kommandona vara följande:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Ta bort klustret
Ett kluster består av andra Azure-resurser förutom själva klusterresursen. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat ett utvecklingskluster provar du följande:
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Ta bort ett kluster](service-fabric-cluster-delete.md) 
* [Distribuera appar med hjälp av PowerShell](service-fabric-deploy-remove-applications.md)
* [Distribuera appar med hjälp av CLI](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
