---
title: Snabb start – konfigurera Azure NetApp Files och skapa en NFS-volym | Microsoft Docs
description: Snabb start – beskriver hur du snabbt konfigurerar Azure NetApp Files och skapar en volym.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 06ebe8ed22453289fa02c238d9b2e6ef13191f2a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888550"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Snabb start: Konfigurera Azure NetApp Files och skapa en NFS-volym 

Den här artikeln visar hur du snabbt konfigurerar Azure NetApp Files och skapar en volym. 

I den här snabb starten ska du konfigurera följande objekt:

- Registrering för Azure NetApp Files-och NetApp Resource Provider
- Ett NetApp-konto
- En kapacitets grupp
- En NFS-volym för Azure NetApp Files

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar 

> [!IMPORTANT] 
> Du måste beviljas åtkomst till Azure NetApp Filess tjänsten.  Information om hur du begär åtkomst till tjänsten finns på sidan för att [skicka Azure NetApp Files Waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Du måste vänta en officiell bekräftelse via e-post från Azure NetApp Files-teamet innan du fortsätter. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrera dig för Azure NetApp Files-och NetApp Resource Provider

> [!NOTE]
> Registreringsprocessen kan ta lite tid att slutföra.
>

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

För registrerings steg med portalen öppnar du en Cloud Shell-session som anges ovan och följer dessa Azure CLI-steg:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Den här instruktions artikeln kräver Azure PowerShell module AZ version 2.6.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du vill kan du använda Cloud Shell-konsolen i en PowerShell-session i stället.

1. I en PowerShell-kommandotolk (eller PowerShell Cloud Shell-session) anger du den prenumeration som har vit listas för Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registrera Azure-resurs leverantören:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Skapa ett NetApp-konto

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Skriv **Azure NetApp Files** i sökrutan Azure Portal och välj sedan **Azure NetApp Files** i listan som visas.

      ![Välj Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Klicka på **+ Lägg till** för att skapa ett nytt NetApp-konto.

     ![Skapa ett nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. I fönstret nytt NetApp-konto anger du följande information: 
   1. Ange **myaccount1** som konto namn. 
   2. Välj din prenumeration.
   3. Välj **Skapa ny** för att skapa en ny resurs grupp. Ange **myRG1** som resurs grupps namn. Klicka på **OK**. 
   4. Välj din konto plats.  

      ![Fönstret nytt NetApp-konto](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Resurs grupps fönster](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Skapa ditt nya NetApp-konto genom att klicka på **skapa** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definiera vissa variabler så att vi kan referera till dem i resten av exemplen:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > En lista över regioner som stöds finns i avsnittet [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Använd `Get-AzLocation | select Location` för att hämta regions namnet som stöds av våra kommando rads verktyg.
    >

1. Skapa en ny resurs grupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Skapa Azure NetApp Files-konto med kommandot [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera vissa variabler så att vi kan referera till dem i resten av exemplen:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > En lista över regioner som stöds finns i avsnittet [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Använd `az account list-locations -query "[].{Region:name}" --out table` för att hämta regions namnet som stöds av våra kommando rads verktyg.
    >

2. Skapa en ny resurs grupp med kommandot [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Skapa Azure NetApp Files konto med kommandot [AZ netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Konfigurera en kapacitetspool

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Från bladet Azure NetApp Files hantering väljer du ditt NetApp-konto (**myaccount1**).

    ![Välj NetApp-konto](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Klicka på **kapacitets grupper**från bladet Azure NetApp Files hantering i NetApp-kontot.

    ![Klicka på kapacitets grupper](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klicka på **+ Lägg till pooler**. 

    ![Klicka på Lägg till pooler](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Ange information för kapacitets gruppen: 
    1. Ange **mypool1** som poolnamn.
    2. Välj **Premium** för Service nivån. 
    3. Ange **4 (TIB)** som pool-storlek. 

5. Klicka på **OK**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definiera några nya variabler för framtida bruk

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Skapa en ny kapacitets pool med hjälp av kommandot [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några nya variabler för framtida bruk

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Skapa en ny kapacitets pool med hjälp av [AZ netappfiles pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

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

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Skapa NFS-volym för Azure NetApp Files

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Från bladet Azure NetApp Files hantering i ditt NetApp-konto klickar du på **volymer**.

    ![Klicka på volymer](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klicka på **+ Lägg till volymen**.

    ![Klicka på Lägg till volymer](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. I fönstret Skapa en volym anger du information för volymen: 
   1. Ange **myvol1** som volym namn. 
   2. Välj din Capacity pool (**mypool1**).
   3. Använd standardvärdet för kvot. 
   4. Under virtuellt nätverk klickar du på **Skapa nytt** för att skapa ett nytt virtuellt Azure-nätverk (VNet).  Fyll sedan i följande information:
       * Ange **myvnet1** som VNet-namn.
       * Ange ett adress utrymme för inställningen, till exempel 10.7.0.0/16
       * Ange **myANFsubnet** som under näts namn.
       * Ange adress intervall för under nätet, till exempel 10.7.0.0/24. Du kan inte dela det dedikerade under nätet med andra resurser.
       * Välj **Microsoft. NetApp/volymer** för under näts delegering.
       * Klicka på **OK** för att skapa VNet.
   5. I undernät väljer du det nyligen skapade VNet (**myvnet1**) som delegerat undernät.

      ![Skapa ett volym fönster](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Fönstret Skapa virtuellt nätverk](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klicka på **protokoll**och utför följande åtgärder: 
    * Välj **NFS** som protokoll typ för volymen.  
    * Ange **myfilepath1** som den fil Sök väg som ska användas för att skapa export Sök vägen för volymen.  
    * Välj NFS-version (**NFSv3** eller **nfsv 4.1**) för volymen.  
      Se [överväganden](azure-netapp-files-create-volumes.md#considerations) och [bästa praxis](azure-netapp-files-create-volumes.md#best-practice) om NFS-versioner. 
      
> [!IMPORTANT] 
> Åtkomst till NFSv 4.1-funktionen kräver vit listning.  Skicka en begäran till <anffeedback@microsoft.com>för att begära vit listning. 

  ![Ange NFS-protokoll för snabb start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klicka på **Granska + skapa**.

    ![Granska och skapa fönster](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Granska informationen för volymen och klicka sedan på **skapa**.  
    Den skapade volymen visas på bladet volymer.

    ![Volym skapad](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en under näts delegering till "Microsoft. NetApp/Volumes" med kommandot [New-AzDelegation](/powershell/module/az.network/new-azdelegation) .

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Skapa en under näts konfiguration med kommandot [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Skapa det virtuella nätverket med kommandot [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Skapa volymen med kommandot [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) .
   
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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definiera några variabler för senare användning.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Skapa ett virtuellt nätverk utan undernät med hjälp av kommandot [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create) .
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Skapa ett delegerat undernät genom att använda [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) kommandot.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Skapa volymen med kommandot [AZ netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) .
   
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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

När du är färdig och om du vill kan du ta bort resurs gruppen. Åtgärden att ta bort en resurs grupp går inte att ångra.  

> [!IMPORTANT]
> Alla resurser i resurs grupperna tas bort permanent och kan inte återställas. 

1. Skriv **Azure NetApp Files** i sökrutan Azure Portal och välj sedan **Azure NetApp Files** i listan som visas.

2. I listan över prenumerationer klickar du på resurs gruppen (myRG1) som du vill ta bort. 

    ![Navigera till resurs grupper](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. På sidan resurs grupp klickar du på **ta bort resurs grupp**.

    ![Ta bort resursgrupp](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Ett fönster öppnas och visar en varning om vilka resurser som tas bort med resursgruppen.

4. Ange namnet på resurs gruppen (myRG1) för att bekräfta att du vill ta bort resurs gruppen och alla resurser i den permanent och klicka sedan på **ta bort**.

    ![Ta bort resursgrupp](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

När du är färdig och om du vill kan du ta bort resurs gruppen. Åtgärden att ta bort en resurs grupp går inte att ångra.  

> [!IMPORTANT]
> Alla resurser i resurs grupperna tas bort permanent och kan inte återställas.

1. Ta bort resurs grupp med kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

När du är färdig och om du vill kan du ta bort resurs gruppen. Åtgärden att ta bort en resurs grupp går inte att ångra.  

> [!IMPORTANT]
> Alla resurser i resurs grupperna tas bort permanent och kan inte återställas.

1. Ta bort resurs grupp med kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) .
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Hantera volymer med hjälp av Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
