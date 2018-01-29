---
title: "Skapa ett virtuellt Azure-nätverk peering - olika distributionsmodeller - samma prenumeration | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk peering mellan virtuella nätverk som skapats via olika Azure distributionsmodeller som finns i samma Azure-prenumerationen."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 033d696f0d219fef66b4acd523f28a35afcf9929
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Skapa ett virtuellt nätverk peering - olika distributionsmodeller, samma prenumeration 

I kursen får du lära dig att skapa ett virtuellt nätverk peering mellan virtuella nätverk som skapats via olika distributionsmodeller. Båda virtuella nätverk finns i samma prenumeration. Peering två virtuella nätverk gör resurser i olika virtuella nätverk för att kommunicera med varandra med samma bandbredd och svarstid som om resurserna som fanns i samma virtuella nätverk. Lär dig mer om [virtuella nätverk peering](virtual-network-peering-overview.md). 

Stegen för att skapa ett virtuellt nätverk som peering är olika beroende på om de virtuella nätverken är på samma eller olika prenumerationer och som [Azure distributionsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar ett virtuellt nätverk peering i andra scenarier genom att klicka på scenario i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](virtual-network-create-peering.md) |samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Annorlunda|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Annorlunda|

Att går inte skapa ett virtuellt nätverk som peering mellan två virtuella nätverk som distribuerats via den klassiska distributionsmodellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta virtuella nätverk. 

