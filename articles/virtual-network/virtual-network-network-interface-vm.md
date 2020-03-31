---
title: Lägga till nätverksgränssnitt i eller ta bort från virtuella Azure-datorer
description: Lär dig hur du lägger till nätverksgränssnitt i eller tar bort nätverksgränssnitt från virtuella datorer.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060323"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till ett befintligt nätverksgränssnitt när du skapar en virtuell Azure-dator (VM). Lär dig också att lägga till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i tillståndet stoppad (deallocated). Ett nätverksgränssnitt gör det möjligt för en Virtuell Azure-dator att kommunicera med internet,Azure och lokala resurser. En virtuell dator har ett eller flera nätverksgränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt läser du [Hantera IP-adresser för nätverksgränssnittet](virtual-network-network-interface-addresses.md). Hur du skapar, ändrar eller tar bort nätverksgränssnitt finns i [Hantera nätverksgränssnitt](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har ett, konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför en av dessa uppgifter innan du påbörjar resten av den här artikeln:

- **Portalanvändare**: Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare:** Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Leta reda på listrutan **Välj miljö** i webbläsaren Azure Cloud Shell och välj sedan **PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör `Connect-AzAccount` för att skapa en anslutning med Azure.

- **CLI-användare (Azure Command-line Interface)**: Kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.26 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör `az login` för att skapa en anslutning med Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägga till befintliga nätverksgränssnitt i en ny virtuell dator

