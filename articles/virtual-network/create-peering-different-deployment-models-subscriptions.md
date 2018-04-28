---
title: Skapa ett virtuellt Azure-nätverk som peering - distribution av olika modeller - olika prenumerationer | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk peering mellan virtuella nätverk som skapats via olika Azure distributionsmodeller som finns i olika Azure-prenumerationer.
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 08a025acb89d3b35798688dc333038fb807284cd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Skapa ett virtuellt nätverk peering - olika distributionsmodeller och prenumerationer

I kursen får du lära dig att skapa ett virtuellt nätverk peering mellan virtuella nätverk som skapats via olika distributionsmodeller. Virtuella nätverk finns i olika prenumerationer. Peering två virtuella nätverk gör resurser i olika virtuella nätverk för att kommunicera med varandra med samma bandbredd och svarstid som om resurserna som fanns i samma virtuella nätverk. Lär dig mer om [virtuella nätverk peering](virtual-network-peering-overview.md).

Stegen för att skapa ett virtuellt nätverk som peering är olika beroende på om de virtuella nätverken är på samma eller olika prenumerationer och som [Azure distributionsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuella nätverken skapas via. Lär dig hur du skapar ett virtuellt nätverk peering i andra scenarier genom att klicka på scenario i följande tabell:

|Azure-distributionsmodell  | Azure-prenumeration  |
|--------- |---------|
|[Båda Resource Manager](tutorial-connect-virtual-networks-portal.md) |samma|
|[Båda Resource Manager](create-peering-different-subscriptions.md) |Annorlunda|
|[En Resource Manager, en klassisk](create-peering-different-deployment-models.md) |samma|

Att går inte skapa ett virtuellt nätverk som peering mellan två virtuella nätverk som distribuerats via den klassiska distributionsmodellen. Den här kursen använder virtuella nätverk som finns i samma region. Den här självstudiekursen peers virtuella nätverk i samma region. Du kan också peer-virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region).  

