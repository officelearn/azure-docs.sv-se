---
title: Skapa ett Azure-nätverk (klassiskt) med flera undernät | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk (klassisk) med flera undernät i Azure.
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098124"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Skapa ett virtuellt nätverk (klassiskt) med flera undernät

> [!IMPORTANT]
> Azure har två [olika distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att skapa de flesta nya virtuella nätverk via den [Resource Manager](quick-create-portal.md) distributionsmodell.

I de här självstudierna lär du dig hur du skapar en grundläggande Azure-nätverk (klassisk) som har olika offentliga och privata undernät. Du kan skapa Azure-resurser, t.ex. virtuella datorer och molntjänster i ett undernät. Resurser som skapats i virtuella nätverk (klassisk) kan kommunicera med varandra och med resurser i andra nätverk som är anslutna till ett virtuellt nätverk.

Mer information om alla [virtuellt nätverk](manage-virtual-network.md) och [undernät](virtual-network-manage-subnet.md) inställningar.

> [!WARNING]
> Virtuella nätverk (klassisk) raderas omedelbart av Azure när en [prenumerationen har inaktiverats](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuella nätverk (klassisk) tas bort, oavsett om det finns resurser i det virtuella nätverket. Om du senare aktivera prenumerationen igen, måste du återskapa resurser som fanns i det virtuella nätverket.

Du kan skapa ett virtuellt nätverk (klassisk) med hjälp av den [Azure-portalen](#portal), [Azure-kommandoradsgränssnittet (CLI) 1.0](#azure-cli), eller [PowerShell](#powershell).

## <a name="portal"></a>Portalen

1. I en webbläsare går du till den [Azure-portalen](https://portal.azure.com). Logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klicka på **skapa en resurs** i portalen.
3. Ange *virtuellt nätverk* i den **Sök på Marketplace** rutan högst upp på den **New** fönstret som visas. Klicka på **virtuellt nätverk** när den visas i sökresultaten.
4. Välj **klassiska** i den **Välj en distributionsmodell** rutan den **virtuellt nätverk** fönstret som visas, klicka sedan på **skapa**. 
5. Ange följande värden den **skapa virtuellt nätverk (klassisk)** fönstret och klicka sedan på **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVnet|
    |Adressutrymme|10.0.0.0/16|
    |Namn på undernät|Offentligt|
    |Adressintervall för undernätet|10.0.0.0/24|
    |Resursgrupp|Lämna **Skapa nytt** markerad och ange sedan **myResourceGroup**.|
    |Prenumeration och plats|Välj din prenumeration och plats.

    Om du är nybörjare på Azure kan du läsa mer om [resursgrupper](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), och [platser](https://azure.microsoft.com/regions) (kallas även *regioner*).
4. Du kan skapa bara ett undernät när du skapar ett virtuellt nätverk i portalen. I den här självstudien skapar du ett andra undernät när du har skapat det virtuella nätverket. Du kan senare skapa Internet-tillgängliga resurser i den **offentliga** undernät. Du kan också skapa resurser som inte är tillgänglig från Internet i den **privata** undernät. Om du vill skapa ett andra undernät, ange **myVnet** i den **Sök efter resurser** rutan längst upp på sidan. Klicka på **myVnet** när den visas i sökresultaten.
5. Klicka på **undernät** (i den **inställningar** avsnitt) på den **skapa virtuellt nätverk (klassisk)** fönstret som visas.
6. Klicka på **+ Lägg till** på den **myVnet – undernät** fönstret som visas.
7. Ange **privata** för **namn** på den **Lägg till undernät** fönstret. Ange **10.0.1.0/24** för **adressintervall**.  Klicka på **OK**.
8. På den **myVnet – undernät** fönstret du ser den **offentliga** och **privata** undernät som du skapade.
9. **Valfritt**: När du är klar med den här självstudien kanske du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter:
    - Klicka på **översikt** på den **myVnet** fönstret.
    - Klicka på den **ta bort** -ikonen i den **myVnet** fönstret.
    - Bekräfta borttagningen genom att klicka på **Ja** i den **ta bort virtuellt nätverk** box.

## <a name="azure-cli"></a>Azure CLI

1. Du kan antingen [installera och konfigurera Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), eller använda CLI i Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill få hjälp med CLI-kommandon, Skriv `azure <command> --help`. 
2. En CLI-session och logga in på i Azure med kommandot nedan. Om du klickar på **prova** i rutan nedan en Cloud Shell öppnas. Du kan logga in på Azure-prenumerationen, utan att ange följande kommando:

    ```azurecli-interactive
    azure login
    ```

3. För att säkerställa CLI finns i Service Management-läge, anger du följande kommando:

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
    
6. Granska virtuellt nätverk och undernät:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Valfritt**: Du kanske vill ta bort de resurser som du skapade när du är klar med den här självstudien så att du inte betalar användningsavgifter:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Även om du inte kan ange en resursgrupp för att skapa ett virtuellt nätverk (klassiskt) i med hjälp av CLI, Azure skapar det virtuella nätverket i en resursgrupp med namnet *standard-nätverk*.

## <a name="powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starta en PowerShell-session.
3. I PowerShell loggar du in i Azure genom att ange kommandot `Add-AzureAccount`.
4. Ändra följande sökväg och filnamn, efter behov, och sedan exportera dina befintliga nätverkskonfigurationsfilen:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Använd valfri textredigerare för att skapa ett virtuellt nätverk med offentliga och privata undernät, att lägga till den **VirtualNetworkSite** element efter nätverkskonfigurationsfilen.

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

    Granska fullständiga [nätverksschemat för konfigurationsfilen](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importera nätverkskonfigurationsfilen:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importera en ändrade nätverkskonfigurationsfil kan orsaka ändringar i befintliga virtuella nätverk (klassiskt) i din prenumeration. Kontrollera att du bara lägga till det tidigare virtuella nätverket och att du inte ändra eller ta bort alla befintliga virtuella nätverk från din prenumeration. 

7. Granska virtuellt nätverk och undernät:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Valfritt**: Du kanske vill ta bort alla resurser som du skapade när du är klar med den här självstudien så att du inte betalar användningsavgifter. Om du vill ta bort det virtuella nätverket, Slutför stegen 4 – 6 igen, den här tiden tar bort den **VirtualNetworkSite** element som du lade till i steg 5.
 
> [!NOTE]
> Även om du inte kan ange en resursgrupp för att skapa ett virtuellt nätverk (klassiskt) i med hjälp av PowerShell, Azure skapar det virtuella nätverket i en resursgrupp med namnet *standard-nätverk*.

---

## <a name="next-steps"></a>Nästa steg

- Mer information om alla virtuella nätverk och undernätsinställningar, se [hantera virtuella nätverk](manage-virtual-network.md) och [hantera virtuella undernätverk](virtual-network-manage-subnet.md). Har du olika alternativ för att använda virtuella nätverk och undernät i en produktionsmiljö för att uppfylla olika krav.
- Skapa en [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorn och ansluter den till ett befintligt virtuellt nätverk.
- Om du vill ansluta två virtuella nätverk i samma Azure-plats, skapa en [virtuell nätverkspeering](create-peering-different-deployment-models.md) mellan virtuella nätverk. Peerkoppling kan upprättas mellan ett virtuellt nätverk (Resource Manager) till ett virtuellt nätverk (klassisk), men du kan inte skapa en peering mellan två virtuella nätverk (klassisk).
- Ansluta det virtuella nätverket till ett lokalt nätverk med hjälp av en [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) krets.
