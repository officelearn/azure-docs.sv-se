---
title: Skapa en VNet-peering - olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverks peering mellan virtuella nätverk som skapats via Resource Manager och som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245127"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Skapa en virtuell nätverks peering - Resource Manager, olika prenumerationer

I den här självstudien lär du dig att skapa en virtuell nätverks peering mellan virtuella nätverk som skapats via Resource Manager. De virtuella nätverken finns i olika prenumerationer. Peering två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svarstid som om resurserna var i samma virtuella nätverk. Läs mer om [Virtual Network peering](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks peering är olika, beroende på om de virtuella nätverken är i samma eller olika prenumerationer och vilken [Azure-distributionsmodell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas genom. Lär dig hur du skapar en virtuell nätverks peering i andra scenarier genom att välja scenariot i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Olika|

Det går inte att skapa en virtuell nätverks peering mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Om du behöver ansluta virtuella nätverk som båda har skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att ansluta de virtuella nätverken.

Den här självstudien peer virtuella nätverk i samma region. Du kan också peer virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peering virtuella nätverk.

Du kan använda [Azure-portalen](#portal), Azure [command-line interface](#cli) (CLI), Azure [PowerShell](#powershell)eller en Azure Resource [Manager-mall](#template) för att skapa en virtuell nätverks peering. Välj någon av de tidigare verktygslänkarna för att gå direkt till stegen för att skapa en virtuell nätverks peering med ditt verktyg.

Om de virtuella nätverken finns i olika prenumerationer och prenumerationerna är associerade med olika Azure Active Directory-klienter slutför du följande steg innan du fortsätter:
1. Lägg till användaren från varje Active Directory-klient som [gästanvändare](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i den motsatta Azure Active Directory-klienten.
1. Varje användare måste acceptera gästanvändaranbjudan från den motsatta Azure Active Directory-klienten.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Skapa peering - Azure-portal

I följande steg används olika konton för varje prenumeration. Om du använder ett konto som har behörighet för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela en annan användarbehörighet till de virtuella nätverken.

1. Logga in på [Azure-portalen](https://portal.azure.com) som *UserA*. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
2. Välj **+ Skapa en resurs,** välj **Nätverk**och välj sedan **Virtuellt nätverk**.
3. Markera eller ange följande exempelvärden för följande inställningar och välj sedan **Skapa:**
    - **Namn**: *myVnetA*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resursgrupp:** Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *Östra USA*
4. Skriv *myVnetA*i rutan **Sökresurser** högst upp på portalen . Välj **myVnetA** när det visas i sökresultaten. 
5. Välj **Åtkomstkontroll (IAM)** i den lodräta listan med alternativ till vänster.
6. Under **myVnetA - Access control (IAM)** väljer du **+ Lägg till rolltilldelning**.
7. Välj **Nätverksdeltagare** i **rutan Roll.**
8. I rutan **Välj** väljer du *UserB*eller skriver UserB:s e-postadress för att söka efter den.
9. Välj **Spara**.
10. Under **myVnetA - Access control (IAM)** väljer du **Egenskaper** i den vertikala listan med alternativ till vänster. Kopiera **RESURS-ID**, som används i ett senare steg. Resurs-ID:t liknar följande `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`exempel: .
11. Logga ut från portalen som UserA och logga sedan in som UserB.
12. Slutför steg 2-3, ange eller välja följande värden i steg 3:

    - **Namn**: *myVnetB*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.1.0.0/24*
    - **Prenumeration**: Välj prenumeration B.
    - **Resursgrupp:** Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *Östra USA*

13. Skriv *myVnetB*i rutan **Sökresurser** högst upp på portalen . Välj **myVnetB** när det visas i sökresultaten.
14. Under **myVnetB**väljer du **Egenskaper** i den lodräta listan med alternativ till vänster. Kopiera **RESURS-ID**, som används i ett senare steg. Resurs-ID:t liknar följande `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`exempel: .
15. Välj **Åtkomstkontroll (IAM)** under **myVnetB**och slutför sedan steg 5-10 för myVnetB och ange **UserA** i steg 8.
16. Logga ut från portalen som UserB och logga in som UserA.
17. Skriv *myVnetA*i rutan **Sökresurser** högst upp på portalen . Välj **myVnetA** när det visas i sökresultaten.
18. Välj **myVnetA**.
19. Under **INSTÄLLNINGAR**väljer du **Peerings**.
20. Under **myVnetA - Peerings**väljer du **+ Lägg till**
21. Under **Lägg till peering**anger eller väljer du följande alternativ och väljer sedan **OK:**
     - **Namn**: *myVnetAToMyVnetB*
     - **Distributionsmodell för virtuellt nätverk**: Välj **Resurshanteraren**.
     - **Jag vet mitt resurs-ID:** Markera den här rutan.
     - **Resurs-ID:** Ange resurs-ID från steg 14.
     - **Tillåt virtuell nätverksåtkomst:** Kontrollera att **Aktiverad** är markerad.
    Inga andra inställningar används i den här självstudien. Mer information om alla peering-inställningar finns i [Hantera virtuella nätverks peerings](virtual-network-manage-peering.md#create-a-peering).
22. Peering du skapade visas en kort vänta efter att ha valt **OK** i föregående steg. **Initierad** visas i kolumnen **PEERING STATUS** för **myVnetAToMyVnetB-peering** som du skapade. Du har kikat myVnetA till myVnetB, men nu måste du peer myVnetB till myVnetA. Peering måste skapas i båda riktningarna för att resurser i de virtuella nätverken ska kunna kommunicera med varandra.
23. Logga ut från portalen som UserA och logga in som UserB.
24. Fyll i steg 17-21 igen för myVnetB. I steg 21 namnger du peering *myVnetBToMyVnetA*, väljer *myVnetA* för **virtuellt nätverk**och anger ID från steg 10 i rutan **Resurs-ID.**
25. Några sekunder efter att du har valt **OK** för att skapa peering för myVnetB visas **myVnetBToMyVnetA-peering** som du just har skapat **med Ansluten** i kolumnen **PEERING STATUS.**
26. Logga ut från portalen som UserB och logga in som UserA.
27. Fyll i steg 17-19 igen. **Peering-status** för **myVnetAToVNetB-peering** är nu också **ansluten**. Peering har upprättats när du ser **Ansluten** i kolumnen **PEERING STATUS** för båda virtuella nätverken i peering. Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
29. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i avsnittet [Ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering - Azure CLI

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörigheter för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort rader med skript som skapar användarrolltilldelningar. Ersätt UserA@azure.com UserB@azure.com och i alla följande skript med de användarnamn du använder för UserA och UserB. 

Följande skript:

- Kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera läser du [Installera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fungerar i ett Bash-skal. Läs mer om körningsalternativ för Azure CLI-skript i Windows-klienter i informationen om att [installera Azure CLI för Windows](/cli/azure/install-azure-cli-windows).

I stället för att installera CLI och dess beroenden kan du använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **Prova** i skriptet som följer, som anropar ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Öppna en CLI-session och logga in `azure login` på Azure som UserA med kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
2. Kopiera följande skript till en textredigerare på datorn, ersätt `<SubscriptionA-Id>` med ID:t för SubscriptionA, `Enter`kopiera det ändrade skriptet, klistra in det i CLI-sessionen och tryck på . Om du inte känner till ditt `az account show` prenumerations-ID anger du kommandot. Värdet för **id** i utdata är ditt prenumerations-ID.

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

3. Logga ut från Azure som `az logout` UserA med kommandot och logga sedan in på Azure som UserB. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
4. Skapa myVnetB. Kopiera skriptinnehållet i steg 2 till en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID:et för SubscriptionB. Ändra 10.0.0.0/16 till 10.1.0.0/16, ändra allt Som till B och alla Bs till A. Kopiera `Enter`det ändrade skriptet, klistra in det i din CLI-session och tryck på .
5. Logga ut från Azure som UserB och logga in på Azure som UserA.
6. Skapa en virtuell nätverks peering från myVnetA till myVnetB. Kopiera följande skriptinnehåll till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID:et för SubscriptionB. Om du vill köra skriptet kopierar du det ändrade skriptet, klistrar in det i CLI-sessionen och trycker på Retur.

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

7. Visa peering-tillståndet för myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Tillståndet **initieras**. Den ändras till **Ansluten** när du skapar peering till myVnetA från myVnetB.

8. Logga ut UserA från Azure och logga in på Azure som UserB.
9. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 6 till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID för SubscriptionA och ändra alla Som till B och alla Bs till A. När du har gjort ändringarna kopierar du det ändrade skriptet, `Enter`klistrar in det i CLI-sessionen och trycker på .
10. Visa peering-tillståndet för myVnetB. Kopiera skriptinnehållet i steg 7 till en textredigerare på datorn. Ändra A till B för resursgruppen och virtuella nätverksnamn, kopiera skriptet, klistra in `Enter`det ändrade skriptet i cli-sessionen och tryck sedan på . Peering-tillståndet är **Anslutet**. Peering-tillståndet för myVnetA ändras till **Ansluten** när du har skapat peering från myVnetB till myVnetA. Du kan logga UserA tillbaka till Azure och slutföra steg 7 igen för att verifiera peering-tillståndet för myVnetA. 

    > [!NOTE]
    > Peeringen upprättas inte förrän peering-tillståndet är **anslutet** för båda virtuella nätverken.

11. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
12. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-cli) i den här artikeln.

Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörigheter för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort rader med skript som skapar användarrolltilldelningar. Ersätt UserA@azure.com UserB@azure.com och i alla följande skript med de användarnamn du använder för UserA och UserB.

1. Bekräfta att du har Azure PowerShell version 1.0.0 eller senare. Du kan göra detta `Get-Module -Name Az` genom att köra Vi rekommenderar att du installerar den senaste versionen av PowerShell [Az-modulen](/powershell/azure/install-az-ps). Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Starta en PowerShell-session.
3. I PowerShell loggar du in på Azure `Connect-AzAccount` som UserA genom att ange kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
4. Skapa en resursgrupp och ett virtuellt nätverk A. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID:t för SubscriptionA. Om du inte känner till ditt `Get-AzSubscription` prenumerations-ID anger du kommandot för att visa det. Värdet för **ID** i den returnerade utdata är ditt prenumerations-ID. Om du vill köra skriptet kopierar du det ändrade `Enter`skriptet, klistrar in det i PowerShell och trycker sedan på .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Logga ut UserA från Azure och logga in UserB. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
6. Kopiera skriptinnehållet i steg 4 till en textredigerare på datorn. Ersätt `<SubscriptionA-Id>` med ID för prenumeration B. Ändra 10.0.0.0/16 till 10.1.0.0/16. Ändra allt när det gäller B och alla Bs till A. Om du vill köra skriptet kopierar du det `Enter`ändrade skriptet, klistrar in det i PowerShell och trycker sedan på .
7. Logga ut UserB från Azure och logga in UserA.
8. Skapa peering från myVnetA till myVnetB. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID:et för prenumeration B. Om du vill köra skriptet kopierar du det ändrade `Enter`skriptet, klistrar in på PowerShell och trycker sedan på .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Visa peering-tillståndet för myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet **initieras**. Den ändras till **Ansluten** när du ställer in peering till myVnetA från myVnetB.

10. Logga ut UserA från Azure och logga in UserB.
11. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 8 till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ID:et för prenumeration A och ändra allt som till B och alla Bs till A. Om du vill köra skriptet kopierar du det ändrade `Enter`skriptet, klistrar in det i PowerShell och trycker sedan på .
12. Visa peering-tillståndet för myVnetB. Kopiera skriptinnehållet i steg 9 till en textredigerare på datorn. Ändra A till B för resursgruppen och virtuella nätverksnamn. Om du vill köra skriptet klistrar du `Enter`in det ändrade skriptet i PowerShell och trycker sedan på . Tillståndet är **Anslutet**. Peering-tillståndet **för myVnetA** ändras till **Ansluten** när du har skapat peering från **myVnetB** till **myVnetA**. Du kan logga UserA tillbaka till Azure och slutföra steg 9 igen för att verifiera peering-tillståndet för myVnetA.

    > [!NOTE]
    > Peeringen upprättas inte förrän peering-tillståndet är **anslutet** för båda virtuella nätverken.

    Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
14. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Skapa peering - Resource Manager-mall

1. Om du vill skapa ett virtuellt nätverk och tilldela lämpliga [behörigheter](virtual-network-manage-peering.md#permissions)följer du stegen i avsnitten [Portal,](#portal) [Azure CLI](#cli)eller [PowerShell](#powershell) i den här artikeln.
2. Spara texten som följer på en fil på den lokala datorn. Ersätt `<subscription ID>` med UserA:s prenumerations-ID. Du kan till exempel spara filen som vnetpeeringA.json.

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

3. Logga in på Azure som UserA och distribuera mallen med hjälp av [portalen,](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)eller [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Ange filnamnet som du sparade exempeljisontexten i steg 2 till.
4. Kopiera exemplet json från steg 2 till en fil på datorn och gör ändringar på raderna som börjar med:
   - **namn**: Ändra *myVnetA/myVnetAToMyVnetB* till *myVnetB/myVnetBToMyVnetA*.
   - **id**: `<subscription ID>` Ersätt med UserB:s prenumerations-ID och ändra *myVnetB* till *myVnetA*.
5. Fyll i steg 3 igen, inloggad på Azure som UserB.
6. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
7. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien slutför du stegen i avsnittet [Ta bort resurser](#delete) i den här artikeln med hjälp av Azure-portalen, PowerShell eller Azure CLI.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser
När du har slutfört den här självstudien kanske du vill ta bort de resurser som du har skapat i självstudien, så att du inte ådrar dig användningsavgifter. Om du tar bort en resursgrupp tas också alla resurser som finns i resursgruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Logga in på Azure-portalen som UserA.
2. Ange **myResourceGroupA**i sökrutan för portalen . I sökresultaten väljer du **myResourceGroupA**.
3. Välj **Ta bort**.
4. Om du vill bekräfta borttagningen anger du **myResourceGroupA**i rutan **SKRIV RESURSGRUPPNAMN** och väljer sedan **Ta bort**.
5. Logga ut från portalen som UserA och logga in som UserB.
6. Slutför steg 2-4 för myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Logga in på Azure som UserA och kör följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Logga ut från Azure som UserA och logga in som UserB.
3. Kör följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Logga in på Azure som UserA och kör följande kommando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Logga ut från Azure som UserA och logga in som UserB.
3. Kör följande kommando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Nästa steg

- Bekanta dig med viktiga begränsningar och beteenden för [virtuella nätverks peering](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverks peering för produktionsanvändning.
- Lär dig mer om alla inställningar för [virtuell nätverks peering](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapar en hubb och talade nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med virtuell nätverks peering.
