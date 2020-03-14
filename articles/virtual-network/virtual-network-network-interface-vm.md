---
title: Lägg till eller ta bort nätverks gränssnitt från virtuella Azure-datorer
description: Lär dig hur du lägger till nätverks gränssnitt i eller tar bort nätverks gränssnitt från virtuella datorer.
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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279564"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Lägga till nätverks gränssnitt i eller ta bort nätverks gränssnitt från virtuella datorer

Lär dig hur du lägger till ett befintligt nätverks gränssnitt när du skapar en virtuell Azure-dator (VM), eller om du vill lägga till eller ta bort nätverks gränssnitt från en befintlig virtuell dator i läget Stoppad (Frigjord). Ett nätverks gränssnitt gör det möjligt för en virtuell Azure-dator att kommunicera med Internet, Azure och lokala resurser. En virtuell dator kan ha ett eller flera nätverks gränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverks gränssnitt, se [Hantera IP-adresser för nätverks gränssnitt](virtual-network-network-interface-addresses.md). Om du behöver skapa, ändra eller ta bort nätverks gränssnitt, se [Hantera nätverks gränssnitt](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande åtgärder innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett [kostnads fritt utvärderings konto](https://azure.microsoft.com/free).
- Om du använder portalen öppnar du https://portal.azure.comoch loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder kommando rads kommandon i Azure för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. I den här självstudien krävs Azure CLI version 2.0.26 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Lägga till befintliga nätverks gränssnitt till en ny virtuell dator

När du skapar en virtuell dator via portalen skapar portalen ett nätverks gränssnitt med standardinställningar och kopplar det till den virtuella datorn åt dig. Du kan inte lägga till befintliga nätverks gränssnitt till en ny virtuell dator eller skapa en virtuell dator med flera nätverks gränssnitt med hjälp av Azure Portal. Du kan göra båda med CLI eller PowerShell, men se till att bekanta dig med [begränsningarna](#constraints). Om du skapar en virtuell dator med flera nätverks gränssnitt, måste du också konfigurera operativ systemet så att det används korrekt när du har skapat den virtuella datorn. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverks gränssnitt.

### <a name="commands"></a>Kommandon

Innan du skapar den virtuella datorn skapar du ett nätverks gränssnitt genom att följa stegen i [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface).

|Verktyg|Kommando|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Lägga till ett nätverks gränssnitt till en befintlig virtuell dator

1. Logga in på Azure Portal.
2. I rutan Sök högst upp i portalen skriver du namnet på den virtuella dator som du vill lägga till nätverks gränssnittet i, eller bläddrar till den virtuella datorn genom att välja **alla tjänster**och sedan **virtuella datorer**. När du har hittat den virtuella datorn väljer du den. Den virtuella datorn måste ha stöd för det antal nätverks gränssnitt som du vill lägga till. För att ta reda på hur många nätverks gränssnitt varje VM-storlek stöder, se [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Välj **Översikt**under **Inställningar**. Välj **stoppa**och vänta tills **statusen** för den virtuella datorn ändras till **stoppad (Frigjord)** .
4. Välj **nätverk**under **Inställningar**.
5. Välj **bifoga nätverks gränssnitt**. I listan över nätverks gränssnitt som inte är anslutna till en annan virtuell dator väljer du den som du vill koppla.

   >[!NOTE]
   >Det nätverks gränssnitt du väljer kan inte ha accelererat nätverk aktiverat, kan inte ha en tilldelad IPv6-adress och måste finnas i samma virtuella nätverk som det som innehåller det nätverks gränssnitt som är kopplat till den virtuella datorn.

   Om du inte har ett befintligt nätverks gränssnitt måste du först skapa ett. Det gör du genom att välja **skapa nätverks gränssnitt**. Mer information om hur du skapar ett nätverks gränssnitt finns i [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface). Mer information om ytterligare begränsningar när du lägger till nätverks gränssnitt till virtuella datorer finns i [begränsningar](#constraints).

6. Välj **OK**.
7. Välj **Översikt**, under **Inställningar**och **Starta** sedan för att starta den virtuella datorn.
8. Konfigurera operativ systemet för den virtuella datorn så att det använder flera nätverks gränssnitt på rätt sätt. Lär dig hur du konfigurerar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) för flera nätverks gränssnitt.

### <a name="commands"></a>Kommandon
|Verktyg|Kommando|
|---|---|
|CLI|[AZ VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Visa nätverks gränssnitt för en virtuell dator

Du kan visa de nätverks gränssnitt som är kopplade till en virtuell dator för att lära dig om varje nätverks gränssnitts konfiguration och de IP-adresser som tilldelats varje nätverks gränssnitt. 

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller nätverks deltagare för din prenumeration. Mer information om hur du tilldelar roller till konton finns i [inbyggda roller för rollbaserad åtkomst kontroll i Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Skriv **virtuella datorer**i rutan som innehåller text **Sök resurserna** överst i Azure Portal. När **virtuella datorer** visas i Sök resultaten väljer du den.
3. Välj namnet på den virtuella dator som du vill visa nätverks gränssnitt för.
4. I avsnittet **Inställningar** för den virtuella datorn som du har valt väljer du **nätverk**. Information om inställningar för nätverks gränssnitt och hur du ändrar dem finns i [Hantera nätverks gränssnitt](virtual-network-network-interface.md). Information om hur du lägger till, ändrar eller tar bort IP-adresser som tilldelats ett nätverks gränssnitt finns i [Hantera nätverks gränssnittets IP-adresser](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Ta bort ett nätverks gränssnitt från en virtuell dator

1. Logga in på Azure Portal.
2. I rutan Sök högst upp i portalen söker du efter namnet på den virtuella dator som du vill ta bort (frånkoppla) nätverks gränssnittet från eller bläddrar efter den virtuella datorn genom att välja **alla tjänster**och sedan **virtuella datorer**. När du har hittat den virtuella datorn väljer du den.
3. Välj **Översikt**, under **Inställningar**, och **stoppa**sedan. Vänta tills **statusen** för den virtuella datorn ändras till **stoppad (Frigjord)** .
4. Välj **nätverk**under **Inställningar**.
5. Välj **Koppla från nätverks gränssnitt**. I listan över nätverks gränssnitt som är kopplade till den virtuella datorn väljer du det nätverks gränssnitt som du vill koppla från.

   >[!NOTE]
   >Om det bara finns ett nätverks gränssnitt i listan kan du inte koppla bort det eftersom det alltid måste finnas minst ett nätverks gränssnitt kopplat till en virtuell dator.
6. Välj **OK**.

### <a name="commands"></a>Kommandon

|Verktyg|Kommando|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Villkor

- En virtuell dator måste ha minst ett nätverks gränssnitt kopplat till sig.
- En virtuell dator kan bara ha så många nätverks gränssnitt som är anslutna till den virtuella datorns storlek. Mer information om hur många nätverks gränssnitt varje VM-storlek stöder finns i [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alla storlekar har stöd för minst två nätverks gränssnitt.
- Nätverks gränssnitt som du lägger till i en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverks gränssnitt finns i [skapa ett nätverks gränssnitt](virtual-network-network-interface.md#create-a-network-interface).
- Tidigare kunde nätverks gränssnitt bara läggas till i virtuella datorer som stödde flera nätverks gränssnitt och har skapats med minst två nätverks gränssnitt. Det gick inte att lägga till ett nätverks gränssnitt till en virtuell dator som har skapats med ett nätverks gränssnitt, även om den virtuella dator storleken stödde flera nätverks gränssnitt. Däremot kan du bara ta bort nätverks gränssnitt från en virtuell dator med minst tre nätverks gränssnitt, eftersom virtuella datorer som skapats med minst två nätverks gränssnitt alltid måste ha minst två nätverks gränssnitt. Ingen av dessa begränsningar gäller längre. Nu kan du skapa en virtuell dator med valfritt antal nätverks gränssnitt (upp till det antal som stöds av storleken på den virtuella datorn).
- Som standard definieras det första nätverks gränssnittet som är kopplat till en virtuell dator som det *primära* nätverks gränssnittet. Alla andra nätverks gränssnitt på den virtuella datorn är *sekundära* nätverks gränssnitt.
- Även om du kan styra vilka nätverks gränssnitt som du skickade utgående trafik till, skickas som standard all utgående trafik från den virtuella datorn till den IP-adress som tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet.
- Tidigare behövde alla virtuella datorer i samma tillgänglighets uppsättning ha ett enskilt eller flera nätverks gränssnitt. Virtuella datorer med valfritt antal nätverks gränssnitt kan nu finnas i samma tillgänglighets uppsättning, upp till det antal som stöds av storleken på den virtuella datorn. Du kan bara lägga till en virtuell dator i en tillgänglighets uppsättning när den skapas. Mer information om tillgänglighets uppsättningar finns i [Hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Även om nätverks gränssnitt i samma virtuella dator kan anslutas till olika undernät i ett virtuellt nätverk måste nätverks gränssnitten vara anslutna till samma virtuella nätverk.
- Du kan lägga till valfri IP-adress för alla IP-konfigurationer för ett primärt eller sekundärt nätverks gränssnitt till en Azure Load Balancer backend-pool. Tidigare kunde bara den primära IP-adressen för det primära nätverks gränssnittet läggas till i en backend-pool. Mer information om IP-adresser och konfigurationer finns i [lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).
- Om du tar bort en virtuell dator tas inte nätverks gränssnitten som är kopplade till den bort. När du tar bort en virtuell dator kopplas nätverks gränssnitten bort från den virtuella datorn. Du kan lägga till nätverks gränssnitten till olika virtuella datorer eller ta bort dem.
- Precis som med IPv6 kan du inte ansluta ett nätverks gränssnitt med accelererat nätverk aktiverat till en virtuell dator när du har skapat det. För att kunna dra nytta av accelererade nätverk måste du också slutföra stegen i det virtuella dator operativ systemet. Lär dig mer om accelererat nätverk och andra begränsningar när du använder dem för virtuella [Windows](create-vm-accelerated-networking-powershell.md) -eller [Linux](create-vm-accelerated-networking-cli.md) -datorer.

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar en virtuell dator med flera nätverks gränssnitt eller IP-adresser finns i följande artiklar:

|Aktivitet|Verktyg|
|---|---|
|Skapa en virtuell dator med flera nätverkskort|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Skapa en virtuell dator med en virtuell NÄTVERKSKORTs uppsättning med flera IPv4-adresser|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Skapa en virtuell nätverkskort virtuell dator med en privat IPv6-adress (bakom en Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager mall](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
