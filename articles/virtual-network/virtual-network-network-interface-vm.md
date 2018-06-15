---
title: Lägg till nätverksgränssnitt till eller ta bort från virtuella datorer i Azure | Microsoft Docs
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
ms.openlocfilehash: 6193dcc6ba2e78c55ed6c6f769aea50fcfb3ac40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603467"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägg till nätverksgränssnitt till eller ta bort nätverksgränssnitt från virtuella datorer

Lär dig hur du lägger till en befintlig nätverksgränssnittet när du skapar en Azure virtuell dator (VM), eller att lägga till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i tillståndet Stoppad (frigjord). Ett nätverksgränssnitt kan en virtuell Azure-dator kan kommunicera med internet, Azure och lokala resurser. En virtuell dator kan ha en eller flera nätverksgränssnitt. 

Om du behöver för att lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, se [hantera IP-adresser i nätverket gränssnittet](virtual-network-network-interface-addresses.md). Om du behöver skapa, ändra, eller ta bort nätverksgränssnitt, se [hantera nätverksgränssnitt](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Innan du börjar

Utför följande uppgifter innan du slutför stegen i alla avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder portalen, öppna https://portal.azure.com, och logga in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här kursen kräver Azure PowerShell Modulversion 5.2.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.
- Om du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln, antingen köra kommandona i det [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här kursen kräver Azure CLI version 2.0.26 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägg till befintliga nätverksgränssnitt i en ny virtuell dator

När du skapar en virtuell dator via portalen portalen skapar ett nätverksgränssnitt med standardinställningar och kopplas till den virtuella datorn för dig. Du kan inte lägga till befintliga nätverksgränssnitt i en ny virtuell dator eller skapa en virtuell dator med flera nätverksgränssnitt med hjälp av Azure portal. Du kan göra både med hjälp av CLI eller PowerShell, men se till att bekanta dig med de [begränsningar](#constraints). Om du skapar en virtuell dator med flera nätverksgränssnitt, måste du också konfigurera operativsystemet för att använda dem korrekt när du har skapat den virtuella datorn. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

### <a name="commands"></a>Kommandon

Innan du skapar den virtuella datorn måste du skapa ett nätverksgränssnitt med hjälp av stegen i [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyget|Kommando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Lägg till ett nätverksgränssnitt i en befintlig virtuell dator

1. Logga in på Azure Portal.
2. I sökrutan överst på portalen, skriver du namnet på den virtuella datorn som du vill lägga till nätverksgränssnittet eller bläddra till den virtuella datorn genom att välja **alla tjänster**, och sedan **virtuella datorer**. När du har hittat den virtuella datorn, välja den. Den virtuella datorn måste ha stöd för antalet nätverksgränssnitt som du vill lägga till. Ta reda på hur många nätverksgränssnitt varje VM-storlek stöder, se [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Välj **översikt**under **inställningar**. Välj **stoppa**, och sedan vänta tills den **Status** ändras till den virtuella datorns **Stoppad (frigjord)**. 
4. Välj **nätverk**under **inställningar**.
5. Välj **bifoga nätverksgränssnittet**. Välj det som du vill koppla från listan över nätverksgränssnitt som inte är anslutna till en annan virtuell dator. 

    >[!NOTE]
    Nätverksgränssnittet som du väljer kan inte ha snabbare nätverk aktiverad, kan inte ha en IPv6-adress som tilldelats och måste finnas i samma virtuella nätverk som det innehåller det nätverksgränssnitt som är anslutna till den virtuella datorn. 

    Om du inte har en befintlig nätverksgränssnitt, måste du först skapa en. Om du vill göra det, Välj **skapa nätverksgränssnittet**. Mer information om hur du skapar ett nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface). Läs mer om ytterligare begränsningar när du lägger till nätverksgränssnitt för virtuella datorer i [begränsningar](#constraints).

6. Välj **OK**.
7. Välj **översikt**under **inställningar**, och sedan **starta** att starta den virtuella datorn.
8. Konfigurera VM-operativsystem för att använda flera nätverksgränssnitt korrekt. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverksgränssnitt.

|Verktyget|Kommando|
|---|---|
|CLI|[Lägg till AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa nätverksgränssnitt för en virtuell dator

Du kan visa nätverksgränssnitt som är anslutna till en virtuell dator mer information om konfigurationen för varje nätverksgränssnitt och IP-adresser som tilldelats varje nätverksgränssnitt. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller Network-deltagare för din prenumeration. Läs mer om hur du tilldelar roller till konton i [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. I rutan som innehåller texten **söka resurser** längst upp i Azure-portalen, Skriv **virtuella datorer**. När **virtuella datorer** visas i sökresultaten väljer den.
3. Välj namnet på den virtuella datorn som du vill visa nätverksgränssnitt.
4. I den **inställningar** avsnittet för den virtuella datorn du har valt, Välj **nätverk**. Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem, se [hantera nätverksgränssnitt](virtual-network-network-interface.md). Läs om hur du lägger till, ändra eller ta bort IP-adresser tilldelas ett nätverksgränssnitt i [hantera IP-adresser i nätverket gränssnittet](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Kommandon

|Verktyget|Kommando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

1. Logga in på Azure Portal.
2. I sökrutan överst på portalen, söka efter namnet på den virtuella datorn som du vill ta bort (frånkoppla) nätverksgränssnittet från eller bläddra efter den virtuella datorn genom att välja **alla tjänster**, och sedan **virtuella datorer**. När du har hittat den virtuella datorn, välja den.
3. Välj **översikt**under **inställningar**, och sedan **stoppa**. Vänta tills den **Status** ändras till den virtuella datorns **Stoppad (frigjord)**. 
4. Välj **nätverk**under **inställningar**.
5. Välj **koppla från nätverksgränssnittet**. Välj nätverksgränssnittet som du vill koppla bort från listan över nätverksgränssnitt som är anslutna till den virtuella datorn. 

    >[!NOTE]
    Om det bara ett nätverksgränssnitt, kan du koppla från den, eftersom en virtuell dator måste alltid ha minst ett nätverksgränssnitt som är kopplade till den.
6. Välj **OK**.

### <a name="commands"></a>Kommandon

|Verktyget|Kommando|
|---|---|
|CLI|[ta bort AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Ta bort AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverksgränssnitt som är kopplade till den.
- En virtuell dator kan endast ha många nätverksgränssnitt som kopplats till den som stöds för VM-storlek. Läs mer om hur många nätverkskort stöder varje VM-storlek i [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alla storlekar stöder minst två nätverksgränssnitt.
- Nätverksgränssnitt som du lägger till en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt finns [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface).
- Tidigare kunde endast nätverksgränssnitt läggas till virtuella datorer som har stöd för flera nätverksgränssnitt och har skapats med minst två nätverksgränssnitt. Du kunde inte lägga till ett nätverksgränssnitt till en virtuell dator som har skapats med ett nätverksgränssnitt, även om den Virtuella datorstorleken stöd för flera nätverksgränssnitt. Omvänt kan du kan bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som har skapats med minst två nätverk gränssnitt alltid måste ha minst två nätverksgränssnitt. Ingen av dessa villkor gäller längre. Nu kan du skapa en virtuell dator med valfritt antal nätverksgränssnitt (upp till antalet som stöds av VM-storlek).
- Som standard definieras första nätverksgränssnittet kopplad till en virtuell dator som den *primära* nätverksgränssnitt. Alla andra nätverksgränssnitt på den virtuella datorn är *sekundära* nätverksgränssnitt.
- Även om du kan styra vilka nätverksgränssnittet du skicka utgående trafik, som standard, all utgående trafik från den virtuella datorn skickas ut IP-adressen till den primära IP-adresskonfigurationen för det primära nätverksgränssnittet.
- Tidigare var alla virtuella datorer i samma tillgänglighetsuppsättning måste ha en enda eller flera nätverksgränssnitt. Virtuella datorer med valfritt antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till antalet som stöds av VM-storlek. Du kan bara lägga till en virtuell dator till en tillgänglighetsuppsättning när den skapas. Läs mer om tillgänglighetsuppsättningar i [hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Medan nätverksgränssnitt i samma virtuella dator kan vara ansluten till olika undernät i ett virtuellt nätverk, måste nätverksgränssnitt alla vara ansluten till samma virtuella nätverk.
- Du kan lägga till IP-adresser för alla IP-konfiguration för alla primära eller sekundära nätverksgränssnittet för en Azure-belastningsutjämnaren backend-adresspool. Tidigare kunde endast primära IP-adressen för det primära nätverksgränssnittet läggas till en backend-adresspool. Mer information om IP-adresser och konfigurationer finns [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).
- Om du tar bort en virtuell dator tar inte bort nätverksgränssnitt som är kopplade till den. När du tar bort en virtuell dator kopplas nätverksgränssnitten bort från den virtuella datorn. Du kan lägga till nätverksgränssnitt i olika virtuella datorer eller ta bort dem.
- Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, måste du lägga till (bifoga) den till en virtuell dator när du skapar den virtuella datorn. Du kan inte lägga till ett nätverksgränssnitt med en tilldelad IPv6-adress till en virtuell dator när du har skapat den virtuella datorn. Om du lägger till ett nätverksgränssnitt med en tilldelad privata IPv6-adress när du skapar en virtuell dator kan du bara lägga till nätverksgränssnittet till den virtuella datorn, oavsett hur många nätverkskort som stöder VM-storlek. Se [hantera IP-adresser i nätverket gränssnittet](virtual-network-network-interface-addresses.md) mer information om hur du tilldelar IP-adresser till nätverksgränssnitt.
- Som IPv6, kan du koppla ett nätverksgränssnitt med snabbare nätverksfunktioner som är aktiverade för att en virtuell dator när du har skapat. Dessutom för att kunna utnyttja snabbare nätverksfunktioner, måste du också slutföra stegen i VM-operativsystemet. Lär dig mer om snabbare nätverk och andra begränsningar när du använder den för [Windows](create-vm-accelerated-networking-powershell.md) eller [Linux](create-vm-accelerated-networking-cli.md) virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar för att skapa en virtuell dator med flera nätverksgränssnitt eller IP-adresser:

### <a name="commands"></a>Kommandon

|Aktivitet|Verktyget|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en enda NIC VM med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en enda NIC VM med en privat IPv6-adress (bakom en belastningsutjämnare i Azure)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


