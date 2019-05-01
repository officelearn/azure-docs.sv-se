---
title: Skapa ett Azure vnet-peering - olika distributionsmodeller – samma prenumeration | Microsoft Docs
description: Lär dig hur du skapar en virtuell nätverkspeering mellan virtuella nätverk som skapats via olika Azure distributionsmodeller som finns i samma Azure-prenumeration.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud;anavin
ms.openlocfilehash: 56474ee56051c3b0b7482e81b0174b7945537654
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694716"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Skapa en virtuell nätverkspeering - olika distributionsmodeller, samma prenumeration

I den här självstudien får du lära dig att skapa en virtuell nätverkspeering mellan virtuella nätverk som skapats via olika distributionsmodeller. Båda virtuella nätverken finnas i samma prenumeration. Peering två virtuella nätverk kan resurser i olika virtuella nätverk kan kommunicera med varandra med samma bandbredd och latens som om resurserna fanns i samma virtuella nätverk. Läs mer om [peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverkspeering är olika, beroende på om de virtuella nätverken är i samma, eller olika, prenumerationer och som [Azure-distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverkspeering i andra scenarier genom att klicka på scenario i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Both Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Both Resource Manager](create-peering-different-subscriptions.md) |Annorlunda|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Annorlunda|

Att går inte skapa en virtuell nätverkspeering mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Om du vill ansluta virtuella nätverk som har både skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta virtuella nätverk.

Den här självstudien peerar virtuella nätverk i samma region. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekanta dig med den [peering krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints) innan peerkoppla virtuella nätverk.

Du kan använda Azure-portalen, Azure [kommandoradsgränssnittet](#cli) (CLI), Azure [PowerShell](#powershell), eller en Azure Resource Manager-mall för att skapa vnet-peering. Klicka på någon av föregående verktyget länkarna och gå direkt till stegen för att skapa en virtuell nätverkspeering med ditt val.

## <a name="create-peering---azure-portal"></a>Skapa peering - Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com). Du loggar in med kontot måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klicka på **+ ny**, klickar du på **nätverk**, klicka sedan på **virtuellt nätverk**.
3. I den **skapa virtuellt nätverk** bladet anger, eller värden för följande inställningar och sedan klicka på **skapa**:
    - **Name**: *myVnet1*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resursgrupp**: Välj **Skapa nytt** och ange *myResourceGroup*
    - **Plats**: *USA, östra*
4. Klicka på **+ Ny**. I den **Sök på Marketplace** skriver *virtuellt nätverk*. Klicka på **virtuellt nätverk** när den visas i sökresultaten.
5. I den **virtuellt nätverk** bladet väljer **klassiska** i den **Välj en distributionsmodell** rutan och klicka sedan på **skapa**.
6. I den **skapa virtuellt nätverk** bladet anger, eller värden för följande inställningar och sedan klicka på **skapa**:
    - **Name**: *myVnet2*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resursgrupp**: Välj **Använd befintlig** och välj *myResourceGroup*
    - **Plats**: *USA, östra*
7. I den **Sök efter resurser** högst upp i portalen, Skriv *myResourceGroup*. Klicka på **myResourceGroup** när den visas i sökresultaten. Ett blad som visas för den **myresourcegroup** resursgrupp. Resursgruppen innehåller två virtuella nätverk som skapats i föregående steg.
8. Klicka på **myVNet1**.
9. I den **myVnet1** bladet som visas, klickar du på **Peerings** i lodräta listan med alternativ på vänster sida av bladet.
10. I den **myVnet1 - Peerings** bladet som visas, klickar du på **+ Lägg till**
11. I den **Lägg till peering** bladet som visas, ange, eller Välj följande alternativ, och klicka sedan **OK**:
     - **Name**: *myVnet1ToMyVnet2*
     - **Distributionsmodell för virtuellt nätverk**:  Välj **klassiska**.
     - **Prenumeration**: Välj din prenumeration
     - **Virtuellt nätverk**:  Klicka på **Välj ett virtuellt nätverk**, klicka sedan på **myVnet2**.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktiverad** har valts.
    Inga andra inställningar som används i den här självstudien. Läs om alla inställningar för peering [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
12. När du klickar på **OK** i föregående steg, den **Lägg till peering** bladet stängs och du ser den **myVnet1 - Peerings** bladet igen. Efter några sekunder visas peer-kopplingen du har skapat i bladet. **Ansluten** visas i den **PEERING STATUS** kolumnen för den **myVnet1ToMyVnet2** peering du skapade.

    Peer-kopplingen upprättas. Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard namnmatchning i Azure för de virtuella nätverken är resurser i virtuella nätverk inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Valfritt**: Även om skapandet av virtuella datorer som inte omfattas i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
14. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i den [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering – Azure CLI

Utför följande steg med hjälp av den klassiska Azure CLI och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell, genom att bara välja de **prova** knappen i någon av följande steg eller genom att installera den [klassiskt CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) och [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) och köra kommandon på den lokala datorn.

1. Om du använder Cloud Shell, gå vidare till steg 2, eftersom Cloud Shell loggar du in automatiskt till Azure. Öppna en kommandosession och logga in på Azure med hjälp av den `azure login` kommando.
2. Köra CLI i Service Management-läge genom att ange den `azure config mode asm` kommando.
3. Ange följande kommando för att skapa det virtuella nätverket (klassisk):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Kör följande bash CLI-skript med hjälp av CLI inte klassiska CLI. Alternativen på körs bash-CLI-skript på Windows-dator, finns i [installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows).

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

5. Skapa en virtuell nätverkspeering mellan två virtuella nätverk som skapats via de olika distributionsmodeller med hjälp av CLI. Kopiera följande skript till en textredigerare på din dator. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte vet ditt prenumerations-ID, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-ID. Klistra in de ändrade skripten i i CLI-sessionen och tryck sedan på `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
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

6. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in den i din CLI-sessionen och tryck sedan på `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Utdata visar **ansluten** i den **PeeringState** kolumn.

   Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard namnmatchning i Azure för de virtuella nätverken är resurser i virtuella nätverk inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Valfritt**: Även om skapandet av virtuella datorer som inte omfattas i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
8. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-cli) i den här artikeln.

## <a name="powershell"></a>Skapa peering - PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) och [Az](https://www.powershellgallery.com/packages/Az/) moduler. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell, logga in på Azure genom att ange den `Add-AzureAccount` kommando. Du loggar in med kontot måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints).
4. Du måste skapa en ny eller ändra en befintlig, network-konfigurationsfil för att skapa ett virtuellt nätverk (klassisk) med PowerShell. Lär dig hur du [exportera, uppdatera och importera nätverkskonfigurationen](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite** elementet för det virtuella nätverket som används i den här självstudien:

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
    > Importera en ändrade nätverkskonfigurationsfil kan orsaka ändringar i befintliga virtuella nätverk (klassiskt) i din prenumeration. Kontrollera att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från din prenumeration.
5. Logga in på Azure för att skapa det virtuella nätverket (Resource Manager) genom att ange den `Connect-AzAccount` kommando. Du loggar in med kontot måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints).
6. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera skriptet, klistra in den i PowerShell och tryck sedan på `Enter`.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Skapa en virtuell nätverkspeering mellan två virtuella nätverk som skapats via olika distributionsmodeller. Kopiera följande skript till en textredigerare på din dator. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte vet ditt prenumerations-ID, ange den `Get-AzSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. För att köra skriptet, kopiera det ändrade skriptet från din textredigerare och sedan högerklicka i PowerShell-sessionen och tryck sedan på `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in den i PowerShell-sessionen och tryck sedan på `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Utdata visar **ansluten** i den **PeeringState** kolumn.

    Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard namnmatchning i Azure för de virtuella nätverken är resurser i virtuella nätverk inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Valfritt**: Även om skapandet av virtuella datorer som inte omfattas i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
10. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete"></a>Ta bort resurser

När du har slutfört den här kursen, kanske du vill ta bort alla resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. Ta bort en resursgrupp tar även bort alla resurser i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Ange i portalen sökrutan **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **Skriv RESURSGRUPPSNAMNET** anger **myResourceGroup**, och klicka sedan på **ta bort**.

### <a name="delete-cli"></a>Azure CLI

1. Använda Azure CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Använda klassiska CLI för att ta bort det virtuella nätverket (klassisk) med följande kommandon:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Om du vill ta bort det virtuella nätverket (klassisk) med PowerShell kan du ändra en befintlig konfigurationsfil för nätverk. Lär dig hur du [exportera, uppdatera och importera nätverkskonfigurationen](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite element för det virtuella nätverket som används i den här självstudien:

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
    > Importera en ändrade nätverkskonfigurationsfil kan orsaka ändringar i befintliga virtuella nätverk (klassiskt) i din prenumeration. Kontrollera att du bara ta bort det tidigare virtuella nätverket och att du inte ändra eller ta bort alla andra befintliga virtuella nätverk från din prenumeration.

## <a name="next-steps"></a>Nästa steg

- Noggrant bekanta dig med viktiga [vnet peering begränsningar och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverkspeering för produktion använder.
- Lär dig mer om alla [peering-inställningar för virtuellt nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och ekrar nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling.
