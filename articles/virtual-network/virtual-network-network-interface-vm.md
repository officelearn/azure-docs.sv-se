---
title: Lägga till nätverksgränssnitt till eller ta bort från Azure-datorer | Microsoft Docs
description: Lär dig mer om att lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: cf856a680601edd950cd0a5fddbc1241782478e2
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648905"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till en befintlig nätverksgränssnitt när du skapar en Azure-dator (VM), eller att lägga till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i tillståndet Stoppad (frigjord). Ett nätverksgränssnitt kan en Azure virtuell dator ska kunna kommunicera med internet, Azure och lokala resurser. En virtuell dator kan ha en eller flera nätverksgränssnitt. 

Om du behöver för att lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, se [hantera IP-adresser i nätverksgränssnittet](virtual-network-network-interface-addresses.md). Om du vill skapa, ändra, eller ta bort nätverksgränssnitt kan du läsa [hantera nätverksgränssnitt](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.26 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägga till befintliga nätverksgränssnitt till en ny virtuell dator

När du skapar en virtuell dator via portalen portalen skapar ett nätverksgränssnitt med standardinställningar och kopplar den till den virtuella datorn för dig. Du kan inte lägga till befintliga nätverksgränssnitt till en ny virtuell dator eller skapa en virtuell dator med flera nätverksgränssnitt med hjälp av Azure portal. Du kan göra både och, hjälp av CLI eller PowerShell, men se till att bekanta dig med den [begränsningar](#constraints). Om du skapar en virtuell dator med flera nätverksgränssnitt måste du också konfigurera operativsystemet för att använda dem korrekt när du har skapat den virtuella datorn. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

Innan du skapar den virtuella datorn måste du skapa ett nätverksgränssnitt med hjälp av stegen i [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyget|Kommando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Lägga till ett nätverksgränssnitt i en befintlig virtuell dator

1. Logga in på Azure Portal.
2. I sökrutan högst upp i portalen skriver du namnet på den virtuella datorn som du vill lägga till nätverksgränssnittet eller bläddra till den virtuella datorn genom att välja **alla tjänster**, och sedan **virtuella datorer**. När du har hittat den virtuella datorn, markerar du den. Den virtuella datorn måste ha stöd för antalet nätverksgränssnitt som du vill lägga till. För att ta reda på hur många nätverksgränssnitt varje VM-storleken stöder, finns i [storlekar för Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Välj **översikt**under **inställningar**. Välj **stoppa**, och sedan vänta tills den **Status** för den virtuella datorn ändras till **Stoppad (frigjord)**.
4. Välj **nätverk**under **inställningar**.
5. Välj **bifoga nätverksgränssnittet**. Välj det som du vill koppla från listan över nätverksgränssnitt som inte är anslutna till en annan virtuell dator.

   >[!NOTE]
   >Nätverksgränssnittet som du väljer inte har accelererat nätverk aktiverat kan inte ha en IPv6-adress som tilldelats och måste finnas i samma virtuella nätverk som det som innehåller nätverksgränssnitt som är anslutna till den virtuella datorn.

   Om du inte har en befintlig nätverksgränssnitt, måste du först skapa en. Om du vill göra det, Välj **skapa nätverksgränssnittet**. Mer information om hur du skapar ett nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface). Läs mer om ytterligare begränsningar när du lägger till nätverksgränssnitt för virtuella datorer i [begränsningar](#constraints).

6. Välj **OK**.
7. Välj **översikt**under **inställningar**, och sedan **starta** att starta den virtuella datorn.
8. Konfigurera VM-operativsystem om du vill använda flera nätverksgränssnitt korrekt. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon
|Verktyget|Kommando|
|---|---|
|CLI|[Lägg till AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Lägg till AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa flera nätverksgränssnitt för en virtuell dator

Du kan visa de nätverksgränssnitt som är anslutna till en virtuell dator att lära dig om konfigurationen för varje nätverksgränssnitt och IP-adresser som tilldelats varje nätverksgränssnitt. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller Nätverksdeltagare för din prenumeration. Läs mer om hur du tilldelar roller för konton i [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. I rutan som innehåller texten **Sök efter resurser** högst upp på Azure-portalen, Skriv **virtuella datorer**. När **virtuella datorer** visas i sökresultatet väljer du det.
3. Välj namnet på den virtuella datorn som du vill visa nätverksgränssnitt.
4. I den **inställningar** avsnittet för den virtuella datorn du valde Välj **nätverk**. Mer information om inställningar för nätverksgränssnittet och hur du ändrar dem, se [hantera nätverksgränssnitt](virtual-network-network-interface.md). Läs om hur du lägger till, ändra eller ta bort IP-adresser tilldelade till ett nätverksgränssnitt i [hantera IP-adresser i nätverksgränssnittet](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Kommandon

|Verktyget|Kommando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

1. Logga in på Azure Portal.
2. I sökrutan högst upp i portalen, söker du efter namnet på den virtuella datorn som du vill ta bort (koppla från) nätverksgränssnitt från eller bläddra efter den virtuella datorn genom att välja **alla tjänster**, och sedan **virtuella datorer**. När du har hittat den virtuella datorn, markerar du den.
3. Välj **översikt**under **inställningar**, och sedan **stoppa**. Vänta tills den **Status** för den virtuella datorn ändras till **Stoppad (frigjord)**.
4. Välj **nätverk**under **inställningar**.
5. Välj **koppla från nätverksgränssnittet**. Välj nätverksgränssnittet som du vill koppla bort från listan över nätverksgränssnitt som är anslutna till den virtuella datorn.

   >[!NOTE]
   >Om det bara ett nätverksgränssnitt, kan du koppla bort det, eftersom en virtuell dator måste alltid ha minst ett nätverksgränssnitt som är kopplat till den.
6. Välj **OK**.

### <a name="commands"></a>Kommandon

|Verktyget|Kommando|
|---|---|
|CLI|[ta bort AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Ta bort AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverksgränssnitt som är kopplat till den.
- En virtuell dator kan bara ha så många nätverksgränssnitt kopplade till den som stöd för VM-storlek. Läs mer om hur många nätverksgränssnitt varje VM-storleken stöder i [storlekar för Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alla storlekar som stöder minst två nätverksgränssnitt.
- Nätverksgränssnitt som du lägger till i en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).
- Tidigare kunde du bara läggas nätverksgränssnitt för virtuella datorer som stöds flera nätverksgränssnitt och skapades med minst två nätverksgränssnitt. Du kan inte lägga till ett nätverksgränssnitt till en virtuell dator som har skapats med ett nätverksgränssnitt, även om VM-storleken stöds flera nätverksgränssnitt. Omvänt kan du kan bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som skapats med minst två nätverk gränssnitt alltid behövde ha minst två nätverksgränssnitt. Inget av dessa begränsningar gäller längre. Du kan nu skapa en virtuell dator med valfritt antal nätverksgränssnitt (upp till det antal som stöds av virtuella datorstorlek).
- Som standard definieras det första nätverksgränssnittet som kopplas till en virtuell dator som den *primära* nätverksgränssnitt. Alla andra nätverksgränssnitt på den virtuella datorn är *sekundära* nätverksgränssnitt.
- Även om du kan styra vilka nätverksgränssnitt skickas av utgående trafik till, som standard, all utgående trafik från den virtuella datorn skickas ut IP-adress som tilldelats den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.
- Tidigare var alla virtuella datorer i samma tillgänglighetsuppsättning måste ha en enda eller flera nätverksgränssnitt. Virtuella datorer med valfritt antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till det antal som stöds av VM-storleken. Du kan bara lägga till en virtuell dator i en tillgänglighetsuppsättning när den skapas. Läs mer om tillgänglighetsuppsättningar i [hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Medan nätverksgränssnitt i samma virtuella dator kan anslutas till olika undernät inom ett virtuellt nätverk, måste nätverksgränssnitt alla vara ansluten till samma virtuella nätverk.
- Du kan lägga till IP-adresser för alla IP-konfiguration för alla primära eller sekundära nätverksgränssnitt till en Azure Load Balancer-backend-pool. Tidigare kunde bara den primära IP-adressen för det primära nätverksgränssnittet läggas till en backend-poolen. Mer information om IP-adresser och konfigurationer finns [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).
- Tar bort en virtuell dator tas inte bort nätverksgränssnitt som är kopplade till den. När du tar bort en virtuell dator nätverksgränssnitt har kopplats bort från den virtuella datorn. Du kan lägga till nätverksgränssnitt i olika virtuella datorer eller ta bort dem.
- Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, måste du lägga till (Anslut) den till en virtuell dator när du skapar den virtuella datorn. Du kan inte lägga till ett nätverksgränssnitt med en tilldelad IPv6-adress till en virtuell dator när du har skapat den virtuella datorn. Om du lägger till ett nätverksgränssnitt med en tilldelad privata IPv6-adress när du skapar en virtuell dator kan du bara lägga till nätverksgränssnittet till den virtuella datorn, oavsett hur många nätverksgränssnitt som har stöd för virtuella datorstorlek. Se [hantera IP-adresser i nätverksgränssnittet](virtual-network-network-interface-addresses.md) mer information om hur du tilldelar IP-adresser till nätverksgränssnitt.
- Precis som med IPv6, kan inte du koppla ett nätverksgränssnitt med accelererat nätverk aktiverat till en virtuell dator när du har skapat. Dessutom för att dra nytta av accelererat nätverk, måste du också slutföra stegen i VM-operativsystem. Lär dig mer om accelererat nätverk och andra begränsningar när du använder den för [Windows](create-vm-accelerated-networking-powershell.md) eller [Linux](create-vm-accelerated-networking-cli.md) virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Om du vill skapa en virtuell dator med flera nätverksgränssnitt eller IP-adresser finns i följande artiklar:

|Aktivitet|Verktyget|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC-VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC-VM med en privat IPv6-adress (bakom en belastningsutjämnare för Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|