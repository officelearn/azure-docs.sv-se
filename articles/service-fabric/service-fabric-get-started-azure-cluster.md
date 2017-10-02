---
title: Konfigurera ett Azure Service Fabric-kluster | Microsoft Docs
description: "Snabbstart – skapa ett Service Fabric-kluster i Azure för Windows eller Linux."
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
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecf9554554c8b7acbd8b8f5aa9122ce1678c6502
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Skapa ditt första Service Fabric-kluster i Azure
Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. I den här snabbstarten får du hjälp att skapa ett kluster med fem noder som körs i antingen Windows eller Linux på bara några minuter via [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) eller [Azure Portal](http://portal.azure.com).  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Skapa klustret

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
2. Välj **Compute** från bladet **Nytt** och sedan **Service Fabric-kluster** från bladet **Compute**.
3. Fyll i Service Fabric-formuläret **Grundläggande inställningar**. För **Operativsystem** väljer du den version av Windows eller Linux som du vill köra klusternoderna i. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. När du är klar klickar du på **OK**.

    ![Utdata efter klusterinstallationen][cluster-setup-basics]

4. Fyll i formuläret **Klusterkonfiguration**.  För **Antal nodtyper** anger du "1".

5. Välj **Node type 1 (Primary)** (Nodtyp 1 (Primär)) och fyll i formuläret **Konfiguration av nodtyp**.  Ange ett nodtypnamn och ställ in [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) på "Brons."  Välj en VM-storlek.

    Nodtypen definierar antalet virtuella datorer och deras storlek, anpassade slutpunkter och andra inställningar för virtuella datorer av samma typ. Varje definierad nodtyp konfigureras som en separat VM-skalningsuppsättning som används till att distribuera och hantera virtuella datorer som en uppsättning. Varje nodtyp kan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet.  Den första, eller primära, nodtypen används för systemtjänsterna för Service Fabric, och den måste ha fem eller fler virtuella datorer.

    Vid distribution till en produktionsmiljö är det viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md).  I den här snabbstarten kör du däremot inga program, så välj VM-storleken *DS1_v2 Standard*.  Välj ”Silver” som [tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) och en inledande kapacitet på 5 för VM-skalningsuppsättningar.  

    Anpassade slutpunkter används till att öppna portar i Azure Load Balancer så att du kan ansluta med program som körs i klustret.  Ange ”80, 8172” så att du öppnar portarna 80 och 8172.

    Markera inte kryssrutan **Konfigurera avancerade inställningar**, som används till att anpassa slutpunkter för TCP/HTTP-hantering, portintervall för program, [placeringsbegränsningar](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) och [kapacitetsegenskaper](service-fabric-cluster-resource-manager-metrics.md).    

    Välj **OK**.

6. I formuläret **Klusterkonfiguration** ställer du in **Diagnostik** på **På**.  I den här snabbstarten behöver du inte ange några anpassade [infrastrukturinställningar](service-fabric-cluster-fabric-settings.md).  Under **Fabric-version** väljer du uppgraderingsläget **Automatiskt** så att Microsoft automatiskt uppdaterar den Fabric-kod som kör klustret.  Ställ in läget som **Manuellt** om du vill [välja en version som stöds](service-fabric-cluster-upgrade.md) och uppgradera till den. 

    ![Konfiguration av nodtyp][node-type-config]

    Välj **OK**.

