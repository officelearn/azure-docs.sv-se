---
title: Skapa en Azure-nätverk peering - Resource Manager – olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverkspeering mellan virtuella nätverk som skapats via Resource Manager och som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: 3b929c3f85bab4319fe2fef1b5c0575f0d1d972a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511684"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Skapa en virtuell nätverkspeering - resurshanteraren, olika prenumerationer

I den här självstudien får du lära dig att skapa en virtuell nätverkspeering mellan virtuella nätverk som skapats via Resource Manager. De virtuella nätverken finnas i olika prenumerationer. Peering två virtuella nätverk kan resurser i olika virtuella nätverk kan kommunicera med varandra med samma bandbredd och latens som om resurserna fanns i samma virtuella nätverk. Läs mer om [peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverkspeering är olika, beroende på om de virtuella nätverken är i samma, eller olika, prenumerationer och som [Azure-distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverkspeering i andra scenarier genom att välja scenariot i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Both Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models-subscriptions.md) |Annorlunda|

Att går inte skapa en virtuell nätverkspeering mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Om du vill ansluta virtuella nätverk som har både skapats via den klassiska distributionsmodellen kan du använda en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) att ansluta virtuella nätverk.

Den här självstudien peerar virtuella nätverk i samma region. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekanta dig med den [peering krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints) innan peerkoppla virtuella nätverk.

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI), Azure [PowerShell](#powershell), eller en [Azure Resource Manager-mall](#template)att skapa vnet-peering. Välj någon av föregående verktyget länkarna och gå direkt till stegen för att skapa en virtuell nätverkspeering med ditt val.

## <a name="portal"></a>Skapa peering - Azure-portalen

Om de virtuella nätverk som du vill peerkoppla finns i prenumerationer som är kopplade till olika Azure Active Directory-klienter, följer du stegen i avsnittet CLI och PowerShell i den här artikeln. Portalen har inte stöd för att peerkoppla virtuella nätverk som hör till prenumerationer från olika Active Directory-klienter.

Följande steg kan du använda olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela en annan användarbehörigheter för de virtuella nätverken.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som *UserA*. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
2. Välj **+ skapa en resurs**väljer **nätverk**, och välj sedan **virtuellt nätverk**.
3. Välj eller ange följande exempelvärden för följande inställningar och sedan **skapa**:
    - **Name**: *myVnetA*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resursgrupp**: Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *USA, östra*
4. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetA*. Välj **myVnetA** när den visas i sökresultaten. 
5. Välj **åtkomstkontroll (IAM)** från en lodrät lista över alternativ till vänster.
6. Under **myVnetA - åtkomstkontroll (IAM)** väljer **+ Lägg till rolltilldelning**.
7. Välj **nätverksdeltagare** i den **rollen** box.
8. I den **Välj** väljer *UserB*, eller ange användare BS e-postadress för att söka efter den.
9. Välj **Spara**.
10. Under **myVnetA - åtkomstkontroll (IAM)** väljer **egenskaper** från en lodrät lista över alternativ till vänster. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som genereras liknar följande exempel: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Logga ut från portalen som UserA och sedan logga in som användare b.
12. Slutför steg 2 – 3, att ange eller markera följande värden i steg 3:

    - **Name**: *myVnetB*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj en prenumeration B.
    - **Resursgrupp**: Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *USA, östra*

13. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetB*. Välj **myVnetB** när den visas i sökresultaten.
14. Under **myVnetB**väljer **egenskaper** från en lodrät lista över alternativ till vänster. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som genereras liknar följande exempel: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Välj **åtkomstkontroll (IAM)** under **myVnetB**, och utför steg 5 – 10 för myVnetB, att ange **UserA** i steg 8.
16. Logga ut från portalen som UserB och logga in som UserA.
17. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetA*. Välj **myVnetA** när den visas i sökresultaten.
18. Välj **myVnetA**.
19. Under **inställningar**väljer **Peerings**.
20. Under **myVnetA - Peerings**väljer **+ Lägg till**
21. Under **Lägg till peering**, anger, eller Välj följande alternativ och väljer sedan **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Distributionsmodell för virtuellt nätverk**:  Välj **Resource Manager**.
     - **Jag vet mitt resurs-ID**: Markera den här kryssrutan.
     - **Resurs-ID**: Ange resurs-ID från steg 14.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktiverad** har valts.
    Inga andra inställningar som används i den här självstudien. Läs om alla inställningar för peering [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
22. Peer-kopplingen du har skapat visas en kort stund när du har valt **OK** i föregående steg. **Initierade** visas i den **PEERING STATUS** kolumnen för den **myVnetAToMyVnetB** peering du skapade. Du har peer-kopplade myVnetA till myVnetB, men du måste nu peer-myVnetB till myVnetA. Peer-kopplingen måste skapas i båda riktningarna att resurser i de virtuella nätverken kan kommunicera med varandra.
23. Logga ut från portalen som UserA och logga in som användare b.
24. Slutför steg 17-21 igen för myVnetB. Namnge peer-kopplingen i steg 21 *myVnetBToMyVnetA*väljer *myVnetA* för **virtuellt nätverk**, och ange det ID: T från steg 10 i den **resurs-ID**box.
25. Några sekunder efter att ha valt **OK** att skapa peering för myVnetB, den **myVnetBToMyVnetA** peering du nyss skapade visas den med **ansluten** i **PEERING STATUS** kolumn.
26. Logga ut från portalen som UserB och logga in som UserA.
27. Slutför steg 17-19 igen. Den **PEERING STATUS** för den **myVnetAToVNetB** peering är nu också **ansluten**. Peer-kopplingen har upprättats efter att du ser **ansluten** i den **PEERING STATUS** kolumn för båda virtuella nätverken i peer-kopplingen. Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
29. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i den [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering – Azure CLI

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla åtgärder, hoppa över stegen för att logga ut från Azure och ta bort raderna för skript som skapar rolltilldelningar för användare. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för UserA och UserB. Om de virtuella nätverken finns i olika prenumerationer, och prenumerationerna är kopplade till olika Azure Active Directory-klienter, utför du följande steg innan du fortsätter:
 - Lägg till användaren från varje Active Directory-klient som en [gästanvändare](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i motsatt Azure Active Directory-klient.
 - Varje användare måste acceptera inbjudan för gäst-användaren från motsatt Azure Active Directory-klient.

Följande skript:

- Kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fungerar i ett Bash-gränssnitt. Läs mer om körningsalternativ för Azure CLI-skript i Windows-klienter i informationen om att [installera Azure CLI för Windows](/cli/azure/install-azure-cli-windows).

Du kan använda Azure Cloud Shell istället för att installera CLI och dess beroenden. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj den **prova** knappen i skriptet som följer, som anropar ett gränssnitt för molnet som du kan logga in på Azure-kontot med. 

1. Öppna en CLI-session och logga in på Azure som användare a med hjälp av den `azure login` kommando. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
2. Kopiera följande skript till en textredigerare på din dator, ersätter `<SubscriptionA-Id>` med ID Prenumerationa sedan kopiera det ändrade skriptet, klistra in den i din CLI-sessionen och tryck på `Enter`. Om du inte vet ditt prenumerations-Id, anger du 'az account show'-kommando. Värdet för **id** i utdata är ditt prenumerations-Id.

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
    
3. Logga ut från Azure som användare a med hjälp av den `az logout` kommandot och sedan logga in på Azure som användare b. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
4. Skapa myVnetB. Kopiera skriptinnehållet i steg 2 till en textredigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: T för SubscriptionB. Ändra 10.0.0.0/16 10.1.0.0/16, alla avseende B, och alla Bs till A. kopia det ändrade skriptet, klistra in den i din CLI-sessionen och tryck på `Enter`. 
5. Logga ut från Azure som UserB och logga in på Azure som UserA.
6. Skapa ett virtuellt nätverk som peer-kopplingen från myVnetA till myVnetB. Kopiera följande skriptinnehållet till en textredigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: T för SubscriptionB. Kopiera det ändrade skriptet för att köra skriptet, klistra in den i din CLI-sessionen och tryck på RETUR.

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

7. Visa peerkopplingens status för myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Tillståndet är **initierad**. Ändras till **ansluten** när du skapar peer-kopplingen till myVnetA från myVnetB.

8. Logga ut användare a från Azure och logga in på Azure som användare b.
9. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 6 till en textredigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: T för Prenumerationa och ändra alla avseende B och alla Bs till A. När du har gjort ändringarna, kopiera det ändrade skriptet, klistra in den i din CLI-sessionen och tryck på `Enter`.
10. Visa peerkopplingens status för myVnetB. Kopiera skriptinnehållet i steg 7 till en textredigerare på din dator. Ändra A till B för resursgrupp och namn för virtuellt nätverk, kopiera skriptet, klistra in de ändrade skripten i i CLI-sessionen och tryck sedan på `Enter`. Peering-tillståndet är **ansluten**. MyVnetA peering-tillståndet ändras till **ansluten** när du har skapat peer-kopplingen från myVnetB till myVnetA. Du kan logga UserA tillbaka till Azure och fullständig steg 7 igen för att verifiera myVnetA peering-tillståndet. 

    > [!NOTE]
    > Peer-kopplingen är inte upprättad förrän peering-tillståndet är **ansluten** för båda virtuella nätverken.

11. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
12. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-cli) i den här artikeln.

Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Skapa peering - PowerShell

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla åtgärder, hoppa över stegen för att logga ut från Azure och ta bort raderna för skript som skapar rolltilldelningar för användare. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för UserA och UserB. Om de virtuella nätverken finns i olika prenumerationer, och prenumerationerna är kopplade till olika Azure Active Directory-klienter, utför du följande steg innan du fortsätter:
 - Lägg till användaren från varje Active Directory-klient som en [gästanvändare](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i motsatt Azure Active Directory-klient.
 - Varje användare måste acceptera inbjudan för gäst-användaren från motsatt Active Directory-klient.

1. Bekräfta att du har version 6.5.0 eller högre. Du kan göra detta genom att köra den `Get-Module -Name AzureRm` vi rekommenderar att du installerar den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Starta en PowerShell-session.
3. I PowerShell, logga in på Azure som UserA genom att ange den `Connect-AzureRmAccount` kommando. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
4. Skapa en resursgrupp och ett virtuellt nätverk A. kopiera följande skript till en textredigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: T för Prenumerationa. Om du inte vet ditt prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Kopiera det ändrade skriptet för att köra skriptet, klistra in det i PowerShell och tryck sedan på `Enter`.

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

5. Logga ut användare a från Azure och logga in användare b. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
6. Kopiera skriptinnehållet i steg 4 till en textredigerare på din dator. Ersätt `<SubscriptionA-Id>` med ID: T för prenumerationen B. Ändra 10.0.0.0/16 till 10.1.0.0/16. Ändra alla avseende B och alla Bs till A. Kopiera det ändrade skriptet för att köra skriptet, klistra in i PowerShell och tryck sedan på `Enter`.
7. Logga ut användare b från Azure och logga in användare a.
8. Skapa peering från myVnetA till myVnetB. Kopiera följande skript till en textredigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID för prenumerationen B. Kopiera det ändrade skriptet för att köra skriptet, klistra in i PowerShell och tryck sedan på `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Visa peerkopplingens status för myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet är **initierad**. Ändras till **ansluten** när du har konfigurerat peer-kopplingen till myVnetA från myVnetB.

10. Logga ut användare a från Azure och logga in användare b.
11. Skapa peering från myVnetB till myVnetA. Kopiera skriptinnehållet i steg 8 till en textredigerare på din dator. Ersätt `<SubscriptionB-Id>` med ID: T för prenumeration A och ändra alla avseende B och alla Bs till A. Kopiera det ändrade skriptet för att köra skriptet, klistra in det i PowerShell och tryck sedan på `Enter`.
12. Visa peerkopplingens status för myVnetB. Kopiera skriptinnehållet i steg 9 till en textredigerare på din dator. Ändra A till B för resursgrupp och namn på virtuellt nätverk. Klistra in de ändrade skripten i PowerShell för att köra skriptet, och tryck sedan på `Enter`. Tillståndet är **ansluten**. Peering-tillståndet för **myVnetA** ändras till **ansluten** när du har skapat peer-kopplingen från **myVnetB** till **myVnetA**. Du kan logga UserA gå tillbaka till Azure och fullständig steg 9 igen för att verifiera myVnetA peering-tillståndet. 

    > [!NOTE]
    > Peer-kopplingen är inte upprättad förrän peering-tillståndet är **ansluten** för båda virtuella nätverken.

    Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
14. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="template"></a>Skapa peering - Resource Manager-mall

Om de virtuella nätverken finns i olika prenumerationer, och prenumerationerna är kopplade till olika Azure Active Directory-klienter, utför du följande steg innan du fortsätter:
 - Lägg till användaren från varje Active Directory-klient som en [gästanvändare](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) i motsatt Azure Active Directory-klient.
 - Varje användare måste acceptera inbjudan för gäst-användaren från motsatt Active Directory-klient.
 
1. Skapa ett virtuellt nätverk och tilldela rätt [behörigheter](virtual-network-manage-peering.md#permissions), utför stegen i den [Portal](#portal), [Azure CLI](#cli), eller [PowerShell](#powershell)avsnitt i den här artikeln.
2. Spara texten nedan till en fil på din lokala dator. Ersätt `<subscription ID>` med Useras prenumerations-ID. Du kan spara filen som vnetpeeringA.json, till exempel.

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

3. Logga in på Azure som UserA och distribuera mallen med hjälp av den [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), eller [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Ange namnet på filen som du sparade exemplet json-texten i steg 2 till.
4. Kopiera exempel-json från steg 2 till en fil på din dator och göra ändringar i raderna som börjar med:
    - **Namn på**: Ändra *myVnetA/myVnetAToMyVnetB* till *myVnetB/myVnetBToMyVnetA*.
    - **ID**: Ersätt `<subscription ID>` med Userbs prenumerations-ID och ändrar *myVnetB* till *myVnetA*.
5. Slutför steg 3 igen, inloggad Azure som användare b.
6. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
7. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i den [ta bort resurser](#delete) i den här artikeln med hjälp av Azure portal, PowerShell eller Azure CLI.

## <a name="delete"></a>Ta bort resurser
När du har slutfört den här kursen, kanske du vill ta bort alla resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. Ta bort en resursgrupp tar även bort alla resurser i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Logga in på Azure-portalen som UserA.
2. Ange i portalen sökrutan **myResourceGroupA**. I sökresultaten väljer **myResourceGroupA**.
3. Välj **Ta bort**.
4. Bekräfta borttagningen, i den **Skriv RESURSGRUPPSNAMNET** anger **myResourceGroupA**, och välj sedan **ta bort**.
5. Logga ut från portalen som UserA och logga in som användare b.
6. Slutför steg 2 – 4 för myResourceGroupB.

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

- Noggrant bekanta dig med viktiga [vnet peering begränsningar och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverkspeering för produktion använder.
- Lär dig mer om alla [peering-inställningar för virtuellt nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och ekrar nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling.
