---
title: 'Snabbstart: Konfigurera Azure NetApp-filer och NFS-volym'
description: Snabbstart - Beskriver hur du snabbt konfigurerar Azure NetApp-filer och skapar en volym.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 12/01/2019
ms.openlocfilehash: fc7f13fb7ffe1667aaeaa4a3cc1916c6049a98c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75551666"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Snabbstart: Konfigurera Azure NetApp-filer och skapa en NFS-volym 

Den här artikeln visar hur du snabbt konfigurerar Azure NetApp-filer och skapar en volym. 

I den här snabbstarten ställer du in följande objekt:

- Registrering för Azure NetApp-filer och NetApp-resursprovider
- Ett NetApp-konto
- En kapacitetspool
- En NFS-volym för Azure NetApp-filer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar 

> [!IMPORTANT] 
> Du måste beviljas åtkomst till Azure NetApp Files-tjänsten.  Information om hur du begär åtkomst till tjänsten finns på [sidan för att skicka in en väntelista för Azure NetApp-filer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Du måste vänta på ett e-postmeddelande med en officiell bekräftelse från Azure NetApp Files-teamet innan du fortsätter. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrera dig för Azure NetApp-filer och NetApp-resursprovider

> [!NOTE]
> Registreringsprocessen kan ta lite tid att slutföra.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill registrera steg med Portal öppnar du en Cloud Shell-session enligt ovan och följer de här Azure CLI-stegen:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Den här how-to-artikeln kräver Azure PowerShell-modulen Az version 2.6.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du vill kan du använda Cloud Shell-konsolen i en PowerShell-session i stället.

1. I en PowerShell-kommandotolk (eller PowerShell Cloud Shell-session) anger du den prenumeration som har vitlistats för Azure NetApp-filer:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registrera Azure Resource Provider:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I sökrutan för Azure-portalen anger du **Azure NetApp-filer** och väljer sedan **Azure NetApp-filer** i listan som visas.

      ![Välj Azure NetApp-filer](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.

     ![Skapa nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Ange följande information i fönstret Nytt NetApp-konto: 
   1. Ange **myaccount1** för kontonamnet. 
   2. Välj din prenumeration.
   3. Välj **Skapa ny** om du vill skapa en ny resursgrupp. Ange **myRG1** för resursgruppsnamnet. Klicka på **OK**. 
   4. Välj kontoplats.  

      ![Nytt netappkontofönster](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Fönstret Resursgrupp](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Klicka på **Skapa** om du vill skapa ditt nya NetApp-konto.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Definiera några variabler så att vi kan hänvisa till dem i resten av exemplen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Se [Produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) för en lista över regioner som stöds.
    > Om du vill få det regionnamn som stöds av våra kommandoradsverktyg kan du använda`Get-AzLocation | select Location`
    >

1. Skapa en ny resursgrupp med kommandot [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Skapa Azure NetApp-filer med kommandot [New-AzAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några variabler så att vi kan hänvisa till dem i resten av exemplen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Se [Produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) för en lista över regioner som stöds.
    > Om du vill få det regionnamn som stöds av våra kommandoradsverktyg kan du använda`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Skapa en ny resursgrupp med kommandot [az group create:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Skapa Azure NetApp Files-konto med kommandot [az netappfiles-konto skapa:](/cli/azure/netappfiles/account#az-netappfiles-account-create)
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj ditt NetApp-konto **(myaccount1)** från azure netapp-filhanteringsbladet .

    ![Välj NetApp-konto](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Klicka på **Kapacitetspooler**i Azure NetApp-filhanteringsbladet för ditt NetApp-konto .

    ![Klicka på Kapacitetspooler](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klicka på **+ Lägg till pooler**. 

    ![Klicka på Lägg till pooler](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Ge information om kapacitetspoolen: 
    1. Ange **mypool1** som poolnamn.
    2. Välj **Premium** för servicenivån. 
    3. Ange **4 (TiB)** som poolstorlek. 

5. Klicka på **OK**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Definiera några nya variabler för framtida referens

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Skapa en ny kapacitetspool med hjälp av [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några nya variabler för framtida referens

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Skapa en ny kapacitetspool med hjälp av [az netappfiles-poolen skapa](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Skapa NFS-volym för Azure NetApp-filer

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klicka på **Volymer**från azure netapp-filhanteringsbladet för ditt NetApp-konto .

    ![Klicka på Volymer](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klicka på **+ Lägg till volymen**.

    ![Klicka på Lägg till volymer](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Ange information för volymen i fönstret Skapa en volym: 
   1. Ange **myvol1** som volymnamn. 
   2. Välj din kapacitetspool **(mypool1).**
   3. Använd standardvärdet för kvot. 
   4. Under virtuellt nätverk klickar du på **Skapa ny** för att skapa ett nytt virtuellt Azure-nätverk (Vnet).  Fyll sedan i följande information:
       * Ange **myvnet1** som Vnet-namn.
       * Ange ett adressutrymme för din inställning, till exempel 10.7.0.0/16
       * Ange **myANFsubnet** som undernätsnamn.
       * Ange undernätsadressintervallet, till exempel 10.7.0.0/24. Du kan inte dela det dedikerade undernätet med andra resurser.
       * Välj **Microsoft.NetApp/volymer** för undernätsdelegering.
       * Klicka på **OK** för att skapa det virtuella nätverket.
   5. I undernätet väljer du det nyskapade Vnet **(myvnet1**) som ombudsundernät.

      ![Skapa ett volymfönster](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Skapa fönstret Virtuellt nätverk](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klicka på **Protokoll**och slutför sedan följande åtgärder: 
    * Välj **NFS** som protokolltyp för volymen.  
    * Ange **myfilepath1** som den filsökväg som ska användas för att skapa exportsökvägen för volymen.  
    * Välj NFS-versionen **(NFSv3** eller **NFSv4.1)** för volymen.  
      Se [överväganden](azure-netapp-files-create-volumes.md#considerations) och [bästa praxis](azure-netapp-files-create-volumes.md#best-practice) om NFS-versioner. 
      
  ![Ange NFS-protokoll för snabbstart](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klicka på **Granska + skapa**.

    ![Granska och skapa fönster](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Granska informationen för volymen och klicka sedan på **Skapa**.  
    Den skapade volymen visas i bladet Volymer.

    ![Skapad volym](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Skapa en undernätsdelegering till "Microsoft.NetApp/volumes" med kommandot [New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Skapa en undernätskonfiguration med kommandot [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Skapa det virtuella nätverket med kommandot [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Skapa volymen med kommandot [New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera vissa variabler för senare användning.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Skapa virtuellt nätverk utan undernät med kommandot [az network vnet create.](/cli/azure/network/vnet#az-network-vnet-create)
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Skapa ett delegerat undernät med kommandot [az network vnet undernät.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Skapa volymen med hjälp av kommandot [az netappfiles volym create.](/cli/azure/netappfiles/volume#az-netappfiles-volume-create)
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Rensa resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp är oåterkallelig.  

> [!IMPORTANT]
> Alla resurser inom resursgrupperna tas bort permanent och kan inte ångras. 

1. I sökrutan för Azure-portalen anger du **Azure NetApp-filer** och väljer sedan **Azure NetApp-filer** i listan som visas.

2. Klicka på den resursgrupp (myRG1) som du vill ta bort i listan över prenumerationer. 

    ![Navigera till resursgrupper](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Klicka på **Ta bort resursgrupp**på resursgruppssidan.

    ![Ta bort resursgrupp](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Ett fönster öppnas och visar en varning om vilka resurser som tas bort med resursgruppen.

4. Ange namnet på resursgruppen (myRG1) för att bekräfta att du vill ta bort resursgruppen och alla resurser permanent i den och klicka sedan på **Ta bort**.

    ![Ta bort resursgrupp](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp är oåterkallelig.  

> [!IMPORTANT]
> Alla resurser inom resursgrupperna tas bort permanent och kan inte ångras.

1. Ta bort resursgruppen med kommandot [Ta bort AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

När du är klar och om du vill kan du ta bort resursgruppen. Åtgärden att ta bort en resursgrupp är oåterkallelig.  

> [!IMPORTANT]
> Alla resurser inom resursgrupperna tas bort permanent och kan inte ångras.

1. Ta bort resursgruppen med kommandot [az group delete.](/cli/azure/group#az-group-delete)
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Hantera volymer med hjälp av Azure NetApp-filer](azure-netapp-files-manage-volumes.md)  
