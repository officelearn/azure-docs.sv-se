---
title: "Lägg till nätverksgränssnitt till eller ta bort från virtuella datorer i Azure | Microsoft Docs"
description: "Lär dig mer om att lägga till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: abe6abb942d206330e809f3aef388b846d7d7c7f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägg till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till en befintlig nätverksgränssnittet när du skapar en virtuell dator eller Lägg till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i tillståndet Stoppad (frigjord). Ett nätverksgränssnitt kan ett Azure Virtual Machine (VM-namn) kan kommunicera med Internet, Azure och lokala resurser. En virtuell dator kan ha en eller flera nätverksgränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, läsa den [hantera IP-adresser i nätverket gränssnittet](virtual-network-network-interface-addresses.md) artikel. Om du behöver för att skapa, ändra eller ta bort nätverksgränssnitt, kan du läsa den [hantera nätverksgränssnitt](virtual-network-network-interface.md) artikel.

## <a name="before"></a>Innan du börjar

Utför följande uppgifter innan du slutför alla steg i alla avsnitt i den här artikeln:

- Logga in på Azure [portal](https://portal.azure.com), Azure-kommandoradsgränssnittet (CLI) eller Azure PowerShell med ett Azure-konto. Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure PowerShell-kommandon som är installerad. Om du vill få hjälp med PowerShell-kommandon med exempel på, skriver `get-help <command> -full`. Du kan använda Azure Cloud-gränssnittet i stället för att installera Azure PowerShell. Azure Cloud-gränssnittet är en kostnadsfri PowerShell som du kan köra direkt i Azure-portalen. Den har Azure PowerShell förinstallerat och konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell **> _** längst upp i den [portal](https://portal.azure.com) och välja PowerShell i det övre vänstra hörnet när fönstret shell visas.
- Om Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI installerad. Om du vill få hjälp med CLI-kommandon, Skriv `az <command> --help`. Du kan använda Azure Cloud-gränssnittet i stället för att installera CLI och dess krav. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell **> _** längst upp i den [portal](https://portal.azure.com) och välj Bash i det övre vänstra hörnet när fönstret shell visas.

## <a name="vm-create"></a>Lägg till befintliga nätverksgränssnitt i en ny virtuell dator

När du skapar en virtuell dator via portalen portalen skapar ett nätverksgränssnitt med standardinställningar och kopplas till den virtuella datorn för dig. Du kan inte lägga till befintliga nätverksgränssnitt i en ny virtuell dator eller skapa en virtuell dator med flera nätverksgränssnitt med Azure-portalen. Du kan göra båda med CLI eller PowerShell. Innan du använder PowerShell eller CLI för att skapa en virtuell dator med en befintlig nätverksgränssnittet men kan du bekanta dig med de [begränsningar](#constraints). Om du skapar en virtuell dator med flera nätverksgränssnitt, måste du också konfigurera operativsystemet för att använda dem korrekt när den virtuella datorn har skapats. Mer information finns i Konfigurera [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

**Kommandon** innan du skapar den virtuella datorn, skapa ett nätverksgränssnitt med hjälp av stegen i [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyget|Kommando|
|---|---|
|CLI|[Skapa AZ vm](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Lägg till ett nätverksgränssnitt i en befintlig virtuell dator

1. Logga in på Azure-portalen.
2. I sökrutan överst på portalen, söka efter namnet på den virtuella datorn som du vill lägga till nätverksgränssnitt för eller bläddra till den virtuella datorn genom att klicka på **alla tjänster**, sedan **virtuella datorer**. När du har hittat den virtuella datorn, klickar du på den. Den virtuella datorn som du vill lägga till ett nätverksgränssnitt och måste ha stöd för antalet nätverksgränssnitt som du vill lägga till. Att lära dig hur många nätverksgränssnitt varje VM storlek har stöd för, läsa den [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar.  
3. Klicka på **översikt**under **inställningar**. Klicka på **stoppa**, och vänta tills den **Status** ändras till den virtuella datorns *Stoppad (frigjord)*. 
4. Klicka på **nätverk**under **inställningar**.
5. Klicka på **bifoga nätverksgränssnittet**. Klicka på nätverksgränssnittet som du vill koppla från listan över befintliga nätverksgränssnitt som inte är anslutna till en annan virtuell dator. Nätverksgränssnittet som du väljer kan inte ha snabbare nätverk som är aktiverad, kan inte ha en IPv6-adress som tilldelats och måste finnas i samma virtuella nätverk som det virtuella nätverket som nätverksgränssnittet är anslutna till den virtuella datorn. Om du inte har en befintlig nätverksgränssnitt, måste du först skapa en. Klicka för att skapa ett nätverksgränssnitt **skapa nätverksgränssnittet**. Mer information om hur du skapar ett nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface). Läs mer om ytterligare begränsningar när du lägger till nätverksgränssnitt för virtuella datorer i [begränsningar](#constraints).
6. Klicka på **OK**.
7. Klicka på **översikt**under **inställningar**. Klicka på **starta** att starta den virtuella datorn.
8. Konfigurera VM-operativsystem för att använda flera nätverksgränssnitt korrekt. Mer information finns i Konfigurera [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

|Verktyget|Kommando|
|---|---|
|CLI|[Lägg till AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>Visa nätverksgränssnitt för en virtuell dator

Du kan visa nätverksgränssnitt som är anslutna till en virtuell dator mer information om konfigurationen för varje nätverksgränssnitt och IP-adresser som tilldelats varje nätverksgränssnitt. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller Network-deltagare för din prenumeration. Läs mer om att tilldela roller till konton i [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *virtuella datorer*. När **virtuella datorer** visas i sökresultaten klickar du på den.
3. Klicka på namnet på den virtuella datorn som du vill visa nätverksgränssnitt för.
4. I den **inställningar** för den virtuella datorn du har valt, klickar du på **nätverk**. Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem, se [hantera nätverksgränssnitt](virtual-network-network-interface.md). Mer information om hur du lägger till, ändrar eller tar bort IP-adresser tilldelas ett nätverksgränssnitt finns [hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ vm visa](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

1. Logga in på Azure-portalen.
2. I sökrutan överst på portalen, söka efter namnet på den virtuella datorn som du vill ta bort (frånkoppla) nätverksgränssnittet från eller bläddra efter den virtuella datorn genom att klicka på **alla tjänster**, sedan **virtuella datorer**. När du har hittat den virtuella datorn, klickar du på den.
3. Klicka på **översikt**under **inställningar**. Klicka på **stoppa**, och vänta tills den **Status** ändras till den virtuella datorns *Stoppad (frigjord)*. 
4. Klicka på **nätverk**under **inställningar**.
5. Klicka på **koppla från nätverksgränssnittet**. Klicka på nätverksgränssnittet som du vill koppla bort från listan över nätverksgränssnitt som är anslutna till den virtuella datorn. Om det bara ett nätverksgränssnitt, kan du koppla från den, eftersom en virtuell dator måste alltid ha minst ett nätverksgränssnitt som är kopplade till den.
6. Klicka på **OK**.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[ta bort AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Ta bort AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar för att skapa en virtuell dator med flera nätverksgränssnitt eller IP-adresser:

**Kommandon**

|Aktivitet|Verktyget|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC VM med en privat IPv6-adress (bakom en belastningsutjämnare i Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverksgränssnitt som är kopplade till den.
- En virtuell dator kan endast ha många nätverksgränssnitt som kopplats till den som stöds för VM-storlek. Läs mer om hur många nätverkskort stöder varje VM-storlek i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar. Alla storlekar stöder minst två nätverksgränssnitt.
- Nätverksgränssnitt som du lägger till en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).
- Tidigare kunde endast nätverksgränssnitt läggas till virtuella datorer som har stöd för flera nätverksgränssnitt och har skapats med minst två nätverksgränssnitt. Du kunde inte lägga till ett nätverksgränssnitt till en virtuell dator som har skapats med ett nätverksgränssnitt, även om den Virtuella datorstorleken stöd för flera nätverksgränssnitt. Omvänt kan du kan bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som har skapats med minst två nätverk gränssnitt alltid måste ha minst två nätverksgränssnitt. Ingen av dessa villkor gäller längre. Nu kan du skapa en virtuell dator med valfritt antal nätverksgränssnitt (upp till antalet som stöds av VM-storlek).
- Som standard definieras första nätverksgränssnittet kopplad till en virtuell dator som den *primära* nätverksgränssnitt. Alla andra nätverksgränssnitt på den virtuella datorn är *sekundära* nätverksgränssnitt.
- Även om du kan styra vilka nätverksgränssnittet du skicka utgående trafik, som standard, all utgående trafik från den virtuella datorn skickas ut IP-adressen till den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.
- Tidigare var alla virtuella datorer i samma tillgänglighetsuppsättning måste ha en enda eller flera nätverksgränssnitt. Virtuella datorer med valfritt antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till antalet som stöds av VM-storlek. Du kan bara lägga till en virtuell dator till en tillgänglighetsuppsättning när den skapas om. Mer information om tillgänglighetsuppsättningar i [hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) artikel.
- Medan nätverksgränssnitt i samma virtuella dator kan vara ansluten till olika undernät inom ett VNet, måste nätverksgränssnitt alla vara ansluten till samma virtuella nätverk.
- Du kan lägga till IP-adresser för alla IP-konfiguration för alla primära eller sekundära nätverksgränssnittet för en Azure-belastningsutjämnaren backend-adresspool. Tidigare kunde endast primära IP-adressen för det primära nätverksgränssnittet läggas till en backend-adresspool. Mer information om IP-adresser och konfigurationer av [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md) artikel.
- Om du tar bort en virtuell dator tar inte bort nätverksgränssnitt som är kopplade till den. När en virtuell dator tas bort oberoende nätverksgränssnitt från den virtuella datorn. Du kan lägga till nätverksgränssnitt i olika virtuella datorer eller ta bort dem.
- Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, måste du lägga till (bifoga) den till en virtuell dator när du skapar den virtuella datorn. Du kan inte lägga till ett nätverksgränssnitt med en tilldelad IPv6-adress till en virtuell dator när den virtuella datorn har skapats. Om du lägger till ett nätverksgränssnitt med en tilldelad privata IPv6-adress när du skapar en virtuell dator kan du bara lägga till nätverksgränssnittet till den virtuella datorn, oavsett hur många nätverkskort som stöder VM-storlek. Se [Network interface IP-adresser](virtual-network-network-interface-addresses.md) lära dig mer om att tilldela IP-adresser till nätverksgränssnitt.
- Liknande IPv6, du kan inte lägga ett nätverksgränssnitt med snabbare nätverksfunktioner som är aktiverade för att en virtuell dator när den virtuella datorn har skapats. Dessutom för att kunna utnyttja snabbare nätverksfunktioner, måste du också slutföra stegen i VM-operativsystemet. Läs mer om snabbare nätverksfunktioner och andra begränsningar när du använder den i Accelerated nätverksfunktioner för [Windows](create-vm-accelerated-networking-powershell.md) eller [Linux](create-vm-accelerated-networking-cli.md) virtuella datorer.
