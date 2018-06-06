---
title: Skapa ett Azure-nätverk peering - Resource Manager - olika prenumerationer | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk peering mellan virtuella nätverk som skapats via Resource Manager som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: b67b13f30538d21f1a4db9675ee7c13d999f842a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726286"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Skapa ett virtuellt nätverk peering - hanteraren för filserverresurser, olika prenumerationer 

I kursen får du lära dig att skapa ett virtuellt nätverk peering mellan virtuella nätverk som skapats via Resource Manager. Virtuella nätverk finns i olika prenumerationer. Peering två virtuella nätverk gör resurser i olika virtuella nätverk för att kommunicera med varandra med samma bandbredd och svarstid som om resurserna som fanns i samma virtuella nätverk. Lär dig mer om [virtuella nätverk peering](virtual-network-peering-overview.md). 

Stegen för att skapa ett virtuellt nätverk som peering är olika beroende på om de virtuella nätverken är på samma eller olika prenumerationer och som [Azure distributionsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar ett virtuellt nätverk peering i andra scenarier genom att välja Scenarionamn i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Annorlunda|

Att går inte skapa ett virtuellt nätverk som peering mellan två virtuella nätverk som distribuerats via den klassiska distributionsmodellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta virtuella nätverk.

Den här självstudiekursen peers virtuella nätverk i samma region. Du kan också peer-virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekanta dig med de [peering kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan peering virtuella nätverk.

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI) Azure [PowerShell](#powershell), eller en [Azure Resource Manager-mall](#template)att skapa ett virtuellt nätverk som peering. Välj någon av föregående verktyget länkar att gå direkt till steg för att skapa ett virtuellt nätverk peering verktyget dina val.

## <a name="portal"></a>Skapa peering - Azure-portalen

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut från portalen och hoppa över steg för att tilldela behörigheter för en annan för de virtuella nätverken.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som *UserA*. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
2. Välj **+ skapa en resurs**väljer **nätverk**, och välj sedan **för virtuella nätverk**.
3. Välj eller ange följande exempelvärden för följande inställningar och sedan **skapa**:
    - **Namnet**: *myVnetA*
    - **Adressutrymmet**: *10.0.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.0.0.0/24*
    - **Prenumerationen**: Välj prenumeration A.
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *östra USA*
4. I den **söka resurser** rutan överst i portalen, typen *myVnetA*. Välj **myVnetA** när den visas i sökresultaten. 
5. Välj **åtkomstkontroll (IAM)** från lodräta listan till vänster.
6. Under **myVnetA - åtkomstkontroll (IAM)** väljer **+ Lägg till**.
7. Välj **Network-deltagare** i den **rollen** rutan.
8. I den **Välj** väljer *b*, eller ange användare BS e-postadress för att söka efter den. En lista över användare som visas är från samma Azure Active Directory-klientorganisation som det virtuella nätverket som du konfigurerar peering för. Om du inte ser b, är förmodligen eftersom b i en annan Active Directory-klient än UserA. Om du vill ansluta virtuella nätverk i olika Active Directory-klienter kan du ansluta dem med en [Azure VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), i stället för ett virtuellt nätverk som peering.
9. Välj **Spara**.
10. Under **myVnetA - åtkomstkontroll (IAM)** väljer **egenskaper** från lodräta listan till vänster. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som liknar följande exempel: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Logga ut från portalen som UserA och sedan logga in som användare b.
12. Slutför steg 2 – 3, att ange eller markera följande värden i steg 3:

    - **Namnet**: *myVnetB*
    - **Adressutrymmet**: *10.1.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.1.0.0/24*
    - **Prenumerationen**: Välj prenumeration B.
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *östra USA*

13. I den **söka resurser** rutan överst i portalen, typen *myVnetB*. Välj **myVnetB** när den visas i sökresultaten.
14. Under **myVnetB**väljer **egenskaper** från lodräta listan till vänster. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som liknar följande exempel: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Välj **åtkomstkontroll (IAM)** under **myVnetB**, och sedan slutföra steg 5 – 10 för myVnetB, ange **UserA** i steg 8.
16. Logga ut från portalen som användare b och logga in som UserA.
17. I den **söka resurser** rutan överst i portalen, typen *myVnetA*. Välj **myVnetA** när den visas i sökresultaten.
18. Välj **myVnetA**.
19. Under **inställningar**väljer **Peerkopplingar**.
20. Under **myVnetA - Peerkopplingar**väljer **+ Lägg till**
21. Under **Lägg till peering**, ange, eller Välj följande alternativ och välj sedan **OK**:
     - **Namnet**: *myVnetAToMyVnetB*
     - **Virtuellt nätverk distributionsmodell**: Välj **Resource Manager**.
     - **Jag vet mitt resurs-ID**: den här kryssrutan.
     - **Resurs-ID**: Ange resurs-ID från steg 14.
     - **Tillåt åtkomst till virtuella nätverk:** se till att **aktiverad** är markerad.
    Inga andra inställningar används i den här kursen. Mer information om alla peering inställningar, läsa [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
22. Peering du skapat visas en kort vänta när du har valt **OK** i föregående steg. **Initierade** ingår i den **PEERING STATUS** kolumnen för den **myVnetAToMyVnetB** peering du skapat. Du har peerkoppla myVnetA till myVnetB, men du måste nu peer-myVnetB till myVnetA. Peering måste skapas i båda riktningarna att aktivera resurser i de virtuella nätverken för att kommunicera med varandra.
23. Logga ut från portalen som UserA och logga in som användare b.
24. Slutför steg 17-21 igen för myVnetB. Namn i steget 21 peering *myVnetBToMyVnetA*väljer *myVnetA* för **för virtuella nätverk**, och ange ID från steg 10 i den **resurs-ID** rutan.
25. Några sekunder efter att ha valt **OK** att skapa peering för myVnetB, den **myVnetBToMyVnetA** peering du precis har skapat visas med **ansluten** i **PEERING STATUS** kolumn.
26. Logga ut från portalen som användare b och logga in som UserA.
27. Slutför steg 17-19 igen. Den **PEERING STATUS** för den **myVnetAToVNetB** peering är nu också **ansluten**. Peering har skapats efter att du ser **ansluten** i den **PEERING STATUS** för både virtuella nätverk i peering. Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
29. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering - Azure CLI

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut ur Azure och ta bort rader i skriptet som skapar användaren rolltilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för användare a och b. Båda av de virtuella nätverk som du vill peer måste vara i prenumerationer som är kopplade till samma Azure Active Directory-klient.  Om du vill ansluta virtuella nätverk i olika Active Directory-klienter kan du ansluta dem med en [Azure VPN-Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), i stället för ett virtuellt nätverk som peering.

Följande skript:

- Kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fungerar i ett Bash-gränssnitt. Alternativen på Azure CLI skriptkörning på Windows-klient kan se [installerar Azure CLI på Windows](/cli/azure/install-azure-cli-windows). 

Du kan använda Azure Cloud Shell istället för att installera CLI och dess beroenden. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj den **prova** knappen i skriptet som följer, som anropar ett moln-gränssnitt som du kan logga in på kontot med. 

1. Öppna en CLI-session och logga in på Azure som användare a med den `azure login` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
2. Kopiera följande skript till en textredigerare på datorn genom att ersätta `<SubscriptionA-Id>` med ID SubscriptionA kopiera skriptet ändrade, klistra in den i din CLI-sessionen och tryck på `Enter`. Om du inte vet ditt prenumerations-Id, anger du kommandot ”Visa az konto'. Värdet för **id** i utdata är ditt prenumerations-Id.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. Logga ut från Azure som användare a med den `az logout` kommandot och sedan logga in på Azure som b. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
4. Skapa myVnetB. Kopiera skriptinnehållet i steg 2 till en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID SubscriptionB. Ändra 10.0.0.0/16 10.1.0.0/16, alla om B, och alla Bs till A. kopia skriptet ändrade klistra in den i din CLI-sessionen och tryck på `Enter`. 
5. Logga ut från Azure som b och logga in på Azure som UserA.
6. Skapa ett virtuellt nätverk peering från myVnetA till myVnetB. Kopiera följande skriptinnehåll till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID SubscriptionB. Kopiera det ändrade skriptet för att köra skriptet, klistra in den i sessionen CLI och tryck på RETUR.
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Visa peering status för myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Tillståndet är **initierade**. Den ändras till **ansluten** när du har skapat peering till myVnetA från myVnetB.

8. Logga ut UserA från Azure och logga in på Azure som b.
9. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 6 till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID: T för SubscriptionA och ändra alla på B och alla Bs till A. När du har gjort ändringarna, kopiera skriptet ändrade, klistrar in den i din CLI-sessionen och tryck på `Enter`.
10. Visa peering status för myVnetB. Kopiera skriptinnehållet i steg 7 till en textredigerare på datorn. Ändra A till B för resursgruppen och virtuella nätverksnamn kopiera skriptet, klistra in ändrade skriptet i den CLI-sessionen och tryck sedan på `Enter`. Peering tillståndet är **ansluten**. MyVnetA peering tillståndet ändras till **ansluten** när du har skapat peering från myVnetB till myVnetA. Du kan logga UserA tillbaka till Azure och fullständig steg 7 igen för att kontrollera tillståndet för peering myVnetA. 

    > [!NOTE]
    > Peering upprättas inte förrän peering tillståndet är **ansluten** för både virtuella nätverk.

11. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
12. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-cli) i den här artikeln.

Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Skapa peering - PowerShell

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut ur Azure och ta bort rader i skriptet som skapar användaren rolltilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för användare a och b. Båda av de virtuella nätverk som du vill peer måste vara i prenumerationer som är kopplade till samma Azure Active Directory-klient.  Om du vill ansluta virtuella nätverk i olika Active Directory-klienter kan du ansluta dem med en [Azure VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md), i stället för ett virtuellt nätverk som peering.

1. Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell, logga in på Azure som UserA genom att ange den `Connect-AzureRmAccount` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
4. Skapa en resursgrupp och virtuella nätverk A. kopiera följande skript för att en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID SubscriptionA. Om du inte vet ditt prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Om du vill köra skriptet kopiera skriptet ändrade, klistra in den i PowerShell och tryck sedan på `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Logga ut UserA från Azure och logga in användare b. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
6. Kopiera skriptinnehållet i steg 4 till en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID för prenumerationen B. Ändra 10.0.0.0/16 till 10.1.0.0/16. Ändra alla på B och alla Bs till A. Om du vill köra skriptet kopiera skriptet ändrade, klistra in i PowerShell och tryck sedan på `Enter`.
7. Logga ut användare b från Azure och logga in UserA.
8. Skapa peering från myVnetA till myVnetB. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID för prenumerationen B. Om du vill köra skriptet kopiera skriptet ändrade klistra in till PowerShell och tryck sedan på `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Visa peering status för myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet är **initierade**. Den ändras till **ansluten** när du ställer in peering till myVnetA från myVnetB.

10. Logga ut UserA från Azure och logga in användare b.
11. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 8 till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID för prenumerationen A och ändra alla på B och alla Bs till A. Om du vill köra skriptet kopiera skriptet ändrade, klistra in den i PowerShell och tryck sedan på `Enter`.
12. Visa peering status för myVnetB. Kopiera skriptinnehållet i steg 9 till en textredigerare på datorn. Ändra A till B för resursgruppen och virtuella nätverksnamn. Om du vill köra skriptet klistra in ändrade skriptet i PowerShell och tryck sedan på `Enter`. Tillståndet är **ansluten**. Peering tillståndet för **myVnetA** ändras till **ansluten** när du har skapat peering från **myVnetB** till **myVnetA**. Du kan logga UserA i tillbaka till Azure och fullständig steg 9 igen för att kontrollera tillståndet för peering myVnetA. 

    > [!NOTE]
    > Peering upprättas inte förrän peering tillståndet är **ansluten** för både virtuella nätverk.

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
14. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-powershell) i den här artikeln.

## <a name="template"></a>Skapa peering - Resource Manager-mall

1. Skapa ett virtuellt nätverk och tilldela rätt [behörigheter](virtual-network-manage-peering.md#permissions), utför stegen i den [Portal](#portal), [Azure CLI](#cli), eller [PowerShell](#powershell)avsnitt i den här artikeln.
2. Spara texten efter till en fil på den lokala datorn. Ersätt `<subscription ID>` med Useras prenumerations-ID. Du kan spara filen som vnetpeeringA.json, till exempel.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. Logga in på Azure som UserA och distribuera mallen med hjälp av den [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), eller [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Ange namnet på filen som du sparade exempel json-texten i steg 2 till.
4. Kopiera json exempel från steg 2 till en fil på datorn och göra ändringar i raderna som börjar med:
    - **namnet**: ändra *myVnetA/myVnetAToMyVnetB* till *myVnetB/myVnetBToMyVnetA*.
    - **ID**: ersätta `<subscription ID>` med BS prenumerations-ID och ändra *myVnetB* till *myVnetA*.
5. Fullständiga steg 3 igen, inloggad Azure som b.
6. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
7. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete) i den här artikeln använder Azure-portalen, PowerShell eller Azure CLI.

## <a name="delete"></a>Ta bort resurser
När du har läst den här självstudiekursen, kanske du vill ta bort de resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Logga in på Azure-portalen som UserA.
2. Skriv i sökrutan portal **myResourceGroupA**. I sökresultaten väljer **myResourceGroupA**.
3. Välj **Ta bort**.
4. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroupA**, och välj sedan **ta bort**.
5. Logga ut från portalen som UserA och logga in som användare b.
6. Slutför steg 2-4 för myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Logga in på Azure som UserA och kör följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Logga ut från Azure som UserA och logga in som användare b.
3. Kör följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Logga in på Azure som UserA och kör följande kommando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Logga ut från Azure som UserA och logga in som användare b.
3. Kör följande kommando:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Nästa steg

- Väl bekanta dig med viktiga [peering begränsningar för virtuellt nätverk och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar ett virtuellt nätverk peering för produktion använder.
- Lär dig mer om alla [peering inställningar för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och eker nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med virtuella nätverk peering.
