---
title: Skapa en virtuell Azure-nätverks peering – olika distributionsmodeller -olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverks peering mellan virtuella nätverk som skapats via olika Azure-distributionsmodeller som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239840"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Skapa en virtuell nätverks peering - olika distributionsmodeller och prenumerationer

I den här självstudien lär du dig att skapa en virtuell nätverks peering mellan virtuella nätverk som skapats genom olika distributionsmodeller. De virtuella nätverken finns i olika prenumerationer. Peering två virtuella nätverk gör det möjligt för resurser i olika virtuella nätverk att kommunicera med varandra med samma bandbredd och svarstid som om resurserna var i samma virtuella nätverk. Läs mer om [Virtual Network peering](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverks peering är olika, beroende på om de virtuella nätverken är i samma eller olika prenumerationer och vilken [Azure-distributionsmodell](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas genom. Lär dig hur du skapar en virtuell nätverks peering i andra scenarier genom att klicka på scenariot från följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Olika|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|

Det går inte att skapa en virtuell nätverks peering mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Den här självstudien använder virtuella nätverk som finns i samma region. Den här självstudien peer virtuella nätverk i samma region. Du kan också peer virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekantar dig med [peering-kraven och begränsningarna](virtual-network-manage-peering.md#requirements-and-constraints) innan du peering virtuella nätverk.

När du skapar en virtuell nätverks peering mellan virtuella nätverk som finns i olika prenumerationer, måste prenumerationerna båda associeras till samma Azure Active Directory-klient. Om du inte redan har en Azure Active Directory-klient kan du snabbt [skapa en](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Du kan ansluta virtuella nätverk i olika prenumerationer och olika Azure Active Directory-klienter med hjälp av en Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan använda [Azure-portalen,](#portal)Azure [command-line interface](#cli) (CLI) eller Azure [PowerShell](#powershell) för att skapa en virtuell nätverks peering. Klicka på någon av de tidigare verktygslänkarna för att gå direkt till stegen för att skapa en virtuell nätverks peering med hjälp av ditt verktyg val.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Skapa peering - Azure-portal

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela en annan användarbehörighet till de virtuella nätverken.

1. Logga in på [Azure-portalen](https://portal.azure.com) som UserA. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
2. Klicka på **+ Ny**, klicka på **Nätverk**och sedan på **Virtuellt nätverk**.
3. I bladet **Skapa virtuellt nätverk** anger eller väljer du värden för följande inställningar och klickar sedan på **Skapa:**
    - **Namn**: *myVnetA*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resursgrupp:** Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *Östra USA*
4. Skriv *myVnetA*i rutan **Sökresurser** högst upp på portalen . Klicka på **myVnetA** när det visas i sökresultaten. Ett blad visas för **myVnetA** virtuella nätverk.
5. I **myVnetA-bladet** som visas klickar du på **Åtkomstkontroll (IAM)** från den vertikala listan med alternativ till vänster om bladet.
6. Klicka på **+ Lägg till rolltilldelning**i bladet **myVnetA - Access control (IAM)** som visas .
7. I tilldelningsbladet **Lägg till roll** som visas väljer du **Nätverksdeltagare** i rutan **Roll.**
8. I rutan **Välj** väljer du UserB eller skriver UserB:s e-postadress för att söka efter den. Listan över användare som visas är från samma Azure Active Directory-klient som det virtuella nätverk som du konfigurerar peering för. Klicka på UserB när den visas i listan.
9. Klicka på **Spara**.
10. Logga ut från portalen som UserA och logga sedan in som UserB.
11. Klicka på **+ Nytt**, skriv *Virtuellt nätverk* i rutan Sök på **Marketplace** och klicka sedan på **Virtuellt nätverk** i sökresultaten.
12. I bladet **Virtuellt nätverk** som visas väljer du **Klassisk** i rutan **Välj en distributionsmodell** och klickar sedan på **Skapa**.
13. I rutan Skapa virtuellt nätverk (klassiskt) som visas anger du följande värden:

    - **Namn**: *myVnetB*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn:** *standard*
    - **Undernätsadressintervall:** *10.1.0.0/24*
    - **Prenumeration**: Välj prenumeration B.
    - **Resursgrupp:** Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *Östra USA*

14. Skriv *myVnetB*i rutan **Sökresurser** högst upp på portalen . Klicka på **myVnetB** när det visas i sökresultaten. Ett blad visas för **myVnetB** virtuella nätverk.
15. I **myVnetB-bladet** som visas klickar du på **Egenskaper** från den vertikala listan med alternativ till vänster om bladet. Kopiera **RESURS-ID**, som används i ett senare steg. Resurs-ID:t liknar följande exempel:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Slutför steg 5-9 för myVnetB, ange **UserA** i steg 8.
17. Logga ut från portalen som UserB och logga in som UserA.
18. Skriv *myVnetA*i rutan **Sökresurser** högst upp på portalen . Klicka på **myVnetA** när det visas i sökresultaten. Ett blad visas för **myVnet** virtuella nätverk.
19. Klicka på **myVnetA**.
20. I **myVnetA-bladet** som visas klickar du på **Peerings** från den vertikala listan med alternativ till vänster om bladet.
21. I **myVnetA - Peerings** blad som dök upp, klicka **+ Lägg**
22. I bladet **Lägg till peering** som visas anger eller markerar du följande alternativ och klickar sedan på **OK:**
     - **Namn**: *myVnetAToMyVnetB*
     - **Distribuera modell för virtuellt nätverk:** Välj **klassisk.**
     - **Jag vet mitt resurs-ID:** Markera den här rutan.
     - **Resurs-ID:** Ange resurs-ID för myVnetB från steg 15.
     - **Tillåt virtuell nätverksåtkomst:** Kontrollera att **Aktiverad** är markerad.
    Inga andra inställningar används i den här självstudien. Mer information om alla peering-inställningar finns i [Hantera virtuella nätverks peerings](virtual-network-manage-peering.md#create-a-peering).
23. När du har klickat **på OK** i föregående steg stängs **bladet Lägg till peering** och du ser **bladet myVnetA - Peerings** igen. Efter några sekunder visas peering-peeringen som du skapade i bladet. **Ansluten** visas i kolumnen **PEERING STATUS** för **myVnetAToMyVnetB-peering** som du skapade. Peering är nu etablerad. Det finns ingen anledning att peer det virtuella nätverket (klassisk) till det virtuella nätverket (Resource Manager).

    Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
25. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i avsnittet [Ta bort resurser](#delete-portal) i den här artikeln.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Skapa peering - Azure CLI

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörigheter för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort rader med skript som skapar användarrolltilldelningar. Ersätt UserA@azure.com UserB@azure.com och i alla följande skript med de användarnamn du använder för UserA och UserB. Slutför följande steg med Azure classic CLI och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell genom att bara välja **prova-knappen** i något av följande steg, eller genom att installera den [klassiska CLI](/cli/azure/install-classic-cli) och [CLI](/cli/azure/install-azure-cli) och köra kommandona på din lokala dator.

1. Om du använder Cloud Shell, hoppa till steg 2, eftersom Cloud Shell automatiskt loggar in dig på Azure. Öppna en kommandosession och logga `azure login` in på Azure med kommandot.
2. Kör den klassiska CLI i servicehanteringsläge genom att ange `azure config mode asm` kommandot.
3. Ange följande klassiska CLI-kommando för att skapa det virtuella nätverket (klassiskt):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. De återstående stegen måste slutföras med ett bash-skal med Azure CLI (inte den klassiska CLI).
5. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ditt prenumerations-ID. Om du inte känner till ditt `az account show` prenumerations-ID anger du kommandot. Värdet för **id** i utdata är ditt prenumerations-ID. Kopiera det ändrade skriptet, klistra in det i din CLI-session och tryck sedan på `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    När du skapade det virtuella nätverket (klassiskt) i steg 4 skapade Azure det virtuella nätverket i resursgruppen *Standardnätverk.*
6. Logga UserB från Azure och logga in som UserA i CLI.
7. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera följande skript, klistra in det i CLI-sessionen och tryck sedan på `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Skapa en virtuell nätverks peering mellan de två virtuella nätverk som skapats genom de olika distributionsmodellerna. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-id>` med ditt prenumerations-ID. Om du inte känner till ditt `az account show` prenumerations-ID anger du kommandot. Värdet för **id** i utdata är ditt prenumerations-ID. Azure skapade det virtuella nätverket (klassiskt) som du skapade i steg 4 i en resursgrupp med namnet *Default-Networking*. Klistra in det ändrade skriptet i `Enter`CLI-sessionen och tryck sedan på .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. När skriptet har körs granskar du peering för det virtuella nätverket (Resource Manager). Kopiera följande skript och klistra sedan in det i CLI-sessionen:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Utdata visar **Ansluten** i **peeringstate-kolumnen.**

    Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
11. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-cli) i den här artikeln.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Skapa peering - PowerShell

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörigheter för båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från Azure och ta bort rader med skript som skapar användarrolltilldelningar. Ersätt UserA@azure.com UserB@azure.com och i alla följande skript med de användarnamn du använder för UserA och UserB. 

1. Installera den senaste versionen av PowerShell [Azure-](https://www.powershellgallery.com/packages/Azure) och [Az-modulerna.](https://www.powershellgallery.com/packages/Az) Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. Logga in på UserB:s prenumeration som UserB i `Add-AzureAccount` PowerShell genom att ange kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
4. Om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell måste du skapa en ny eller ändra en befintlig nätverkskonfigurationsfil. Lär dig hur du [exporterar, uppdaterar och importerar nätverkskonfigurationsfiler](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite-element** för det virtuella nätverk som används i den här självstudien:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Logga in på UserB:s prenumeration som UserB för att `Connect-AzAccount` använda Resource Manager-kommandon genom att ange kommandot.
6. Tilldela UserA-behörigheter till virtuella nätverk B. Kopiera följande skript `<SubscriptionB-id>` till en textredigerare på datorn och ersätt med ID:n för prenumeration B. Om du inte känner till prenumerations-ID:t anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i den returnerade utdata är ditt prenumerations-ID. Azure skapade det virtuella nätverk (klassiskt) som du skapade i steg 4 i en resursgrupp med namnet *Default-Networking*. Om du vill köra skriptet kopierar du det ändrade `Enter`skriptet, klistrar in det i PowerShell och trycker sedan på .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Logga ut från Azure som UserB och logga in på UserA:s prenumeration som UserA genom att ange `Connect-AzAccount` kommandot. Kontot du loggar in med måste ha de behörigheter som krävs för att skapa en virtuell nätverks peering. En lista över behörigheter finns i [Virtual Network peering-behörigheter](virtual-network-manage-peering.md#permissions).
8. Skapa det virtuella nätverket (Resource Manager) genom att kopiera följande skript, klistra `Enter`in det i PowerShell och sedan trycka på:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Tilldela UserB-behörigheter till myVnetA. Kopiera följande skript till en textredigerare på datorn och ersätt `<SubscriptionA-Id>` med ID:n för prenumeration A. Om du inte känner till prenumerations-ID:t anger du `Get-AzSubscription` kommandot för att visa det. Värdet för **ID** i den returnerade utdata är ditt prenumerations-ID. Klistra in den ändrade versionen av skriptet `Enter` i PowerShell och tryck sedan för att köra den.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopiera följande skript till en textredigerare på datorn och ersätt `<SubscriptionB-id>` med ID:n för prenumeration B. Om du vill peer myVnetA till myVNetB kopierar du det `Enter`ändrade skriptet, klistrar in det i PowerShell och trycker sedan på .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Visa peering-tillståndet för myVnetA genom att kopiera följande skript, klistra `Enter`in det i PowerShell och trycka på .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet är **Anslutet**. Den ändras till **Ansluten** när du ställer in peering till myVnetA från myVnetB.

    Alla Azure-resurser som du skapar i något av de virtuella nätverken kan nu kommunicera med varandra via sina IP-adresser. Om du använder standardlösningen för Azure-namn för de virtuella nätverken kan resurserna i de virtuella nätverken inte matcha namn i de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa en egen DNS-server. Lär dig hur du konfigurerar [Namnmatchning med din egen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Valfritt:** Även om det inte ingår att skapa virtuella datorer i den här självstudien kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till en annan för att validera anslutningen.
13. **Valfritt:** Om du vill ta bort de resurser som du skapar i den här självstudien gör du stegen i [Ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete-resources"></a><a name="delete"></a>Ta bort resurser
När du har slutfört den här självstudien kanske du vill ta bort de resurser som du har skapat i självstudien, så att du inte ådrar dig användningsavgifter. Om du tar bort en resursgrupp tas också alla resurser som finns i resursgruppen bort.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Ange **myResourceGroupA**i sökrutan för portalen . Klicka på **myResourceGroupA**i sökresultaten .
2. Klicka på ikonen **Ta bort** på bladet **MyResourceGroupA.**
3. Om du vill bekräfta borttagningen anger du **myResourceGroupA**i rutan **SKRIV RESURSGRUPPNAMN** och klickar sedan på **Ta bort**.
4. Skriv *myVnetB*i rutan **Sökresurser** högst upp på portalen . Klicka på **myVnetB** när det visas i sökresultaten. Ett blad visas för **myVnetB** virtuella nätverk.
5. Klicka på **Ta bort**i **bladet myVnetB** .
6. Bekräfta borttagningen genom att klicka på **Ja** i rutan **Ta bort virtuellt nätverk.**

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Logga in på Azure med CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Logga in på Azure med den klassiska CLI för att ta bort det virtuella nätverket (klassiskt) med följande kommandon:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. I Kommandotolken i PowerShell anger du följande kommando för att ta bort det virtuella nätverket (Resource Manager):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Om du vill ta bort det virtuella nätverket (klassiskt) med PowerShell måste du ändra en befintlig nätverkskonfigurationsfil. Lär dig hur du [exporterar, uppdaterar och importerar nätverkskonfigurationsfiler](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite-element för det virtuella nätverk som används i den här självstudien:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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