---
title: "Skapa ett virtuellt Azure-nätverk med flera undernät | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med flera undernät i Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Skapa ett virtuellt nätverk med flera undernät

I den här kursen lär du dig hur du skapar en grundläggande Azure virtuellt nätverk med separata offentliga och privata undernät. Resurser i virtuella nätverk kan kommunicera med varandra och resurser i andra nätverk som är anslutna till ett virtuellt nätverk. Du kan skapa Azure-resurser, t.ex. virtuella datorer, apptjänstmiljöer, skalningsuppsättningar i virtuella datorer, Azure HDInsight och molntjänster i samma eller olika undernät i ett virtuellt nätverk. Att skapa resurser i olika undernät kan du filtrera nätverkstrafik till och från undernät oberoende med [nätverkssäkerhetsgrupper](virtual-networks-create-nsg-arm-pportal.md), och [vidarebefordra trafik mellan undernät](virtual-network-create-udr-arm-ps.md) via nätverket virtuella enheter, till exempel en brandvägg, om du väljer. 

De följande avsnitten innehåller instruktioner om hur du kan skapa ett virtuellt nätverk med hjälp av den [Azure-portalen](#portal), Azure-kommandoradsgränssnittet ([Azure CLI](#azure-cli)), [Azure PowerShell](#powershell), och en [Azure Resource Manager-mall](#resource-manager-template). Resultatet är samma, oavsett vilket verktyg du använder för att skapa det virtuella nätverket. Klicka för att gå till avsnittet av kursen. Mer information om alla [virtuellt nätverk](virtual-network-manage-network.md) och [undernät](virtual-network-manage-subnet.md) inställningar.

Den här artikeln innehåller steg för att skapa ett virtuellt nätverk med distributionsmodell hanteraren för filserverresurser, som är den distributionsmodell som vi rekommenderar att du använder när du skapar nya virtuella nätverk. Om du behöver skapa ett virtuellt nätverk (klassiska), se [skapa ett virtuellt nätverk (klassiska)](create-virtual-network-classic.md). Om du inte är bekant med Azures distributionsmodeller [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Azure-portalen

1. I en webbläsare går du till den [Azure-portalen](https://portal.azure.com). Logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p).
2. I portalen klickar du på **+ ny** > **nätverk** > **för virtuella nätverk**.
3. På den **skapa virtuellt nätverk** bladet, ange följande värden och klicka sedan på **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVnet|
    |Adressutrymme|10.0.0.0/16|
    |Namn på undernät|Offentligt|
    |Adressintervall för undernätet|10.0.0.0/24|
    |Resursgrupp|Lämna **Skapa nytt** markerad och ange sedan **myResourceGroup**.|
    |Prenumerationen och platsen|Välj din prenumeration och plats.

    Om du har använt Azure lär du dig mer om [resursgrupper](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [prenumerationer](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), och [platser](https://azure.microsoft.com/regions) (kallas även *regioner*).
4. Du kan skapa en enda undernät när du skapar ett virtuellt nätverk i portalen. I den här självstudiekursen skapar du ett andra undernät när du har skapat det virtuella nätverket. Senare kan du skapa Internet-tillgängliga resurser i den **offentliga** undernät. Du kan också skapa resurser som inte är tillgänglig från Internet i den **privata** undernät. Skapa andra undernät i den **söka resurser** överst på sidan, ange **myVnet**. I sökresultaten klickar du på **myVnet**. Om du har flera virtuella nätverk med samma namn i din prenumeration kan du kontrollera resursgrupper som visas under varje virtuellt nätverk. Se till att du klickar på den **myVnet** sökresultat som har resursgruppen **myResourceGroup**.
5. På den **myVnet** bladet under **inställningar**, klickar du på **undernät**.
6. På den **myVnet - undernät** bladet, klickar du på **+ undernät**.
7. På den **Lägg till undernät** bladet för **namn**, ange **privata**. För **adressintervall**, ange **10.0.1.0/24**.  Klicka på **OK**.
8. På den **myVnet - undernät** bladet granska undernäten. Du kan se den **offentliga** och **privata** undernät som du skapade.
9. **Valfritt:** slutföra ytterligare självstudier som visas under [nästa steg](#next-steps) att filtrera nätverkstrafik till och från varje undernät med nätverkssäkerhetsgrupper att vidarebefordra trafik mellan undernät via en virtuell nätverksenhet , eller att ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk.
10. **Valfritt:** bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-portal).

## <a name="azure-cli"></a>Azure CLI

Azure CLI-kommandona är desamma, om du kör kommandon från Windows, Linux eller macOS. Det finns dock scripting skillnader mellan gränssnitt för operativsystemet. I följande steg körs i ett Bash-gränssnitt. 

1. [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI installerad. Om du vill få hjälp med CLI-kommandon, Skriv `az <command> --help`. Du kan använda Azure Cloud-gränssnittet i stället för att installera CLI och dess krav. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Moln-gränssnittet har Azure CLI förinstallerat och konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell (**> _**) längst upp i den [portal](https://portal.azure.com) eller klicka bara på den *prova* knapp i de steg som följer. 
2. Om körs CLI lokalt kan du logga in på Azure med den `az login` kommando. Om du använder molntjänster Shell är du redan inloggad.
3. Granska följande skript och dess kommentarer. Kopiera skriptet i webbläsaren och klistra in den i sessionen CLI:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. När skriptet har slutförts körs, granska undernät för det virtuella nätverket. Kopiera följande kommando och klistra in den i sessionen CLI:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Valfritt:** slutföra ytterligare självstudier som visas under [nästa steg](#next-steps) att filtrera nätverkstrafik till och från varje undernät med nätverkssäkerhetsgrupper att vidarebefordra trafik mellan undernät via en virtuell nätverksenhet , eller att ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk.
6. **Valfria**: ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I PowerShell-sessionen, logga in på Azure med din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) med hjälp av den `login-azurermaccount` kommando.

3. Granska följande skript och dess kommentarer. Kopiera skriptet i webbläsaren och klistra in den i PowerShell-sessionen:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Kopiera följande kommando för att granska undernät för det virtuella nätverket, och klistra in den i PowerShell-sessionen:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Valfritt:** slutföra ytterligare självstudier som visas under [nästa steg](#next-steps) att filtrera nätverkstrafik till och från varje undernät med nätverkssäkerhetsgrupper att vidarebefordra trafik mellan undernät via en virtuell nätverksenhet , eller att ansluta det virtuella nätverket till andra virtuella nätverk eller lokala nätverk.
6. **Valfria**: ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-powershell).

## <a name="resource-manager-template"></a>Resource Manager-mall

Du kan distribuera ett virtuellt nätverk med en Azure Resource Manager-mall. Mer information om mallar finns [vad är Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Åtkomst till mallen och lär dig mer om dess parametrar finns i [skapa ett virtuellt nätverk med två undernät](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) mall. Du kan distribuera mallen med hjälp av den [portal](#template-portal), [Azure CLI](#template-cli), eller [PowerShell](#template-powershell).

Valfria steg när du distribuerar mallen:

1. Slutföra ytterligare självstudier som visas under [nästa steg](#next-steps) att filtrera nätverkstrafik till och från varje undernät med nätverkssäkerhetsgrupper att vidarebefordra trafik mellan undernät via en virtuell nätverksenhet, eller att ansluta den virtuella nätverk till andra virtuella nätverk eller lokala nätverk.
2. Ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i alla avsnitt i [bort resurser](#delete).

### <a name="template-portal"></a>Azure-portalen

1. I webbläsaren och öppna den [mallsidan](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Klicka på den **till Azure** knappen. Om du inte redan är inloggad på Azure, kan du logga in på Azure portal inloggningsskärmen som visas.
3. Logga in på portalen med hjälp av din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Ange följande värden för parametrarna:

    |Parameter|Värde|
    |---|---|
    |Prenumeration|Välj din prenumeration|
    |Resursgrupp|myResourceGroup|
    |Plats|Välj en plats|
    |Namn på virtuella nätverk|myVnet|
    |Vnet-adressprefix|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Offentligt|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privat|

5. Godkänner du villkoren och klicka sedan på **inköp** att distribuera det virtuella nätverket.

### <a name="template-cli"></a>Azure CLI

1. [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI installerad. Om du vill få hjälp med CLI-kommandon, Skriv `az <command> --help`. Du kan använda Azure Cloud-gränssnittet i stället för att installera CLI och dess krav. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Moln-gränssnittet har Azure CLI förinstallerat och konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell **> _** längst upp i den [portal](https://portal.azure.com), eller klicka bara på den **prova** knapp i de steg som följer. 
2. Om körs CLI lokalt kan du logga in på Azure med den `az login` kommando. Om du använder molntjänster Shell är du redan inloggad.
3. Kopiera följande kommando för att skapa en resursgrupp för det virtuella nätverket, och klistra in den i sessionen CLI:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Du kan distribuera mallen med hjälp av något av följande alternativ för parametrar:
    - **Standardvärdena för parametern**. Ange följande kommando:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Anpassade parametervärden**. Hämta och ändra mallen innan du distribuerar mallen. Du också distribuera mallen med hjälp av parametrar på kommandoraden eller distribuera mallen med en separat parameterfil. Om du vill hämta filer för mallen och parametrar, klickar du på den **Bläddra på GitHub** knappen på den [skapa ett virtuellt nätverk med två undernät](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) mall. I GitHub klickar du på den **azuredeploy.parameters.json** eller **azuredeploy.json** fil. Klicka på **Raw** knappen för att visa filen. Kopiera innehållet i filen i webbläsaren. Spara innehållet till en fil på din dator. Du kan ändra parametervärden i mallen, eller distribuera mallen med en separat parameterfil.  

    Mer information om hur du distribuerar mallar med hjälp av dessa metoder skriver `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I PowerShell-sessionen, logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), ange `login-azurermaccount`.
3. Ange följande kommando för att skapa en resursgrupp för det virtuella nätverket:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Du kan distribuera mallen med hjälp av något av följande alternativ för parametrar:
    - **Standardvärdena för parametern**. Ange följande kommando:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Anpassade parametervärden**. Hämta och ändra mallen innan du distribuerar den. Du också distribuera mallen med hjälp av parametrar på kommandoraden eller distribuera mallen med en separat parameterfil. Om du vill hämta filer för mallen och parametrar, klickar du på den **Bläddra på GitHub** knappen på den [skapa ett virtuellt nätverk med två undernät](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) mall. I GitHub klickar du på den **azuredeploy.parameters.json** eller **azuredeploy.json** fil. Klicka på **Raw** knappen för att visa filen. Kopiera innehållet i filen i webbläsaren. Spara innehållet till en fil på din dator. Du kan ändra parametervärden i mallen, eller distribuera mallen med en separat parameterfil.  

    Mer information om hur du distribuerar mallar med hjälp av dessa metoder skriver `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Ta bort resurser

När du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Skriv i sökrutan portal **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroup**, och klicka sedan på **ta bort**.

### <a name="delete-cli"></a>Azure CLI

Ange följande kommando i en CLI-session:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Ange följande kommando i en PowerShell-session:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Mer information om alla virtuella nätverk och undernätsinställningar, se [hantera virtuella nätverk](virtual-network-manage-network.md#view-vnet) och [hantera virtuella undernät](virtual-network-manage-subnet.md#create-subnet). Har du olika alternativ för att använda virtuella nätverk och undernät i en produktionsmiljö för att uppfylla olika krav.
- Filtrera inkommande och utgående trafik genom att skapa och tillämpa [nätverkssäkerhetsgrupper](virtual-networks-nsg.md) till undernät.
- Vidarebefordra trafik mellan undernät via en virtuell nätverksenhet, genom att skapa [användardefinierade vägar](virtual-network-create-udr-arm-ps.md) och tillämpa vägar på varje undernät.
- Skapa en [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuell dator i ett befintligt virtuellt nätverk.
- Ansluta två virtuella nätverk genom att skapa en [virtuellt nätverk peering](virtual-network-peering-overview.md) mellan virtuella nätverk.
- Anslut det virtuella nätverket till ett lokalt nätverk med hjälp av en [VPN-Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) krets.
