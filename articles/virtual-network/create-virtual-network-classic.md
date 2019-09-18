---
title: Skapa ett virtuellt Azure-nätverk (klassiskt) med flera undernät | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk (klassiskt) med flera undernät i Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059014"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Skapa ett virtuellt nätverk (klassiskt) med flera undernät

> [!IMPORTANT]
> Azure har två [olika distributions modeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att du skapar de flesta nya virtuella nätverk via distributions modellen för [Resource Manager](quick-create-portal.md) .

I den här självstudien får du lära dig hur du skapar ett grundläggande virtuellt Azure-nätverk (klassisk) som har separata offentliga och privata undernät. Du kan skapa Azure-resurser, t. ex. virtuella datorer och moln tjänster i ett undernät. Resurser som har skapats i virtuella nätverk (klassiska) kan kommunicera med varandra, och med resurser i andra nätverk som är anslutna till ett virtuellt nätverk.

Läs mer om alla inställningar för [virtuella nätverk](manage-virtual-network.md) och [undernät](virtual-network-manage-subnet.md) .

> [!WARNING]
> Virtuella nätverk (klassiska) tas genast bort av Azure när en [prenumeration är inaktive rad](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuella nätverk (klassiska) tas bort oavsett om det finns resurser i det virtuella nätverket. Om du senare aktiverar prenumerationen måste resurser som fanns i det virtuella nätverket återskapas.

Du kan skapa ett virtuellt nätverk (klassisk) med hjälp av [Azure Portal](#portal), [kommando rads gränssnittet för Azure (CLI) 1,0](#azure-cli)eller [PowerShell](#powershell).

## <a name="portal"></a>Portalen

1. Gå till [Azure Portal](https://portal.azure.com)i en webbläsare. Logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klicka på **skapa en resurs** i portalen.
3. Ange *virtuellt nätverk* i rutan **Sök i Marketplace** överst i fönstret **nytt** som visas. Klicka på **virtuellt nätverk** när det visas i Sök resultaten.
4. Välj **klassisk** i rutan **Välj en distributions modell** i fönstret **Virtual Network** som visas och klicka sedan på **skapa**. 
5. Ange följande värden i fönstret **Skapa virtuellt nätverk (klassisk)** och klicka sedan på **skapa**:

    |Inställning|Värde|
    |---|---|
    |Name|myVnet|
    |Adressutrymme|10.0.0.0/16|
    |Undernätsnamn|Offentligt|
    |Undernätsadressintervall|10.0.0.0/24|
    |Resource group|Lämna **Skapa ny** markerad och ange sedan **myResourceGroup**.|
    |Prenumeration och plats|Välj din prenumeration och plats.

    Om du är nybörjare på Azure kan du läsa mer om [resurs grupper](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)och [platser](https://azure.microsoft.com/regions) (kallas även *regioner*).
4. I portalen kan du bara skapa ett undernät när du skapar ett virtuellt nätverk. I den här självstudien skapar du ett andra undernät när du har skapat det virtuella nätverket. Du kan senare skapa Internet-tillgängliga resurser i det **offentliga** under nätet. Du kan också skapa resurser som inte är tillgängliga från Internet i det **privata** under nätet. Om du vill skapa det andra under nätet anger du **myVnet** i rutan **Sök resurser** överst på sidan. Klicka på **myVnet** när den visas i Sök resultaten.
5. Klicka på **undernät** (i avsnittet **Inställningar** ) i fönstret **skapa ett virtuellt nätverk (klassiskt)** som visas.
6. Klicka på **+ Lägg till** i fönstret **myVnet-undernät** som visas.
7. Ange **Private** som **namn** i rutan **Lägg till undernät** . Ange **10.0.1.0/24** för **adress intervall**.  Klicka på **OK**.
8. I fönstret **myVnet-undernät** kan du se de **offentliga** och **privata** undernät som du har skapat.
9. **Valfritt**: När du har slutfört den här självstudien kanske du vill ta bort de resurser som du har skapat, så att du inte debiteras för användnings kostnader:
    - Klicka på **Översikt** i fönstret **myVnet** .
    - Klicka på ikonen **ta bort** i fönstret **myVnet** .
    - Bekräfta borttagningen genom att klicka på **Ja** i rutan **ta bort virtuellt nätverk** .

## <a name="azure-cli"></a>Azure CLI

1. Du kan antingen [Installera och konfigurera Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använda CLI i Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill ha hjälp med CLI- `azure <command> --help`kommandon skriver du. 
2. Logga in på Azure med kommandot som följer i en CLI-session. Om du klickar på **prova** i rutan nedan öppnas en Cloud Shell. Du kan logga in på din Azure-prenumeration utan att ange följande kommando:

    ```azurecli-interactive
    azure login
    ```

3. För att se till att CLI är i Service Management-läge, anger du följande kommando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Skapa ett virtuellt nätverk med ett privat undernät:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Skapa ett offentligt undernät i det virtuella nätverket:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Granska det virtuella nätverket och undernät:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Valfritt**: Du kanske vill ta bort de resurser som du skapade när du har slutfört den här självstudien så att du inte debiteras för användnings kostnader:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Även om du inte kan ange en resurs grupp för att skapa ett virtuellt nätverk (klassisk) i med hjälp av CLI, skapar Azure det virtuella nätverket i en resurs grupp med namnet *default-Networking*.

## <a name="powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) -modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in i Azure genom att ange kommandot `Add-AzureAccount`.
4. Ändra följande sökväg och fil namn, om det behövs, och exportera sedan din befintliga nätverks konfigurations fil:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Om du vill skapa ett virtuellt nätverk med offentliga och privata undernät använder du valfri text redigerare för att lägga till **VirtualNetworkSite** -elementet som följer med nätverks konfigurations filen.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Granska det fullständiga [schemat för nätverks konfigurations filen](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importera nätverks konfigurations filen:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Om du importerar en ändrad nätverks konfigurations fil kan det orsaka ändringar i befintliga virtuella nätverk (klassisk) i din prenumeration. Se till att du bara lägger till det tidigare virtuella nätverket och att du inte ändrar eller tar bort befintliga virtuella nätverk från din prenumeration. 

7. Granska det virtuella nätverket och undernät:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Valfritt**: Du kanske vill ta bort de resurser som du skapade när du har slutfört den här självstudien så att du inte debiteras för användnings kostnader. Om du vill ta bort det virtuella nätverket slutför du steg 4-6 igen, tar den här tiden bort **VirtualNetworkSite** -elementet som du lade till i steg 5.
 
> [!NOTE]
> Även om du inte kan ange en resurs grupp för att skapa ett virtuellt nätverk (klassisk) i med PowerShell, skapar Azure det virtuella nätverket i en resurs grupp med namnet *default-Networking*.

---

## <a name="next-steps"></a>Nästa steg

- Information om alla inställningar för virtuella nätverk och undernät finns i [Hantera virtuella](manage-virtual-network.md) nätverk och [Hantera virtuella nätverks under nät](virtual-network-manage-subnet.md). Du har olika alternativ för att använda virtuella nätverk och undernät i en produktions miljö för att uppfylla olika krav.
- Skapa en [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -virtuell dator och Anslut den sedan till ett befintligt virtuellt nätverk.
- Om du vill ansluta två virtuella nätverk på samma Azure-plats skapar du en [virtuell nätverks-peering](create-peering-different-deployment-models.md) mellan de virtuella nätverken. Du kan peer-koppla ett virtuellt nätverk (Resource Manager) till ett virtuellt nätverk (klassiskt), men du kan inte skapa en peering mellan två virtuella nätverk (klassisk).
- Anslut det virtuella nätverket till ett lokalt nätverk med hjälp av en [VPN gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -krets.
