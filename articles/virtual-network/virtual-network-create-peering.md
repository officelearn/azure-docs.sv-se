---
title: "Skapa ett virtuellt Azure-nätverk peering - Resource Manager - samma prenumeration | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk peering mellan virtuella nätverk som skapats via Resource Manager som finns i samma Azure-prenumerationen."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Skapa ett virtuellt nätverk peering - hanteraren för filserverresurser, samma prenumeration

I kursen får du lära dig att skapa ett virtuellt nätverk peering mellan virtuella nätverk som skapats via Resource Manager. Båda virtuella nätverk finns i samma prenumeration. Peering två virtuella nätverk gör resurser i olika virtuella nätverk för att kommunicera med varandra med samma bandbredd och svarstid som om resurserna som fanns i samma virtuella nätverk. Lär dig mer om [virtuella nätverk peering](virtual-network-peering-overview.md). 

Stegen för att skapa ett virtuellt nätverk som peering är olika beroende på om de virtuella nätverken är på samma eller olika prenumerationer och som [Azure distributionsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar ett virtuellt nätverk peering i andra scenarier genom att klicka på scenario i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Annorlunda|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Annorlunda|

Att går inte skapa ett virtuellt nätverk som peering mellan två virtuella nätverk som distribuerats via den klassiska distributionsmodellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta virtuella nätverk. 

Den här självstudiekursen peers virtuella nätverk i samma region. Möjligheten att peer-virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Utför stegen i [registrera sig för globalt virtuella nätverk peering](#register) innan du försöker att peer-virtuella nätverk i olika regioner eller peering misslyckas. Möjligheten att ansluta virtuella nätverk i olika regioner med en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) är allmänt tillgänglig och kräver ingen registrering.

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI) Azure [PowerShell](#powershell), eller en [Azure Resource Manager-mall](#template)att skapa ett virtuellt nätverk som peering. Klicka på någon av föregående verktyget länkar att gå direkt till steg för att skapa ett virtuellt nätverk peering verktyget dina val.

## <a name="portal"></a>Skapa peering - Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com). Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. Finns det [behörigheter](#permissions) i den här artikeln för information.
2. Klicka på **+ ny**, klickar du på **nätverk**, klicka på **för virtuella nätverk**.
3. I den **skapa virtuellt nätverk** bladet anger, eller Välj värden för följande inställningar och sedan klickar du på **skapa**:
    - **Namnet**: *myVnet1*
    - **Adressutrymmet**: *10.0.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.0.0.0/24*
    - **Prenumerationen**: väljer din prenumeration
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroup*
    - **Plats**: *östra USA*
4. Slutför steg 2 – 3 igen att ange följande värden i steg 3:
    - **Name**: *myVnet2*
    - **Adressutrymmet**: *10.1.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.1.0.0/24*
    - **Prenumerationen**: väljer din prenumeration
    - **Resursgruppen**: Välj **använda befintliga** och välj *myResourceGroup*
    - **Plats**: *östra USA*
5. I den **söka resurser** rutan överst i portalen, typen *myResourceGroup*. Klicka på **myResourceGroup** när den visas i sökresultaten. Ett blad som visas för den **myresourcegroup** resursgruppen. Resursgruppen innehåller två virtuella nätverk som skapats i föregående steg.
6. Klicka på **myVNet1**.
7. I den **myVnet1** bladet som visas, klickar du på **Peerkopplingar** från lodräta listan till vänster på bladet.
8. I den **myVnet1 - Peerkopplingar** bladet som visas, klickar du på **+ Lägg till**
9. I den **Lägg till peering** bladet som visas anger, eller Välj följande alternativ och klicka på **OK**:
     - **Namnet**: *myVnet1ToMyVnet2*
     - **Virtuellt nätverk distributionsmodell**: Välj **Resource Manager**. 
     - **Prenumerationen**: väljer din prenumeration
     - **Virtuellt nätverk**: Klicka på **Välj ett virtuellt nätverk**, klicka på **myVnet2**.
     - **Tillåt åtkomst till virtuella nätverk:** se till att **aktiverad** är markerad.
    Inga andra inställningar används i den här kursen. Mer information om alla peering inställningar, läsa [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
10. När du klickar på **OK** i föregående steg i **Lägg till peering** blad stängs och du ser den **myVnet1 - Peerkopplingar** bladet igen. Efter några sekunder visas peering du skapat i bladet. **Initierade** ingår i den **PEERING STATUS** kolumnen för den **myVnet1ToMyVnet2** peering du skapat. Du har peerkoppla Vnet1 till Vnet2, men du måste nu peer-myVnet2 till myVnet1. Peering måste skapas i båda riktningarna att aktivera resurser i de virtuella nätverken för att kommunicera med varandra.
11. Slutför steg 5 – 10 igen för myVnet2. Namnge peering *myVnet2ToMyVnet1*.
12. Några sekunder när du klickar på **OK** att skapa peering för MyVnet2, den **myVnet2ToMyVnet1** peering du precis har skapat visas med **ansluten** i den  **PEERING STATUS** kolumn.
13. Slutför steg 5 – 7 igen för MyVnet1. Den **PEERING STATUS** för den **myVnet1ToVNet2** peering är nu också **ansluten**. Peering har skapats efter att du ser **ansluten** i den **PEERING STATUS** för både virtuella nätverk i peering.
14. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
15. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete-portal) i den här artikeln.

Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Skapa peering - Azure CLI

Följande skript:

- Kräver Azure CLI version 2.0.4 eller senare. Om du vill ta reda på vilken version du kör den `az --version` kommando. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fungerar i ett Bash-gränssnitt. Alternativen på Azure CLI skriptkörning på Windows-klient kan se [kör Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Du kan använda Azure Cloud Shell istället för att installera CLI och dess beroenden. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på den **prova** knappen i skriptet som anropar ett moln-gränssnitt som loggar du kan logga in på ditt Azure-konto med. Om du vill köra skriptet klickar du på den **kopiera** knappen och klistra in innehållet i molnet-gränssnittet.

1. Skapa en resursgrupp och två virtuella nätverk.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Skapa ett virtuellt nätverk peering mellan de två virtuella nätverk.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. När skriptet körs, granska peerkopplingar för varje virtuellt nätverk. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Kör det föregående kommandot igen, ersätter *myVnet1* med *myVnet2*. Utdata från både kommandon visar **ansluten** i den **PeeringState** kolumn.

     Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
5. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-cli) i den här artikeln.


## <a name="powershell"></a>Skapa peering - PowerShell

1. Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session genom att gå till **Start**, ange **powershell** och sedan klicka på **PowerShell**.
3. I PowerShell loggar du in i Azure genom att ange kommandot `login-azurermaccount`. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. Finns det [behörigheter](#permissions) i den här artikeln för information.
4. Skapa en resursgrupp och två virtuella nätverk. Kopiera följande skript för att köra skriptet, klistrar in den i PowerShell och tryck sedan på `Enter` efter den sista raden visas på skärmen:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Skapa ett virtuellt nätverk peering mellan de två virtuella nätverk. Kopiera följande skript, klistra in i PowerShell och tryck sedan på `Enter` efter den sista raden visas på skärmen:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Kopiera följande kommando om du vill granska undernät för det virtuella nätverket, klistra in i till PowerShell och tryck sedan på `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Kör det föregående kommandot igen, ersätter *myVnet1* med *myVnet2*. Utdata från både kommandon visar **ansluten** i den **PeeringState** kolumn.
7. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
8. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-powershell) i den här artikeln.

Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Skapa peering - Resource Manager-mall

1. Referens [skapa ett virtuellt nätverk som peering](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager-mall. Anvisningar medföljer mallen för distribution av mallen med hjälp av Azure-portalen, PowerShell eller Azure CLI. Logga in beroende på vilket som verktyget du väljer att distribuera mallen med ett konto som har behörighet att skapa ett virtuellt nätverk som peering. Finns det [behörigheter](#permissions) i den här artikeln för information.
2. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
3. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete) i den här artikeln använder Azure-portalen, PowerShell eller Azure CLI.

## <a name="permissions"></a>Behörigheter

De konton som du använder för att skapa ett virtuellt nätverk som peering måste ha rollen eller nödvändiga behörigheter. Till exempel om du peering två virtuella nätverk som heter VNet1 och VNet2, måste ditt konto tilldelas rollen följande minsta eller behörigheter för varje virtuellt nätverk:
    
|Virtuellt nätverk|Roll|Behörigheter|
|---|---|---|
|VNet1|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Lär dig mer om [inbyggda roller](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och tilldela särskilda behörigheter till [anpassade roller](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (enbart Resource Manager).

## <a name="delete"></a>Ta bort resurser
När du har läst den här självstudiekursen, kanske du vill ta bort de resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Skriv i sökrutan portal **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroup**, och klicka sedan på **ta bort**.

### <a name="delete-cli"></a>Azure CLI

Ange följande kommando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Ange följande kommando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="register"></a>Registrera sig för globalt virtuella nätverk peering förhandsversion

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/en-us/updates/?product=virtual-network) för tillgängliga regioner. To-peer-virtuella nätverk över regioner, måste du först registrera för förhandsversionen av genom att utföra följande steg (inom prenumerationen varje virtuella nätverket som du vill peer) med hjälp av Azure PowerShell eller Azure CLI:

### <a name="powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta PowerShell-sessionen och logga in på Azure med hjälp av `Login-AzureRmAccount` kommando.
3. Registrera prenumerationen som varje virtuellt nätverk som du vill peer gäller i förhandsgranskningen genom att ange följande kommandon:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Inte slutföra stegen i portalen, Azure CLI, PowerShell eller Resource Manager template-sektioner i den här artikeln förrän den **RegistrationState** utdata efter att ange föregående kommando är **registrerad**  för båda prenumerationer.

### <a name="azure-cli"></a>Azure CLI

1. [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Se till att du använder version 2.0.18 eller senare av Azure CLI genom att ange den `az --version` kommando. Om du inte installera den senaste versionen.
3. Logga in på Azure med den `az login` kommando.
4. Registrera dig för förhandsversionen genom att ange följande kommandon:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Inte slutföra stegen i portalen, Azure CLI, PowerShell eller Resource Manager template-sektioner i den här artikeln förrän den **RegistrationState** utdata efter att ange föregående kommando är **registrerad**  för båda prenumerationer.

## <a name="next-steps"></a>Nästa steg

- Väl bekanta dig med viktiga [peering begränsningar för virtuellt nätverk och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar ett virtuellt nätverk peering för produktion använder.
- Lär dig mer om alla [peering inställningar för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och eker nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med virtuella nätverk peering.