När du skapar en virtuell dator via portalen skapar portalen ett nätverksgränssnitt med standardinställningar och kopplar nätverksgränssnittet till den virtuella datorn åt dig. Du kan inte använda portalen för att lägga till befintliga nätverksgränssnitt till en ny virtuell dator eller för att skapa en virtuell dator med flera nätverksgränssnitt. Du kan göra båda med hjälp av CLI eller PowerShell. Var noga med att bekanta dig med [begränsningarna](#constraints). Om du skapar en virtuell dator med flera nätverksgränssnitt måste du också konfigurera operativsystemet så att de används korrekt när du har skapat den virtuella datorn. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

Innan du skapar den virtuella datorn [skapar du ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyg|Kommando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Lägga till ett nätverksgränssnitt i en befintlig virtuell dator

Så här lägger du till ett nätverksgränssnitt på den virtuella datorn:

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer**.

2. Välj namnet på den virtuella datorn. Den virtuella datorn måste stödja antalet nätverksgränssnitt som du vill lägga till. Information om hur många nätverksgränssnitt varje vm-storlek stöder finns i storlekarna i Virtuella Azure för [virtuella Linux-datorer](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. I kommandofältet Virtuell dator väljer du **Stoppa**och sedan **OK** i bekräftelsedialogrutan. Vänta sedan tills **status** för den virtuella datorn ändras till **Stoppad (deallocated)**.

4. Välj > **Nätverksansluten bifoga nätverksgränssnitt**på menyraden För virtuell dator . **Networking** Välj sedan det nätverksgränssnitt som du vill koppla i i **Bifoga befintligt nätverksgränssnitt**och välj **OK**.

    >[!NOTE]
    >Nätverksgränssnittet du väljer kan inte ha aktiverat ett accelererat nätverk, kan inte ha en IPv6-adress tilldelad och måste finnas i samma virtuella nätverk med det nätverksgränssnitt som för närvarande är kopplat till den virtuella datorn.

    Om du inte har ett befintligt nätverksgränssnitt måste du först skapa ett. Om du vill göra det väljer du **Skapa nätverksgränssnitt**. Mer information om hur du skapar ett nätverksgränssnitt finns i [Skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface). Mer information om ytterligare begränsningar när du lägger till nätverksgränssnitt på virtuella datorer finns i [Begränsningar](#constraints).

5. På menyraden för virtuell dator väljer du > **Översiktsstart** för att starta om den virtuella datorn. **Overview**

Nu kan du konfigurera operativsystemet VM så att flera nätverksgränssnitt används korrekt. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (referens); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Tillägg-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa flera nätverksgränssnitt för en virtuell dator

Du kan visa de nätverksgränssnitt som för närvarande är kopplade till en virtuell dator för att lära dig om varje nätverksgränssnitts konfiguration och ip-adresserna som tilldelats varje nätverksgränssnitt. 

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer**.

    >[!NOTE]
    >Logga in med ett konto som har tilldelats rollen Ägare, Deltagare eller Nätverksbidragsgivare för din prenumeration. Mer information om hur du tilldelar roller till konton finns [i Inbyggda roller för Azure-rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverksgränssnitt för.

3. Välj **Nätverk**i menyraden för virtuell dator.

Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem finns i [Hantera nätverksgränssnitt](virtual-network-network-interface.md). Mer information om hur du lägger till, ändrar eller tar bort IP-adresser som tilldelats ett nätverksgränssnitt finns i [Hantera IP-adresser för nätverksgränssnittet](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic lista](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Få-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta en befintlig virtuell dator. Sök efter och välj **Virtuella datorer**.

2. Välj namnet på den virtuella dator som du vill visa anslutna nätverksgränssnitt för.

3. Välj **Stopp**i verktygsfältet Virtuell dator .

4. Vänta tills **status** för den virtuella datorn ändras till **Stoppad (deallocated)**.

5. Välj**Nätverksanslut** **nätverksgränssnitt** > på menyraden För virtuell dator .

6. I dialogrutan **Koppla från nätverksgränssnittet** väljer du det nätverksgränssnitt som du vill koppla från. Välj sedan **OK**.

    >[!NOTE]
    >Om bara ett nätverksgränssnitt visas kan du inte koppla från det, eftersom en virtuell dator alltid måste ha minst ett nätverksgränssnitt kopplat till det.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (referens); [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Ta bort-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens); [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverksgränssnitt kopplat till sig.

- En virtuell dator kan bara ha så många nätverksgränssnitt som är anslutna till den som den virtuella datorns storlek stöder. Mer information om hur många nätverksgränssnitt varje vm-storlek stöder finns i storlekarna i Virtuella Azure för [virtuella Linux-datorer](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [virtuella Windows-datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alla storlekar stöder minst två nätverksgränssnitt.

- De nätverksgränssnitt som du lägger till i en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt finns i [Skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

- Tidigare kan du lägga till nätverksgränssnitt endast till virtuella datorer som stödde flera nätverksgränssnitt och har skapats med minst två nätverksgränssnitt. Du kunde inte lägga till ett nätverksgränssnitt till en virtuell dator som skapades med ett nätverksgränssnitt, även om den virtuella datorns storlek stödde mer än ett nätverksgränssnitt. Omvänt kan du bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som skapats med minst två nätverksgränssnitt alltid måste ha minst två nätverksgränssnitt. Dessa begränsningar gäller inte längre. Du kan nu skapa en virtuell dator med valfritt antal nätverksgränssnitt (upp till det antal som stöds av den virtuella datorns storlek).

- Som standard är det första nätverksgränssnittet som är kopplat till en virtuell dator det *primära* nätverksgränssnittet. Alla andra nätverksgränssnitt i den virtuella datorn är *sekundära* nätverksgränssnitt.

- Du kan styra vilket nätverksgränssnitt du skickar utgående trafik till. En virtuell dator skickar dock som standard all utgående trafik till IP-adressen som har tilldelats den primära IP-konfigurationen för det primära nätverksgränssnittet.

- Tidigare var alla virtuella datorer inom samma tillgänglighetsuppsättning skyldiga att ha ett eller flera nätverksgränssnitt. Virtuella datorer med valfritt antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till det antal som stöds av den virtuella datorns storlek. Du kan bara lägga till en virtuell dator i en tillgänglighetsuppsättning när den skapas. Mer information om tillgänglighetsuppsättningar finns i [Hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Du kan ansluta nätverksgränssnitt i samma virtuella dator till olika undernät i ett virtuellt nätverk. Nätverksgränssnitten måste dock alla vara anslutna till samma virtuella nätverk.

- Du kan lägga till valfri IP-adress för valfri IP-konfiguration av ett primärt eller sekundärt nätverksgränssnitt i en Azure Load Balancer-backend-pool. Tidigare kunde endast den primära IP-adressen för det primära nätverksgränssnittet läggas till i en backend-pool. Mer information om IP-adresser och konfigurationer finns i [Lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).

- Om du tar bort en virtuell dator tas inte de nätverksgränssnitt som är kopplade till den bort. När du tar bort en virtuell dator tas nätverksgränssnitten bort från den virtuella datorn. Du kan lägga till dessa nätverksgränssnitt i olika virtuella datorer eller ta bort dem.

- Precis som med IPv6 kan du inte ansluta ett nätverksgränssnitt med accelererade nätverk som är aktiverat till en virtuell dator när du har skapat den. Om du vill dra nytta av accelererade nätverk måste du också slutföra stegen i operativsystemet VM. Läs mer om accelererade nätverk och andra begränsningar när du använder det för [virtuella Datorer](create-vm-accelerated-networking-powershell.md) i Windows eller [Linux.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Nästa steg

Så här skapar du en virtuell dator med flera nätverksgränssnitt eller IP-adresser:

|Aktivitet|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en virtuell nätverkskort med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en virtuell dator med ett NIC-nätverkskort med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
