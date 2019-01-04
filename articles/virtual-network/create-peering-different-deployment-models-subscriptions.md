---
title: Skapa ett Azure vnet-peering - oliak modeller – olika prenumerationer
titlesuffix: Azure Virtual Network
description: Lär dig hur du skapar en virtuell nätverkspeering mellan virtuella nätverk som skapats via olika Azure distributionsmodeller som finns i olika Azure-prenumerationer.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 45dd31ecca4f09a595fbf1dae5fe61623fb2be3c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025869"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Skapa en virtuell nätverkspeering - olika distributionsmodeller och -prenumerationer

I den här självstudien får du lära dig att skapa en virtuell nätverkspeering mellan virtuella nätverk som skapats via olika distributionsmodeller. De virtuella nätverken finnas i olika prenumerationer. Peering två virtuella nätverk kan resurser i olika virtuella nätverk kan kommunicera med varandra med samma bandbredd och latens som om resurserna fanns i samma virtuella nätverk. Läs mer om [peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

Stegen för att skapa en virtuell nätverkspeering är olika, beroende på om de virtuella nätverken är i samma, eller olika, prenumerationer och som [Azure-distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar en virtuell nätverkspeering i andra scenarier genom att klicka på scenario i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |Samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Annorlunda|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |Samma|

Att går inte skapa en virtuell nätverkspeering mellan två virtuella nätverk som distribueras via den klassiska distributionsmodellen. Den här självstudien använder virtuella nätverk som finns i samma region. Den här självstudien peerar virtuella nätverk i samma region. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region). Vi rekommenderar att du bekanta dig med den [peering krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints) innan peerkoppla virtuella nätverk.

När du skapar en virtuell nätverkspeering mellan virtuella nätverk som finns i olika prenumerationer, måste prenumerationer båda vara kopplade till samma Azure Active Directory-klient. Om du inte redan har en Azure Active Directory-klient, kan du snabbt [skapar ett](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Du kan ansluta virtuella nätverk i olika prenumerationer och annan Azure Active Directory-klienter med hjälp av en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI), eller Azure [PowerShell](#powershell) att skapa vnet-peering. Klicka på någon av föregående verktyget länkarna och gå direkt till stegen för att skapa en virtuell nätverkspeering med ditt val.

## <a name="portal"></a>Skapa peering - Azure-portalen

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla steg, hoppa över stegen för att logga ut från portalen och hoppa över stegen för att tilldela en annan användarbehörigheter för de virtuella nätverken.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som UserA. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
2. Klicka på **+ ny**, klickar du på **nätverk**, klicka sedan på **virtuellt nätverk**.
3. I den **skapa virtuellt nätverk** bladet anger, eller värden för följande inställningar och sedan klicka på **skapa**:
    - **Namnet**: *myVnetA*
    - **Adressutrymme**: *10.0.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.0.0.0/24*
    - **Prenumeration**: Välj prenumeration A.
    - **Resursgrupp**: Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *USA, östra*
4. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetA*. Klicka på **myVnetA** när den visas i sökresultaten. Ett blad som visas för den **myVnetA** virtuellt nätverk.
5. I den **myVnetA** bladet som visas, klickar du på **åtkomstkontroll (IAM)** i lodräta listan med alternativ på vänster sida av bladet.
6. I den **myVnetA - åtkomstkontroll (IAM)** bladet som visas, klickar du på **+ Lägg till rolltilldelning**.
7. I den **Lägg till rolltilldelning** bladet som visas, Välj **nätverksdeltagare** i den **rollen** box.
8. I den **Välj** markerar användare b, eller ange användare BS e-postadress för att söka efter den. En lista över användare som visas är från samma Azure Active Directory-klient som det virtuella nätverket som du konfigurerar peering för. Klicka på användare b när den visas i listan.
9. Klicka på **Spara**.
10. Logga ut från portalen som UserA och sedan logga in som användare b.
11. Klicka på **+ ny**, typ *virtuellt nätverk* i den **Sök på Marketplace** rutan och klicka sedan på **virtuellt nätverk** i sökresultatet.
12. I den **virtuellt nätverk** bladet som visas, Välj **klassiska** i den **Välj en distributionsmodell** rutan och klicka sedan på **skapa**.
13.   I skapa virtuellt nätverk (klassisk) rutan som visas anger du följande värden:

    - **Namnet**: *myVnetB*
    - **Adressutrymme**: *10.1.0.0/16*
    - **Undernätsnamn**: *standard*
    - **Adressintervall för undernätet**: *10.1.0.0/24*
    - **Prenumeration**: Välj en prenumeration B.
    - **Resursgrupp**: Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *USA, östra*

14. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetB*. Klicka på **myVnetB** när den visas i sökresultaten. Ett blad som visas för den **myVnetB** virtuellt nätverk.
15. I den **myVnetB** bladet som visas, klickar du på **egenskaper** i lodräta listan med alternativ på vänster sida av bladet. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som genereras liknar följande exempel: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Slutför steg 5 – 9 för myVnetB, att ange **UserA** i steg 8.
17. Logga ut från portalen som UserB och logga in som UserA.
18. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetA*. Klicka på **myVnetA** när den visas i sökresultaten. Ett blad som visas för den **myVnet** virtuellt nätverk.
19. Klicka på **myVnetA**.
20. I den **myVnetA** bladet som visas, klickar du på **Peerings** i lodräta listan med alternativ på vänster sida av bladet.
21. I den **myVnetA - Peerings** bladet som visas, klickar du på **+ Lägg till**
22. I den **Lägg till peering** bladet som visas, ange, eller Välj följande alternativ, och klicka sedan **OK**:
     - **Namnet**: *myVnetAToMyVnetB*
     - **Distributionsmodell för virtuellt nätverk**:  Välj **klassiska**.
     - **Jag vet mitt resurs-ID**: Markera den här kryssrutan.
     - **Resurs-ID**: Ange resurs-ID för myVnetB från steg 15.
     - **Tillåt åtkomst till virtuellt nätverk:** Se till att **aktiverad** har valts.
    Inga andra inställningar som används i den här självstudien. Läs om alla inställningar för peering [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
23. När du klickar på **OK** i föregående steg, den **Lägg till peering** bladet stängs och du ser den **myVnetA - Peerings** bladet igen. Efter några sekunder visas peer-kopplingen du har skapat i bladet. **Ansluten** visas i den **PEERING STATUS** kolumnen för den **myVnetAToMyVnetB** peering du skapade. Peer-kopplingen upprättas. Det finns inget behov av att peerkoppla virtuella nätverk (klassiskt) till det virtuella nätverket (Resource Manager).

    Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
25. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i den [ta bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering – Azure CLI

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla åtgärder, hoppa över stegen för att logga ut från Azure och ta bort raderna för skript som skapar rolltilldelningar för användare. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för UserA och UserB. Utför följande steg med hjälp av den klassiska Azure CLI och Azure CLI. Du kan slutföra stegen från Azure Cloud Shell, genom att bara välja de **prova** knappen i någon av följande steg eller genom att installera den [klassiskt CLI](/cli/azure/install-classic-cli) och [CLI](/cli/azure/install-azure-cli) och köra kommandon på den lokala datorn.

1. Om du använder Cloud Shell, gå vidare till steg 2, eftersom Cloud Shell loggar du in automatiskt till Azure. Öppna en kommandosession och logga in på Azure med hjälp av den `azure login` kommando.
2. Köra klassiska CLI i Service Management-läge genom att ange den `azure config mode asm` kommando.
3. Ange följande kommando för klassiska CLI att skapa det virtuella nätverket (klassisk):

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. De återstående stegen måste utföras med hjälp av ett bash-gränssnitt med Azure CLI (inte klassisk CLI).
5. Kopiera följande skript till en textredigerare på din dator. Ersätt `<SubscriptionB-Id>` med ditt prenumerations-ID. Om du inte vet ditt prenumerations-Id, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-Id. Kopiera det ändrade skriptet, klistra in den i CLI-sessionen och tryck sedan på `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    När du skapade det virtuella nätverket (klassiskt) i steg 4 Azure skapade det virtuella nätverket i den *standard-nätverk* resursgrupp.
6. Logga in användare b utanför Azure och logga in som UserA i CLI.
7. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera följande skript, klistra in den i CLI-sessionen och tryck sedan på `Enter`.

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

8. Skapa en virtuell nätverkspeering mellan två virtuella nätverk som skapats via olika distributionsmodeller. Kopiera följande skript till en textredigerare på din dator. Ersätt `<SubscriptionB-id>` med ditt prenumerations-Id. Om du inte vet ditt prenumerations-Id, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-Id. Azure skapade det virtuella nätverket (klassisk) som du skapade i steg 4 i en resursgrupp med namnet *standard-nätverk*. Klistra in de ändrade skripten i CLI-sessionen och tryck sedan på `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande skript och klistra in den i din CLI-sessionen:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Utdata visar **ansluten** i den **PeeringState** kolumn.

    Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
11. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-cli) i den här artikeln.

## <a name="powershell"></a>Skapa peering - PowerShell

Den här självstudien använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet till båda prenumerationerna kan du använda samma konto för alla åtgärder, hoppa över stegen för att logga ut från Azure och ta bort raderna för skript som skapar rolltilldelningar för användare. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för UserA och UserB. 

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) och [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduler. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell, logga in på användare BS prenumerationen som användare b genom att ange den `Add-AzureAccount` kommando. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
4. Du måste skapa en ny eller ändra en befintlig, network-konfigurationsfil för att skapa ett virtuellt nätverk (klassisk) med PowerShell. Lär dig hur du [exportera, uppdatera och importera nätverkskonfigurationen](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite** elementet för det virtuella nätverket som används i den här självstudien:

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
    > Importera en ändrade nätverkskonfigurationsfil kan orsaka ändringar i befintliga virtuella nätverk (klassiskt) i din prenumeration. Kontrollera att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från din prenumeration. 

5. Logga in på användare BS prenumerationen som användare b använder Resource Manager-kommandon genom att ange den `Connect-AzureRmAccount` kommando.
6. Tilldela användare a behörigheter till virtuellt nätverk B. kopiera följande skript till en textredigerare på din dator och Ersätt `<SubscriptionB-id>` med ID för prenumerationen B. Om du inte vet prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Azure skapade det virtuella nätverket (klassisk) som du skapade i steg 4 i en resursgrupp med namnet *standard-nätverk*. Kopiera det ändrade skriptet för att köra skriptet, klistra in det i PowerShell och tryck sedan på `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Logga ut från Azure som UserB och logga in på Useras prenumerationen som UserA genom att ange den `Connect-AzureRmAccount` kommando. Det konto som du loggar in med måste ha tillräcklig behörighet för att skapa vnet-peering. En lista över behörigheter finns i [peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).
8. Skapa det virtuella nätverket (Resource Manager) genom att kopiera följande skript, klistra in den i PowerShell och sedan trycka på `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Tilldela användare b behörigheter till myVnetA. Kopiera följande skript till en textredigerare på din dator och Ersätt `<SubscriptionA-Id>` med ID för prenumerationen A. Om du inte vet prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Klistra in den ändrade versionen av skriptet i PowerShell och tryck sedan på `Enter` att köra den.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopiera följande skript till en textredigerare på din dator och Ersätt `<SubscriptionB-id>` med ID för prenumerationen B. Kopiera det ändrade skriptet to-peer myVnetA till myVNetB, klistra in det i PowerShell och tryck sedan på `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Visa peerkopplingens status för myVnetA genom att kopiera följande skript, klistra in det i PowerShell och trycka på `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet är **ansluten**. Ändras till **ansluten** när du har konfigurerat peer-kopplingen till myVnetA från myVnetB.

    Azure-resurser som du skapar i de virtuella nätverken är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard Azure namnmatchning för virtuella nätverk, är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn för virtuella nätverk i en peering, måste du skapa en egen DNS-server. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Valfritt**: Även om skapandet av virtuella datorer inte ingår i den här självstudiekursen, kan du skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till den andra är att verifiera anslutningarna.
13. **Valfritt**: Om du vill ta bort de resurser som du skapar i den här självstudien måste du slutföra stegen i [ta bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete"></a>Ta bort resurser
När du har slutfört den här kursen, kanske du vill ta bort alla resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. Ta bort en resursgrupp tar även bort alla resurser i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Ange i portalen sökrutan **myResourceGroupA**. I sökresultaten klickar du på **myResourceGroupA**.
2. På den **myResourceGroupA** bladet klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **Skriv RESURSGRUPPSNAMNET** anger **myResourceGroupA**, och klicka sedan på **ta bort**.
4. I den **Sök efter resurser** högst upp i portalen, Skriv *myVnetB*. Klicka på **myVnetB** när den visas i sökresultaten. Ett blad som visas för den **myVnetB** virtuellt nätverk.
5. I den **myVnetB** bladet klickar du på **ta bort**.
6. Bekräfta borttagningen genom att klicka på **Ja** i den **ta bort virtuellt nätverk** box.

### <a name="delete-cli"></a>Azure CLI

1. Logga in på Azure som använder CLI för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Logga in på Azure som använder klassisk CLI för att ta bort det virtuella nätverket (klassisk) med följande kommandon:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager) i PowerShell-Kommandotolken:

   ```powershell
   Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
   ```

2. Om du vill ta bort det virtuella nätverket (klassisk) med PowerShell kan du ändra en befintlig konfigurationsfil för nätverk. Lär dig hur du [exportera, uppdatera och importera nätverkskonfigurationen](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite element för det virtuella nätverket som används i den här självstudien:

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
   > Importera en ändrade nätverkskonfigurationsfil kan orsaka ändringar i befintliga virtuella nätverk (klassiskt) i din prenumeration. Kontrollera att du bara ta bort det tidigare virtuella nätverket och att du inte ändra eller ta bort alla andra befintliga virtuella nätverk från din prenumeration. 

## <a name="next-steps"></a>Nästa steg

- Noggrant bekanta dig med viktiga [vnet peering begränsningar och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar en virtuell nätverkspeering för produktion använder.
- Lär dig mer om alla [peering-inställningar för virtuellt nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och ekrar nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling.