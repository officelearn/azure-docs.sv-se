---
title: Skapa en virtuell Azure-nätverks peering – olika distributionsmodeller – samma prenumeration | Microsoft-dokument
description: Lär dig hur du skapar en virtuell nätverks peering mellan virtuella nätverk som skapats via olika Azure-distributionsmodeller som finns i samma Azure-prenumeration.
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
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023267"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Skapa en virtuell nätverks peering - olika distributionsmodeller, samma prenumeration

I den här självstudien lär du dig att skapa en virtuell nätverks peering mellan virtuella nätverk som skapats genom olika distributionsmodeller. Båda virtuella nätverk finns i samma prenumeration. Peering två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svarstid som om resurserna var i samma virtuella nätverk. Läs mer om [Virtual Network peering](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks peering är olika, beroende på om de virtuella nätverken är i samma eller olika prenumerationer och vilken [Azure-distributionsmodell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas genom. Lär dig hur du skapar en virtuell nätverks peering i andra scenarier genom att klicka på scenariot från följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Olika|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Olika|

En virtuell nätverks peering kan inte skapas mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta de virtuella nätverken.

Den här självstudien peer virtuella nätverk i samma region. Du kan också peer virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peering virtuella nätverk.

Du kan använda Azure-portalen, AZURE [command-line interface](#cli) (CLI), Azure [PowerShell](#powershell)eller en Azure Resource Manager-mall för att skapa en virtuell nätverks peering. Klicka på någon av de tidigare verktygslänkarna för att gå direkt till stegen för att skapa en virtuell nätverks peering med hjälp av ditt verktyg val.

## <a name="create-peering---azure-portal"></a>Skapa peering - Azure-portal

1. Logga in på [Azure-portalen](https://portal.azure.com). Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klicka på **+ Ny**, klicka på **Nätverk**och sedan på **Virtuellt nätverk**.
3. I bladet **Skapa virtuellt nätverk** anger eller väljer du värden för följande inställningar och klickar sedan på **Skapa:**
    - **Namn**: *myVnet1*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.0.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resursgrupp:** Välj **Skapa nytt** och ange *myResourceGroup*
    - **Plats**: *Östra USA*
4. Klicka på **+ Ny**. Skriv *Virtuellt nätverk*i rutan **Sök på Marketplace** . Klicka på **Virtuellt nätverk** när det visas i sökresultaten.
5. I bladet **Virtuellt nätverk** väljer du **Klassisk** i rutan **Välj en distributionsmodell** och klickar sedan på **Skapa**.
6. I bladet **Skapa virtuellt nätverk** anger eller väljer du värden för följande inställningar och klickar sedan på **Skapa:**
    - **Namn**: *myVnet2*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.1.0.0/24*
    - **Prenumeration**: Välj din prenumeration
    - **Resursgrupp**: Välj **Använd befintliga** och välj *myResourceGroup*
    - **Plats**: *Östra USA*
7. Skriv *myResourceGroup*i rutan **Sökresurser** högst upp i portalen . Klicka på **myResourceGroup** när den visas i sökresultaten. Ett blad visas för resursgruppen **myresourcegroup.** Resursgruppen innehåller de två virtuella nätverk som skapats i tidigare steg.
8. Klicka på **myVNet1**.
9. I **myVnet1-bladet** som visas klickar du på **Peerings** från den vertikala listan med alternativ till vänster om bladet.
10. I **myVnet1 - Peerings** blad som dök upp, klicka **+ Lägg till**
11. I bladet **Lägg till peering** som visas anger eller markerar du följande alternativ och klickar sedan på **OK:**
     - **Namn**: *myVnet1ToMyVnet2*
     - **Distribuera modell för virtuellt nätverk:** Välj **klassisk.**
     - **Prenumeration**: Välj din prenumeration
     - **Virtuellt nätverk:** Klicka på **Välj ett virtuellt nätverk**och klicka sedan på **myVnet2.**
     - **Tillåt virtuell nätverksåtkomst:** Kontrollera att **Aktiverad** är markerad.
    Inga andra inställningar används i den här självstudien. Mer information om alla peering-inställningar finns i [Hantera virtuella nätverks peerings](virtual-network-manage-peering.md#create-a-peering).
12. När du har klickat **på OK** i föregående steg stängs **bladet Lägg till peering** och du ser **bladet myVnet1 - Peerings** igen. Efter några sekunder visas peering-peeringen som du skapade i bladet. **Ansluten** visas i kolumnen **PEERING STATUS** för **myVnet1ToMyVnet2-peering** som du skapade.

    Peering är nu etablerad. Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Valfritt:** Även om du skapar virtuella datorer omfattas inte av den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
14. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i avsnittet [Ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering - Azure CLI

Slutför följande steg med Azure classic CLI och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell genom att bara välja **prova-knappen** i något av följande steg, eller genom att installera den [klassiska CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) och [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) och köra kommandona på din lokala dator.

1. Om du använder Cloud Shell, hoppa till steg 2, eftersom Cloud Shell automatiskt loggar in dig på Azure. Öppna en kommandosession och logga `azure login` in på Azure med kommandot.
2. Kör CLI i servicehanteringsläge `azure config mode asm` genom att ange kommandot.
3. Ange följande kommando för att skapa det virtuella nätverket (klassisk):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Kör följande bash CLI-skript med CLI, inte den klassiska CLI. Alternativ för att köra bash CLI-skript på Windows-dator finns i [Installera Azure CLI i Windows](/cli/azure/install-azure-cli-windows).

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

5. Skapa en virtuell nätverks peering mellan de två virtuella nätverk som skapats genom de olika distributionsmodellerna med CLI. Kopiera följande skript till en textredigerare på datorn. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte känner till ditt `az account show` prenumerations-ID anger du kommandot. Värdet för **id** i utdata är ditt prenumerations-ID. Klistra in det ändrade skriptet i `Enter`cli-sessionen och tryck sedan på .

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

6. När skriptet har körs granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in det i `Enter`CLI-sessionen och tryck sedan på:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Utdata visar **Ansluten** i **peeringstate-kolumnen.**

   Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Valfritt:** Även om du skapar virtuella datorer omfattas inte av den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
8. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-cli) i den här artikeln.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering - PowerShell

1. Installera den senaste versionen av PowerShell [Azure-](https://www.powershellgallery.com/packages/Azure) och [Az-modulerna.](https://www.powershellgallery.com/packages/Az/) Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. Logga in på Azure i PowerShell `Add-AzureAccount` genom att ange kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#requirements-and-constraints).
4. Om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell måste du skapa en ny eller ändra en befintlig nätverkskonfigurationsfil. Lär dig hur du [exporterar, uppdaterar och importerar nätverkskonfigurationsfiler](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite-element** för det virtuella nätverk som används i den här självstudien:

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
    > Om du importerar en ändrad nätverkskonfigurationsfil kan det orsaka ändringar i befintliga virtuella nätverk (klassiska) i prenumerationen. Se till att du bara lägger till det tidigare virtuella nätverket och att du inte ändrar eller tar bort befintliga virtuella nätverk från din prenumeration.
5. Logga in på Azure för att skapa det virtuella `Connect-AzAccount` nätverket (Resource Manager) genom att ange kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#requirements-and-constraints).
6. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera skriptet, klistra in det i `Enter`PowerShell och tryck sedan på .

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

7. Skapa en virtuell nätverks peering mellan de två virtuella nätverk som skapats genom de olika distributionsmodellerna. Kopiera följande skript till en textredigerare på datorn. Ersätt `<subscription id>` med ditt prenumerations-ID. Om du inte känner till ditt `Get-AzSubscription` prenumerations-ID anger du kommandot för att visa det. Värdet för **ID** i den returnerade utdata är ditt prenumerations-ID. Om du vill köra skriptet kopierar du det ändrade skriptet från textredigeraren, högerklickar sedan i PowerShell-sessionen och trycker sedan på `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. När skriptet har körs granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande kommando, klistra in det i PowerShell-sessionen och tryck sedan på `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Utdata visar **Ansluten** i **peeringstate-kolumnen.**

    Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Valfritt:** Även om du skapar virtuella datorer omfattas inte av den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
10. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser

När du har slutfört den här självstudien kanske du vill ta bort de resurser som du har skapat i självstudien, så att du inte ådrar dig användningsavgifter. Om du tar bort en resursgrupp tas också alla resurser som finns i resursgruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Ange **myResourceGroup**i sökrutan för portalen . Klicka på **myResourceGroup**i sökresultaten .
2. Klicka på ikonen **Ta bort** på bladet **myResourceGroup.**
3. Om du vill bekräfta borttagningen anger du **myResourceGroup**i rutan **SKRIV RESURSGRUPPNAMN** och klickar sedan på **Ta bort**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Använd Azure CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Använd den klassiska CLI för att ta bort det virtuella nätverket (klassiskt) med följande kommandon:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Om du vill ta bort det virtuella nätverket (klassiskt) med PowerShell måste du ändra en befintlig nätverkskonfigurationsfil. Lär dig hur du [exporterar, uppdaterar och importerar nätverkskonfigurationsfiler](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite-element för det virtuella nätverk som används i den här självstudien:

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
    > Om du importerar en ändrad nätverkskonfigurationsfil kan det orsaka ändringar i befintliga virtuella nätverk (klassiska) i prenumerationen. Se till att du bara tar bort det tidigare virtuella nätverket och att du inte ändrar eller tar bort andra befintliga virtuella nätverk från din prenumeration.

## <a name="next-steps"></a>Nästa steg

- Bekanta dig med viktiga begränsningar och beteenden för [virtuella nätverks peering](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverks peering för produktionsanvändning.
- Lär dig mer om alla inställningar för [virtuell nätverks peering](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapar en hubb och talade nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med virtuell nätverks peering.
