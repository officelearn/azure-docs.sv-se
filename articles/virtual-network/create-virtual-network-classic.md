---
title: Skapa ett virtuellt Azure-nätverk (klassiskt) med flera undernät | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk (klassiskt) med flera undernät i Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: c41a1057bb87c70362477d221b69ca3f5137dec4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Skapa ett virtuellt nätverk (klassiskt) med flera undernät

> [!IMPORTANT]
> Azure har två [olika distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar de flesta nya virtuella nätverk via den [Resource Manager](quick-create-portal.md) distributionsmodell.

I den här kursen lär du dig hur du skapar en grundläggande Azure virtuellt nätverk (klassiskt) som har olika offentliga och privata undernät. Du kan skapa Azure-resurser, t.ex. virtuella datorer och molntjänster i ett undernät. Resurser som skapas på virtuella nätverk (klassiskt) kan kommunicera med varandra och resurser i andra nätverk som är anslutna till ett virtuellt nätverk.

Mer information om alla [virtuellt nätverk](manage-virtual-network.md) och [undernät](virtual-network-manage-subnet.md) inställningar.

> [!WARNING]
> Virtuella nätverk (klassiskt) omedelbart tas bort av Azure när en [prenumerationen har inaktiverats](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuella nätverk (klassiskt) tas bort oavsett om det finns resurser i det virtuella nätverket. Om du återaktiverar prenumerationen senare måste du återskapa resurser som fanns i det virtuella nätverket.

Du kan skapa ett virtuellt nätverk (klassiskt) med hjälp av den [Azure-portalen](#portal), [Azure-kommandoradsgränssnittet (CLI) 1.0](#azure-cli), eller [PowerShell](#powershell).

## <a name="portal"></a>Portalen

1. I en webbläsare går du till den [Azure-portalen](https://portal.azure.com). Logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klicka på **skapar du en resurs** i portalen.
3. Ange *för virtuella nätverk* i den **söka Marketplace** rutan längst upp i den **ny** fönstret som visas. Klicka på **för virtuella nätverk** när den visas i sökresultaten.
4. Välj **klassiska** i den **Välj en distributionsmodell** rutan den **virtuellt nätverk** fönstret som visas, klicka sedan på **skapa**. 
5. Ange följande värden den **skapa virtuella nätverk (klassiska)** rutan och klicka sedan på **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVnet|
    |Adressutrymme|10.0.0.0/16|
    |Namn på undernät|Offentligt|
    |Adressintervall för undernätet|10.0.0.0/24|
    |Resursgrupp|Lämna **Skapa nytt** markerad och ange sedan **myResourceGroup**.|
    |Prenumerationen och platsen|Välj din prenumeration och plats.

    Om du har använt Azure lär du dig mer om [resursgrupper](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), och [platser](https://azure.microsoft.com/regions) (kallas även *regioner*).
4. Du kan skapa en enda undernät när du skapar ett virtuellt nätverk i portalen. I den här självstudiekursen skapar du ett andra undernät när du har skapat det virtuella nätverket. Senare kan du skapa Internet-tillgängliga resurser i den **offentliga** undernät. Du kan också skapa resurser som inte är tillgänglig från Internet i den **privata** undernät. För att skapa andra undernät, ange **myVnet** i den **söka resurser** rutan längst upp på sidan. Klicka på **myVnet** när den visas i sökresultaten.
5. Klicka på **undernät** (i den **inställningar** avsnitt) på den **skapa virtuella nätverk (klassiska)** fönstret som visas.
6. Klicka på **+ Lägg till** på den **myVnet - undernät** fönstret som visas.
7. Ange **privata** för **namn** på den **Lägg till undernät** fönstret. Ange **10.0.1.0/24** för **adressintervall**.  Klicka på **OK**.
8. På den **myVnet - undernät** rutan som du kan se den **offentliga** och **privata** undernät som du skapade.
9. **Valfria**: när du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter:
    - Klicka på **översikt** på den **myVnet** fönstret.
    - Klicka på den **ta bort** ikon på den **myVnet** fönstret.
    - Bekräfta borttagningen genom att klicka på **Ja** i den **ta bort virtuellt nätverk** rutan.

## <a name="azure-cli"></a>Azure CLI

1. Du kan antingen [installera och konfigurera Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller använda CLI i gränssnittet för Azure-molnet. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill få hjälp med CLI-kommandon, Skriv `azure <command> --help`. 
2. I en CLI-session, logga in Azure med det kommando som följer. Om du klickar på **prova** i rutan nedan öppnas ett gränssnitt för molnet. Du kan logga in på Azure-prenumerationen, utan att ange följande kommando:

    ```azurecli-interactive
    azure login
    ```

3. Om du vill CLI i Service Management-läge kan du ange följande kommando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Skapa ett virtuellt nätverk med ett privat undernät:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Skapa en offentlig undernät i det virtuella nätverket:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Granska de virtuella nätverk och undernät:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Valfria**: kanske du vill ta bort resurser som du skapade när du är klar med den här självstudiekursen så att du inte betalar användningsavgifter:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Även om du inte kan ange en resursgrupp för att skapa ett virtuellt nätverk (klassiskt) i med hjälp av CLI, Azure skapar det virtuella nätverket i en resursgrupp med namnet *standard-nätverk*.

## <a name="powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modul. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in i Azure genom att ange kommandot `Add-AzureAccount`.
4. Ändra följande sökväg och filnamn efter behov, och sedan exportera konfigurationsfilen befintliga nätverk:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Om du vill skapa ett virtuellt nätverk med offentliga och privata undernät, kan du använda valfri textredigerare att lägga till den **VirtualNetworkSite** elementet som följer till konfigurationsfilen på nätverket.

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

    Granska hela [nätverk schemat för konfigurationsfilen](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importera konfigurationsfilen nätverk:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importera en konfigurationsfil för ändrade nätverket kan orsaka ändringar av befintliga virtuella nätverk (klassiskt) i din prenumeration. Se till att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från prenumerationen. 

7. Granska de virtuella nätverk och undernät:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Valfria**: kanske du vill ta bort resurser som du skapade när du är klar med den här självstudiekursen så att du inte betalar användningsavgifter. Om du vill ta bort det virtuella nätverket, Slutför stegen 4-6 igen, den här gången tar bort den **VirtualNetworkSite** element som du lade till i steg 5.
 
> [!NOTE]
> Även om du inte kan ange en resursgrupp för att skapa ett virtuellt nätverk (klassiskt) i med hjälp av PowerShell, Azure skapar det virtuella nätverket i en resursgrupp med namnet *standard-nätverk*.

---

## <a name="next-steps"></a>Nästa steg

- Mer information om alla virtuella nätverk och undernätsinställningar, se [hantera virtuella nätverk](manage-virtual-network.md) och [hantera virtuella undernät](virtual-network-manage-subnet.md). Har du olika alternativ för att använda virtuella nätverk och undernät i en produktionsmiljö för att uppfylla olika krav.
- Filtrera inkommande och utgående trafik, skapa och använda [nätverkssäkerhetsgrupper](virtual-networks-nsg.md) till undernät.
- Skapa en [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorn och ansluter sedan till ett befintligt virtuellt nätverk.
- För att ansluta två virtuella nätverk i samma Azure-plats, skapa en [virtuellt nätverk peering](create-peering-different-deployment-models.md) mellan virtuella nätverk. Du kan peer (Resource Manager) för ett virtuellt nätverk till ett virtuellt nätverk (klassiskt), men du kan inte skapa en peering mellan två virtuella nätverk (klassiskt).
- Anslut det virtuella nätverket till ett lokalt nätverk med hjälp av en [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) krets.