När du skapar ett virtuellt nätverk peering mellan virtuella nätverk som finns i olika prenumerationer måste prenumerationerna vara kopplad till samma Azure Active Directory-klienten. Om du inte redan har en Azure Active Directory-klient, kan du snabbt [skapar du en](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Du kan ansluta virtuella nätverk för olika prenumerationer och olika Azure Active Directory innehavare med hjälp av en Azure [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Du kan använda den [Azure-portalen](#portal), Azure [kommandoradsgränssnittet](#cli) (CLI) eller Azure [PowerShell](#powershell) att skapa ett virtuellt nätverk som peering. Klicka på någon av föregående verktyget länkar att gå direkt till steg för att skapa ett virtuellt nätverk peering verktyget dina val.

## <a name="portal"></a>Skapa peering - Azure-portalen

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut från portalen och hoppa över steg för att tilldela behörigheter för en annan för de virtuella nätverken.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som UserA. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
2. Klicka på **+ ny**, klickar du på **nätverk**, klicka på **för virtuella nätverk**.
3. I den **skapa virtuellt nätverk** bladet anger, eller Välj värden för följande inställningar och sedan klickar du på **skapa**:
    - **Namnet**: *myVnetA*
    - **Adressutrymmet**: *10.0.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.0.0.0/24*
    - **Prenumerationen**: Välj prenumeration A.
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroupA*
    - **Plats**: *östra USA*
4. I den **söka resurser** rutan överst i portalen, typen *myVnetA*. Klicka på **myVnetA** när den visas i sökresultaten. Ett blad som visas för den **myVnetA** virtuellt nätverk.
5. I den **myVnetA** bladet som visas, klickar du på **åtkomstkontroll (IAM)** från lodräta listan till vänster på bladet.
6. I den **myVnetA - åtkomstkontroll (IAM)** bladet som visas, klickar du på **+ Lägg till**.
7. I den **lägga till behörigheter** bladet som visas, väljer **Network-deltagare** i den **rollen** rutan.
8. I den **Välj** markerar b, eller Skriv BS e-postadress för att söka efter den. En lista över användare som visas är från samma Azure Active Directory-klientorganisation som det virtuella nätverket som du konfigurerar peering för. Klicka på b när den visas i listan.
9. Klicka på **Spara**.
10. Logga ut från portalen som UserA och sedan logga in som användare b.
11. Klickar du på **+ ny**, typen *för virtuella nätverk* i den **söka Marketplace** rutan och klicka sedan på **för virtuella nätverk** i sökresultaten.
12. I den **virtuellt nätverk** bladet som visas, väljer **klassiska** i den **Välj en distributionsmodell** rutan och klicka sedan på **skapa**.
13.   Ange följande värden i virtuella nätverk (klassiskt) rutan som visas:

    - **Namnet**: *myVnetB*
    - **Adressutrymmet**: *10.1.0.0/16*
    - **Undernätnamnet**: *standard*
    - **Adressintervall för gatewayundernät**: *10.1.0.0/24*
    - **Prenumerationen**: Välj prenumeration B.
    - **Resursgruppen**: Välj **Skapa nytt** och ange *myResourceGroupB*
    - **Plats**: *östra USA*

14. I den **söka resurser** rutan överst i portalen, typen *myVnetB*. Klicka på **myVnetB** när den visas i sökresultaten. Ett blad som visas för den **myVnetB** virtuellt nätverk.
15. I den **myVnetB** bladet som visas, klickar du på **egenskaper** från lodräta listan till vänster på bladet. Kopiera den **resurs-ID**, som används i ett senare steg. Resurs-ID som liknar följande exempel: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Slutför steg 5 – 9 för myVnetB, ange **UserA** i steg 8.
17. Logga ut från portalen som användare b och logga in som UserA.
18. I den **söka resurser** rutan överst i portalen, typen *myVnetA*. Klicka på **myVnetA** när den visas i sökresultaten. Ett blad som visas för den **myVnet** virtuellt nätverk.
19. Klicka på **myVnetA**.
20. I den **myVnetA** bladet som visas, klickar du på **Peerkopplingar** från lodräta listan till vänster på bladet.
21. I den **myVnetA - Peerkopplingar** bladet som visas, klickar du på **+ Lägg till**
22. I den **Lägg till peering** bladet som visas anger, eller Välj följande alternativ och klicka på **OK**:
     - **Namnet**: *myVnetAToMyVnetB*
     - **Virtuellt nätverk distributionsmodell**: Välj **klassiska**.
     - **Jag vet mitt resurs-ID**: den här kryssrutan.
     - **Resurs-ID**: Ange resurs-ID för myVnetB från steg 15.
     - **Tillåt åtkomst till virtuella nätverk:** se till att **aktiverad** är markerad.
    Inga andra inställningar används i den här kursen. Mer information om alla peering inställningar, läsa [hantera peerkopplingar mellan virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
23. När du klickar på **OK** i föregående steg i **Lägg till peering** blad stängs och du ser den **myVnetA - Peerkopplingar** bladet igen. Efter några sekunder visas peering du skapat i bladet. **Ansluten** ingår i den **PEERING STATUS** kolumnen för den **myVnetAToMyVnetB** peering du skapat. Peering upprättas. Det finns inget behov av att peer-virtuella nätverk (klassiska) till det virtuella nätverket (Resource Manager).

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
25. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i den [bort resurser](#delete-portal) i den här artikeln.

## <a name="cli"></a>Skapa peering - Azure CLI

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut ur Azure och ta bort rader i skriptet som skapar användaren rolltilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för användare a och b. 

1. [Installera](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 1.0 att skapa virtuella nätverk (klassiska).
2. Öppna en CLI-session och logga in på Azure som användare b använder den `azure login` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
3. Kör CLI i Service Management-läge genom att ange den `azure config mode asm` kommando.
4. Ange följande kommando för att skapa virtuella nätverk (klassiska):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Stegen måste utföras med hjälp av ett bash skal med Azure CLI 2.0.4 eller senare [installerat](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), eller genom att använda Azure Cloud-gränssnittet. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på den **prova** knappen i skripten fram, vilket öppnar ett moln-gränssnitt som loggar in på ditt Azure-konto. Alternativen på körs bash CLI-skript på en Windows-klient, finns [kör Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-Id>` med ditt prenumerations-ID. Om du inte vet ditt prenumerations-Id, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-Id. Kopiera skriptet ändrade, klistra in den i sessionen CLI 2.0 och tryck sedan på `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    När du skapade det virtuella nätverket (klassiskt) i steg 4 Azure skapade det virtuella nätverket i den *standard-nätverk* resursgruppen.
7. Logga b utanför Azure och logga in som UserA i CLI 2.0.
8. Skapa en resursgrupp och ett virtuellt nätverk (Resource Manager). Kopiera följande skript, klistra in den i CLI-sessionen och tryck sedan på `Enter`. 

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

9. Skapa ett virtuellt nätverk peering mellan de två virtuella nätverk som skapats via olika distributionsmodeller. Kopiera följande skript till en textredigerare på datorn. Ersätt `<SubscriptionB-id>` med ditt prenumerations-Id. Om du inte vet ditt prenumerations-Id, ange den `az account show` kommando. Värdet för **id** i utdata är ditt prenumerations-Id. Azure skapade det virtuella nätverket (klassisk) som du skapade i steg 4 i en resursgrupp med namnet *standard-nätverk*. Klistra in ändrade skript i CLI-sessionen och tryck sedan på `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. När skriptet körs, kan du granska peering för det virtuella nätverket (Resource Manager). Kopiera följande skript och klistra in den i sessionen CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Utdata visar **ansluten** i den **PeeringState** kolumn.

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

11. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
12. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-cli) i den här artikeln.

## <a name="powershell"></a>Skapa peering - PowerShell

Den här kursen använder olika konton för varje prenumeration. Om du använder ett konto som har behörighet att båda prenumerationer du använder samma konto för alla åtgärder, hoppa över steg för att logga ut ur Azure och ta bort rader i skriptet som skapar användaren rolltilldelningar. Ersätt UserA@azure.com och UserB@azure.com i alla följande skript med det användarnamn som du använder för användare a och b. 

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) och [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduler. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell, logga in till BS prenumeration som b genom att ange den `Add-AzureAccount` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
4. Du måste skapa en ny eller ändra en befintlig, network-konfigurationsfil om du vill skapa ett virtuellt nätverk (klassiskt) med PowerShell. Lär dig hur du [exportera, uppdatera och importera konfigurationsfiler för nätverket](virtual-networks-using-network-configuration-file.md). Filen bör innehålla följande **VirtualNetworkSite** element för det virtuella nätverket används i den här kursen:

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
    > Importera en konfigurationsfil för ändrade nätverket kan orsaka ändringar av befintliga virtuella nätverk (klassiskt) i din prenumeration. Se till att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från prenumerationen. 

5. Logga in till BS prenumeration som användare b använder Resource Manager-kommandon genom att ange den `Connect-AzureRmAccount` kommando.
6. Tilldela behörigheter för UserA till virtuellt nätverk B. kopiera följande skript för att en textredigerare på datorn och Ersätt `<SubscriptionB-id>` med ID för prenumerationen B. Om du inte vet prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Azure skapade det virtuella nätverket (klassisk) som du skapade i steg 4 i en resursgrupp med namnet *standard-nätverk*. Om du vill köra skriptet kopiera skriptet ändrade, klistra in den i PowerShell och tryck sedan på `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Logga ut från Azure som b och logga in till Useras prenumeration som UserA genom att ange den `Connect-AzureRmAccount` kommando. Du loggar in med kontot måste ha behörighet för att skapa ett virtuellt nätverk som peering. En lista över behörigheter finns [virtuella nätverk peering behörigheter](virtual-network-manage-peering.md#permissions).
8. Skapa virtuella nätverk (Resource Manager) genom att kopiera följande skript, klistra in den i PowerShell och sedan trycka på `Enter`:

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

9. Tilldela myVnetA b behörigheter. Kopiera följande skript till en textredigerare på datorn och Ersätt `<SubscriptionA-Id>` med ID för prenumerationen A. Om du inte vet prenumerations-Id, ange den `Get-AzureRmSubscription` kommando för att visa den. Värdet för **Id** returnerade resultatet är ditt prenumerations-ID. Klistra in den ändrade versionen av skriptet i PowerShell och tryck sedan på `Enter` att köra den.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopiera följande skript till en textredigerare på datorn och Ersätt `<SubscriptionB-id>` med ID för prenumerationen B. To-peer-myVnetA till myVNetB, kopiera skriptet ändrade, klistra in den i PowerShell och tryck sedan på `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Visa peering status för myVnetA genom att kopiera följande skript, klistrar in det i PowerShell och trycka på `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Tillståndet är **ansluten**. Den ändras till **ansluten** när du ställer in peering till myVnetA från myVnetB.

    Azure-resurser som du skapar i antingen virtuellt nätverk är nu kunna kommunicera med varandra via sina IP-adresser. Om du använder standard-Azure namnmatchning för de virtuella nätverken är resurser i de virtuella nätverken inte kan matcha namnen på de virtuella nätverken. Om du vill matcha namn över virtuella nätverk i en peering måste du skapa DNS-servern. Lär dig hur du ställer in [namnmatchning med hjälp av DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Valfria**: även om att skapa virtuella datorer inte ingår i den här självstudiekursen, du kan skapa en virtuell dator i varje virtuellt nätverk och ansluta från en virtuell dator till ett annat genom att verifiera anslutningarna.
13. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-powershell) i den här artikeln.

## <a name="delete"></a>Ta bort resurser
När du har läst den här självstudiekursen, kanske du vill ta bort de resurser som du skapade i självstudierna så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Skriv i sökrutan portal **myResourceGroupA**. I sökresultaten klickar du på **myResourceGroupA**.
2. På den **myResourceGroupA** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroupA**, och klicka sedan på **ta bort**.
4. I den **söka resurser** rutan överst i portalen, typen *myVnetB*. Klicka på **myVnetB** när den visas i sökresultaten. Ett blad som visas för den **myVnetB** virtuellt nätverk.
5. I den **myVnetB** bladet, klickar du på **ta bort**.
6. Bekräfta borttagningen genom att klicka på **Ja** i den **ta bort virtuellt nätverk** rutan.

### <a name="delete-cli"></a>Azure CLI

1. Logga in på Azure med hjälp av CLI 2.0 för att ta bort det virtuella nätverket (Resource Manager) med följande kommando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Logga in på Azure med hjälp av Azure CLI 1.0 att ta bort det virtuella nätverket (klassisk) med följande kommandon:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Ange följande kommando för att ta bort det virtuella nätverket (Resource Manager) på PowerShell-Kommandotolken:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Om du vill ta bort det virtuella nätverket (klassiskt) med PowerShell, måste du ändra en befintlig konfigurationsfil i nätverket. Lär dig hur du [exportera, uppdatera och importera konfigurationsfiler för nätverket](virtual-networks-using-network-configuration-file.md). Ta bort följande VirtualNetworkSite element för det virtuella nätverket används i den här kursen:

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
    > Importera en konfigurationsfil för ändrade nätverket kan orsaka ändringar av befintliga virtuella nätverk (klassiskt) i din prenumeration. Se till att du bara tar bort det tidigare virtuella nätverket och att du inte ändra eller ta bort andra befintliga virtuella nätverk från prenumerationen. 

## <a name="next-steps"></a>Nästa steg

- Väl bekanta dig med viktiga [peering begränsningar för virtuellt nätverk och beteenden](virtual-network-manage-peering.md#requirements-and-constraints) innan du skapar ett virtuellt nätverk peering för produktion använder.
- Lär dig mer om alla [peering inställningar för virtuella nätverk](virtual-network-manage-peering.md#create-a-peering).
- Lär dig hur du [skapa ett nav och eker nätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med virtuella nätverk peering.