7. Fyll i formuläret **Säkerhet**.  I den här snabbstarten kan du välja **Ta bort skydd**.  I produktionsmiljöer bör du endast skapa skyddade kluster eftersom alla kan ansluta anonymt till oskyddade kluster och utföra hanteringsåtgärder.  

    Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns i [Service Fabric cluster security scenarios](service-fabric-cluster-security.md) (Säkerhet för Service Fabric-kluster).  Om du vill aktivera autentisering av användare via Azure Active Directory eller ställa in certifikat för programsäkerhet kan du [skapa ett kluster från en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

    Välj **OK**.

8. Granska sammanfattningen.  Om du vill ladda ned en Resource Manager-mall som bygger på de inställningar du har angett väljer du **Ladda ned mall och parametrar**.  Välj **Skapa** så att klustret skapas.

    Du kan se förloppet bland aviseringarna. (Klicka på klockikonen nära statusfältet uppe till höger på skärmen.) Om du klickade på **Fäst på startsidan** när du skapade klustret ser du **Deploying Service Fabric Cluster** (Distribuerar Service Fabric-kluster) fäst på **startsidan**.

### <a name="connect-to-the-cluster-using-powershell"></a>Ansluta till klustret med PowerShell
Kontrollera att klustret körs genom att ansluta med PowerShell.  Service Fabric PowerShell-modulen installeras med [Service Fabric SDK](service-fabric-get-started.md).  Cmdleten [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) upprättar en anslutning till klustret.   

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
Ett Service Fabric-kluster består av andra Azure-resurser förutom själva klusterresursen. Så om du vill ta bort ett Service Fabric-kluster helt måste du också ta bort alla resurser det består av. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen. Andra sätt att ta bort ett kluster eller borttagning av vissa (men inte alla) resurser i en resursgrupp beskrivs i [Ta bort ett kluster](service-fabric-cluster-delete.md)

Ta bort en resursgrupp på Azure Portal:
1. Navigera till det Service Fabric-kluster du vill ta bort.
2. Klicka på **Resursgrupp** på sidan med klusterinformation.
3. På sidan **Resource Group Essentials** (Information om resursgrupp) klickar du på **Ta bort resursgrupp** och följer sedan anvisningarna för borttagning av resursgruppen.
    ![Ta bort resursgruppen][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-windows-cluster"></a>Använda Azure Powershell för att distribuera ett säkert Windows-kluster
1. Ladda ned [Azure Powershell-modul version 4.0 eller senare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) på datorn.

2. Öppna ett Windows PowerShell-fönster och kör följande kommando. 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    Du bör se utdata som liknar följande.

    ![ps-list][ps-list]

3. Logga in på Azure och välj den prenumeration som du vill skapa klustret på

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. Kör följande kommande för att skapa ett säkert kluster. Glöm inte att anpassa parametrarna. 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    Det kan ta mellan 10 och 30 minuter att slutföra kommandot. När det har slutförts bör utdata se ut ungefär som nedan. Utdata innehåller information om certifikatet, nyckelvalvet som certifikatet överfördes till och den lokala mapp som certifikatet kopieras till. 

    ![ps-out][ps-out]

5. Kopiera alla utdata och spara dem i en textfil eftersom vi behöver hänvisa till dem. Anteckna följande information från utdata. 

    - **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>Installera certifikatet på din lokala dator
  
För att kunna ansluta till klustret måste du installera certifikatet i det personliga arkivet för den aktuella användaren. 

Kör följande PowerShell-kommando

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Du är nu redo att ansluta till det säkra klustret.

### <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster 

Kör följande PowerShell-kommando för att ansluta till ett säkert kluster. Certifikatinformationen måste matcha certifikatet som användes för att konfigurera klustret. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


I följande exempel visas de ifyllda parametrarna: 

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

Remove-AzureRmResourceGroup -Name $RGname -Force

```
## <a name="use-azure-cli-to-deploy-a-secure-linux-cluster"></a>Använda Azure CLI för att distribuera ett säkert Linux-kluster

1. Installera [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) på datorn.
2. Logga in på Azure och välj den prenumeration som du vill skapa klustret i.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Kör kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) för att skapa ett säkert kluster.

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
    # the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Anslut till klustret
Kör följande CLI-kommando för att ansluta till klustret med certifikatet.  När du använder ett klientcertifikat för autentisering måste certifikatinformationen matcha ett certifikat som distribuerats till klusternoderna.  Använd alternativet `--no-verify` för ett självsignerat certifikat.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Kör följande kommando för att kontrollera att du är ansluten och att klustret är felfritt.

```azurecli
az sf cluster health
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
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