Den här självstudiekursen peers virtuella nätverk i samma region. Möjligheten att peer-virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Utför stegen i [registrera sig för globalt virtuella nätverk peering](#register) innan du försöker att peer-virtuella nätverk i olika regioner eller peering misslyckas. Möjligheten att ansluta virtuella nätverk i olika regioner med en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) är allmänt tillgänglig och kräver ingen registrering.

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI) Azure [PowerShell](#powershell), eller en [Azure Resource Manager-mall](#template)att skapa ett virtuellt nätverk som peering. Klicka på någon av föregående verktyget länkar att gå direkt till steg för att skapa ett virtuellt nätverk peering verktyget dina val.

## <a name="create-peering---azure-portal"></a>Skapa peering - Azure-portalen

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
4. Klicka på **+ Ny**. I den **söka Marketplace** skriver *för virtuella nätverk*. Klicka på **för virtuella nätverk** när den visas i sökresultaten. 
5. I den **för virtuella nätverk** bladet väljer **klassiska** i den **Välj en distributionsmodell** rutan och klicka på **skapa**.
6. I den **skapa virtuellt nätverk** bladet anger, eller Välj värden för följande inställningar och sedan klickar du på **skapa**:
    - **Name**: *myVnet2*
    - **Adressutrymmet**: *10.1.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.1.0.0/24*
    - **Prenumerationen**: väljer din prenumeration
    - **Resursgruppen**: Välj **använda befintliga** och välj *myResourceGroup*
    - **Plats**: *östra USA*
7. I den **söka resurser** rutan överst i portalen, typen *myResourceGroup*. Klicka på **myResourceGroup** när den visas i sökresultaten. Ett blad som visas för den **myresourcegroup** resursgruppen. Resursgruppen innehåller två virtuella nätverk som skapats i föregående steg.
8. Klicka på **myVNet1**.
9. I den **myVnet1** bladet som visas, klickar du på **Peerkopplingar** från lodräta listan till vänster på bladet.
10. I den **myVnet1 - Peerkopplingar** bladet som visas, klickar du på **+ Lägg till**
11. I den **Lägg till peering** bladet som visas anger, eller Välj följande alternativ och klicka på **OK**:
     - **Namnet**: *myVnet1ToMyVnet2*
     - **Virtuellt nätverk distributionsmodell**: Välj **klassiska**. 
     - **Prenumerationen**: väljer din prenumeration
     - **Virtuellt nätverk**: Klicka på **Välj ett virtuellt nätverk**, klicka på **myVnet2**.
     - **Tillåt åtkomst till virtuella nätverk:** se till att **aktiverad** är markerad.
    Inga andra inställningar används i den här kursen. Mer information om alla peering inställningar, läsa [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
12. När du klickar på **OK** i föregående steg i **Lägg till peering** blad stängs och du ser den **myVnet1 - Peerkopplingar** bladet igen. Efter några sekunder visas peering du skapat i bladet. **Ansluten** ingår i den **PEERING STATUS** kolumnen för den **myVnet1ToMyVnet2** peering du skapat.

    Peering upprättas. Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
14. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering - Azure CLI

1. [Installera](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 1.0 att skapa virtuella nätverk (klassiska).
2. Öppna en kommandosession och logga in på Azure med hjälp av `azure login` kommando.
3. Kör CLI i Service Management-läge genom att ange den `azure config mode asm` kommando.
4. Ange följande kommando för att skapa virtuella nätverk (klassiska):
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Du kan använda CLI 1.0 eller 2.0 ([installera](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). I den här självstudiekursen används CLI 2.0 för att skapa det virtuella nätverket (Resource Manager), eftersom 2.0 måste användas för att skapa peering. Kör följande bash CLI skript från din lokala dator med CLI 2.0.4 eller senare installerat. Alternativen på körs bash CLI-skript på Windows-klient, finns [kör Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du kan också köra skriptet med Shell för Azure-molnet. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på den **prova** knappen i skriptet som anropar ett moln-gränssnitt som loggar du kan logga in på ditt Azure-konto med. Om du vill köra skriptet klickar du på den **kopiera** knappen och klistra in innehållet i molnet gränssnittet, tryck på `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Skapa ett virtuellt nätverk peering mellan de två virtuella nätverk som skapats via olika distributionsmodeller. Kopiera följande skript till en textredigerare på datorn. Ersätt `<subscription id>` med ditt prenumerations-Id. Om du inte vet ditt prenumerations-Id, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-Id. Klistra in det ändrade skriptet i i CLI-sessionen och tryck sedan på `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in den i CLI-sessionen och tryck sedan på `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Utdata visar **ansluten** i den **PeeringState** kolumn. 

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
9. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-cli) i den här artikeln.

## <a name="powershell"></a>Skapa peering - PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) och [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduler. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in i Azure genom att ange kommandot `Add-AzureAccount`.
4. Du måste skapa en ny eller ändra en befintlig, network-konfigurationsfil om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell. Lär dig hur du [exportera, uppdatera och importera konfigurationsfiler för nätverket](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite** element för det virtuella nätverket används i den här kursen:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importera en konfigurationsfil för ändrade nätverket kan orsaka ändringar av befintliga virtuella nätverk (klassiskt) i din prenumeration. Se till att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från prenumerationen. 
5. Logga in till Azure för att skapa det virtuella nätverket (Resource Manager) genom att ange den `login-azurermaccount` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. Finns det [behörigheter](#permissions) i den här artikeln för information.
6. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera skriptet, klistrar in den i PowerShell och tryck sedan på `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Skapa ett virtuellt nätverk peering mellan de två virtuella nätverk som skapats via olika distributionsmodeller. Kopiera följande skript till en textredigerare på datorn. Ersätt `<subscription id>` med ditt prenumerations-Id. Om du inte vet ditt prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Om du vill köra skriptet kopiera ändrade skriptet från textredigerare och sedan högerklicka i PowerShell-sessionen och tryck sedan på `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in den i PowerShell-sessionen och tryck sedan på `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Utdata visar **ansluten** i den **PeeringState** kolumn.

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
10. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-powershell) i den här artikeln.
 
## <a name="permissions"></a>Behörigheter

De konton som du använder för att skapa ett virtuellt nätverk som peering måste ha rollen eller nödvändiga behörigheter. Till exempel om du peering två virtuella nätverk som heter myVnet1 och myVnet2, måste ditt konto tilldelas rollen följande minsta eller behörigheter för varje virtuellt nätverk:
    
|Virtuellt nätverk|Distributionsmodell|Roll|Behörigheter|
|---|---|---|---|
|myVnet1|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Gäller inte|
|myVnet2|Resource Manager|[Nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisk|[Klassisk nätverksdeltagare](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Lär dig mer om [inbyggda roller](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och tilldela särskilda behörigheter till [anpassade roller](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (enbart Resource Manager).

## <a name="delete"></a>Ta bort resurser
När du har läst den här självstudiekursen, kanske du vill ta bort de resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Skriv i sökrutan portal **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroup**, och klicka sedan på **ta bort**.

### <a name="delete-cli"></a>Azure CLI

1. Använda Azure CLI 2.0 för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Använd Azure CLI 1.0 att ta bort det virtuella nätverket (klassisk) med följande kommandon:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Om du vill ta bort det virtuella nätverket (klassiskt) med PowerShell, måste du ändra en befintlig konfigurationsfil i nätverket. Lär dig hur du [exportera, uppdatera och importera konfigurationsfiler för nätverket](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite element för det virtuella nätverket används i den här kursen:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importera en konfigurationsfil för ändrade nätverket kan orsaka ändringar av befintliga virtuella nätverk (klassiskt) i din prenumeration. Se till att du bara tar bort det tidigare virtuella nätverket och att du inte ändra eller ta bort andra befintliga virtuella nätverk från prenumerationen. 

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
