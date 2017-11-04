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
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 7df1dfbea8c985907d5330819dc1e7bf1578aafa
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Lägg till nätverksgränssnitt till eller ta bort från virtuella datorer

Lär dig hur du lägger till en befintlig nätverksgränssnittet när du skapar en virtuell dator eller Lägg till eller ta bort nätverksgränssnitt från en befintlig virtuell dator i tillståndet Stoppad (frigjord). Ett nätverksgränssnitt kan ett Azure Virtual Machine (VM-namn) kan kommunicera med Internet, Azure och lokala resurser. En virtuell dator kan ha en eller flera nätverksgränssnitt. 

Om du behöver lägga till, ändra eller ta bort IP-adresser för ett nätverksgränssnitt, läsa den [hantera IP-adresser i nätverket gränssnittet](virtual-network-network-interface-addresses.md) artikel. Om du behöver för att skapa, ändra eller ta bort nätverksgränssnitt, kan du läsa den [hantera nätverksgränssnitt](virtual-network-network-interface.md) artikel.

## <a name="before"></a>Innan du börjar

Utför följande uppgifter innan du slutför alla steg i alla avsnitt i den här artikeln:

- Lär dig mer om hur många nätverkskort stöder varje Linux och Windows VM-storlek genom att granska den [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar.
- Logga in på Azure [portal](https://portal.azure.com), Azure-kommandoradsgränssnittet (CLI) eller Azure PowerShell med ett Azure-konto. Om du inte redan har ett Azure-konto, registrera dig för en [ledigt utvärderingskonto](https://azure.microsoft.com/free).
- Om du använder PowerShell-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure PowerShell-kommandon som är installerad. Om du vill få hjälp med PowerShell-kommandon med exempel på, skriver `get-help <command> -full`.
- Om Azure-kommandoradsgränssnittet (CLI)-kommandon för att utföra åtgärder i den här artikeln [installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Se till att du har den senaste versionen av Azure CLI installerad. Om du vill få hjälp med CLI-kommandon, Skriv `az <command> --help`. Du kan använda Azure Cloud-gränssnittet i stället för att installera CLI och dess krav. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Om du vill använda molnet Shell klickar du på molnet Shell **> _** längst upp i den [portal](https://portal.azure.com).

## <a name="about"></a>Om nätverksgränssnitt och virtuella datorer

Du kan lägga till (bifoga) ett befintliga nätverksgränssnittet till en virtuell dator när du skapar den virtuella datorn som nätverksgränssnittet är inte ansluten till en annan virtuell dator. Du kan lägga till ett nätverksgränssnitt till eller ta bort (frånkoppla) ett nätverksgränssnitt för från en befintlig virtuell dator, som den virtuella datorn är i tillståndet Stoppad (frigjord). Om du skapar en virtuell dator med hjälp av Azure portal skapas portalen ett nätverksgränssnitt med standardinställningar. Portalen tillåter inte att du:

- Ange ett befintligt nätverksgränssnitt för att lägga till när du skapar den virtuella datorn
- Skapa en virtuell dator med flera nätverksgränssnitt
- Ange ett namn för nätverksgränssnitt (portalen skapar nätverksgränssnittet med ett standardnamn som)

Du kan använda Azure PowerShell eller CLI för att skapa ett nätverksgränssnitt eller virtuell dator med de föregående attribut som du inte kan använda portalen för. Överväg följande begränsningar och -funktioner innan du slutför uppgifterna i följande avsnitt:

- Alla VM-storlekar stöd minst två nätverkskort, men vissa VM-storlekar stöd för fler än två nätverksgränssnitt. Tidigare stöds endast vissa VM-storlekar ett nätverksgränssnitt. Att lära dig hur många nätverksgränssnitt varje VM storlek har stöd för, läsa den [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar. 
- Tidigare kunde endast nätverksgränssnitt läggas till virtuella datorer som har stöd för flera nätverksgränssnitt och har skapats med minst två nätverksgränssnitt. Du kunde inte lägga till ett nätverksgränssnitt till en virtuell dator som har skapats med ett nätverksgränssnitt, även om den Virtuella datorstorleken stöd för flera nätverksgränssnitt. Omvänt kan du kan bara ta bort nätverksgränssnitt från en virtuell dator med minst tre nätverksgränssnitt, eftersom virtuella datorer som har skapats med minst två nätverk gränssnitt alltid måste ha minst två nätverksgränssnitt. Ingen av dessa villkor gäller längre. Du kan nu skapa en virtuell dator med valfritt antal nätverksgränssnitt (upp till antalet som stöds av VM-storlek) och lägga till eller ta bort alla antalet nätverksgränssnitt (från virtuella datorer i tillståndet Stoppad (frigjord)), så länge som den virtuella datorn har alltid minst ett nätverksgränssnitt.
- Som standard definieras första nätverksgränssnittet på en virtuell dator som den *primära* nätverksgränssnitt. Alla andra nätverksgränssnitt på den virtuella datorn är *sekundära* nätverksgränssnitt.
- Primära nätverksgränssnitt som är tilldelade en standard-gateway som Azure DHCP-servrar, men sekundära nätverksgränssnitt är inte. Eftersom sekundära nätverksgränssnitt har inte tilldelats en standard-gateway, inte kan de kommunicera med resurser utanför deras undernät, som standard. Om du vill aktivera sekundära nätverksgränssnitt kan kommunicera med resurser utanför deras finns [routning i ett operativsystem för virtuella datorer med flera nätverksgränssnitt](#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces).
- Som standard skickas all utgående trafik från Virtuellt IP-adressen till den primära IP-adresskonfigurationen för det primära nätverksgränssnittet. Du kan styra vilken IP-adress används för utgående trafik i operativsystemet för den virtuella datorn, men det är som standard via det primära nätverksgränssnittet.
- Tidigare var alla virtuella datorer i samma tillgänglighetsuppsättning måste ha en enda eller flera nätverksgränssnitt. Virtuella datorer med valfritt antal nätverksgränssnitt kan nu finnas i samma tillgänglighetsuppsättning, upp till antalet som stöds av VM-storlek. Du kan bara lägga till en virtuell dator till en tillgänglighetsuppsättning när den skapas om. Mer information om tillgänglighetsuppsättningar i [hantera tillgängligheten för virtuella datorer i Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) artikel.
- Medan nätverksgränssnitt i samma virtuella dator kan vara ansluten till olika undernät inom ett VNet, måste nätverksgränssnitt alla vara ansluten till samma virtuella nätverk.
- Du kan lägga till IP-adresser för alla IP-konfiguration för alla primära eller sekundära nätverksgränssnittet för en Azure-belastningsutjämnaren backend-adresspool. Tidigare kunde endast primära IP-adressen för det primära nätverksgränssnittet läggas till en backend-adresspool. Mer information om IP-adresser och konfigurationer av [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md) artikel.
- Om du tar bort en virtuell dator tar inte bort nätverksgränssnitt som är kopplade till den. När en virtuell dator tas bort oberoende nätverksgränssnitt från den virtuella datorn. Du kan lägga till nätverksgränssnitt i olika virtuella datorer eller ta bort dem.
- Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, kan du koppla den till en virtuell dator när du skapar den virtuella datorn. Du kan inte koppla ett nätverksgränssnitt med en tilldelad IPv6-adress till en virtuell dator när den virtuella datorn har skapats. Om du kopplar ett nätverksgränssnitt med en tilldelad privata IPv6-adress när du skapar en virtuell dator kan koppla du endast nätverksgränssnittet till den virtuella datorn, oavsett hur många nätverkskort som stöder VM-storlek. Se [Network interface IP-adresser](virtual-network-network-interface-addresses.md) lära dig mer om att tilldela IP-adresser till nätverksgränssnitt.

## <a name="vm-create"></a>Lägg till befintliga nätverksgränssnitt i en ny virtuell dator

När du skapar en virtuell dator via portalen portalen skapar ett nätverksgränssnitt med standardinställningar och kopplas till den virtuella datorn för dig. Du kan inte lägga till befintliga nätverksgränssnitt i en ny virtuell dator eller skapa en virtuell dator med flera nätverksgränssnitt med Azure-portalen. Du kan göra båda med CLI eller PowerShell. Du kan lägga till så många nätverksgränssnitt till en virtuell dator som har stöd för VM-storlek som du skapar. Om du vill veta mer om hur många nätverkskort stöder varje VM-storlek kan läsa den [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar. Nätverksgränssnitt som du lägger till en virtuell dator kan för närvarande inte kopplas till en annan virtuell dator. Mer information om hur du skapar nätverksgränssnitt den [hantera nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface) artikel.

### <a name="routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces"></a>Routning i ett operativsystem för virtuella datorer med flera nätverksgränssnitt

Azure tilldelar en standard-gateway till det första (primära) nätverksgränssnittet kopplade till den virtuella datorn. Azure tilldela inte en standardgateway på ytterligare (sekundär) nätverksgränssnitt som är kopplad till en virtuell dator. Du kan därför inte att kommunicera med resurser utanför det undernät som sekundärt nätverksgränssnitt finns som standard. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät, även om stegen för att aktivera kommunikation är olika för olika operativsystem.

### <a name="windows"></a>Windows

Gör följande i en kommandotolk:

1. Kör den `route print` kommando som returnerar utdata som liknar följande utdata för en virtuell dator med två anslutna nätverksgränssnitten:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    I det här exemplet **Microsoft Hyper-V Network Adapter #4** (gränssnitt 7) är sekundärt nätverksgränssnitt som inte har en standard-gateway som har tilldelats.

2. Kör från en kommandotolk i `ipconfig` kommandot för att se vilken IP-adress har tilldelats det sekundära nätverksgränssnittet. I det här exemplet tilldelas 192.168.2.4 gränssnittet 7. Ingen standard gateway-adress returneras för det sekundära nätverksgränssnittet.

3. Kör följande kommando för att vidarebefordra all trafik för adresser utanför undernätet i sekundära nätverksgränssnitt och gateway för undernätet:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Gateway-adressen för undernätet är den första IP-adress (som slutar i.1) i adressintervall som definierats för undernätet. Om du inte vill att vidarebefordra all trafik utanför undernätet, kan du lägga till enskilda vägar till specifika mål i stället. Om du vill dirigera trafik från det sekundära nätverksgränssnittet till 192.168.3.0 till exempel nätverk, du anger kommandot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. För att bekräfta lyckade kommunikation med en resurs på 192.168.3.0 nätverk, exempelvis ange följande kommando för att pinga 192.168.3.4 gränssnittet 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Du kan behöva öppna ICMP via Windows-brandväggen på den enhet som du pinga med följande kommando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. För att bekräfta tillagda vägen är i routningstabellen, ange den `route print` kommando som returnerar utdata som liknar följande:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Vägen som visas i listan med *192.168.1.1* under **Gateway**, är det flöde som är det som standard för det primära nätverksgränssnittet. Vägen med *192.168.2.1* under **Gateway**, är flödet som du har lagt till.

### <a name="linux"></a>Linux

Eftersom standardbeteendet använder svaga värden routning, rekommenderar vi att begränsa sekundära gränssnitt nätverkstrafik mellan resurser i samma undernät. Om du behöver kommunikation utanför undernätet för sekundära nätverksgränssnitt måste du skapa regler för routning som gör att den virtuella datorn att skicka och ta emot trafik via ett visst nätverksgränssnitt. Annars trafik som hör till eth1, till exempel kan inte bearbetas på rätt sätt med definierade standardvägen. Information om hur du konfigurerar regler för routning finns [konfigurera Linux för flera nätverkskort](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics).

> [!WARNING]
> Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, du kan bara lägga till nätverksgränssnittet till den virtuella datorn när du skapar den virtuella datorn. Du kan inte bifoga mer än ett nätverksgränssnitt på den virtuella datorn när du skapar den virtuella datorn, eller när den virtuella datorn har skapats, så länge som en IPv6 adress har tilldelats ett nätverksgränssnitt som är kopplad till en virtuell dator. Se [Network interface IP-adresser](virtual-network-network-interface-addresses.md) lära dig mer om att tilldela IP-adresser till nätverksgränssnitt.

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[Skapa AZ vm](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Lägga till en befintlig nätverksgränssnitt på en befintlig virtuell dator

Du kan lägga till så många nätverksgränssnitt till en virtuell dator som har stöd för VM-storlek som du vill lägga till nätverksgränssnitt som. Att lära dig hur många nätverksgränssnitt varje VM storlek har stöd för, läsa den [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM-storlekar artiklar. Den virtuella datorn som du vill lägga till ett nätverksgränssnitt och måste ha stöd för antalet nätverksgränssnitt som du vill lägga till och måste vara i tillståndet Stoppad (frigjord). Nätverksgränssnitt som du vill lägga till kan för närvarande inte kopplas till en annan virtuell dator. Du kan inte lägga till nätverkskort till en befintlig virtuell dator med hjälp av Azure portal. Lägg till nätverksgränssnitt i en befintlig virtuell dator, måste du använda CLI eller PowerShell. 

Azure tilldelar en standard-gateway till det första (primära) nätverksgränssnittet kopplade till den virtuella datorn. Azure tilldela inte en standardgateway på ytterligare (sekundär) nätverksgränssnitt som är kopplad till en virtuell dator. Du kan därför inte att kommunicera med resurser utanför det undernät som sekundärt nätverksgränssnitt finns som standard. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät. Om du kräver att din sekundära nätverksgränssnitt kommunikation med resurser utanför deras undernät, se [routning i ett operativsystem för virtuella datorer med flera nätverksgränssnitt](#routing-within-a virtual-machine-operating-system-with-multiple-network-interfaces).

> [!WARNING]
> Om ett nätverksgränssnitt har en privat IPv6-adress som tilldelats, kan inte läggas till en befintlig virtuell dator. Du kan bara lägga till ett nätverksgränssnitt med en tilldelad privata IPv6-adress till en virtuell dator när du skapar en virtuell dator. Se [Network interface IP-adresser](virtual-network-network-interface-addresses.md) lära dig mer om att tilldela IP-adresser till nätverksgränssnitt.

|Verktyget|Kommando|
|---|---|
|CLI|[Lägg till AZ vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referens) eller [detaljerade steg](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referens) eller [detaljerade steg](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>Visa nätverksgränssnitt för en virtuell dator

Du kan visa nätverksgränssnitt som är anslutna till en virtuell dator mer information om konfigurationen för varje nätverksgränssnitt och IP-adresser som tilldelats varje nätverksgränssnitt. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller Network-deltagare för din prenumeration. Läs mer om att tilldela roller till konton i [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *virtuella datorer*. När **virtuella datorer** visas i sökresultaten klickar du på den.
3. I den **virtuella datorer** bladet som visas, klicka på namnet på den virtuella datorn som du vill visa nätverksgränssnitt för.
4. I den **inställningar** valt avsnitt i bladet för virtuella datorer som visas för den virtuella datorn, klickar du på **nätverk**. Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem, läsa den [hantera nätverksgränssnitt](virtual-network-network-interface.md) artikel. Mer information om hur du lägger till, ändrar eller tar bort IP-adresser tilldelas ett nätverksgränssnitt finns [hantera IP-adresser](virtual-network-network-interface-addresses.md).

**Kommandon**

|Verktyget|Kommando|
|---|---|
|CLI|[AZ vm visa](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Ta bort ett nätverksgränssnitt från en virtuell dator

Den virtuella datorn som du vill ta bort (eller koppla från) ett nätverksgränssnitt från måste vara i tillståndet Stoppad (frigjord) och måste ha minst två nätverksgränssnitt kopplade till den. Du kan ta bort alla nätverksgränssnitt, men den virtuella datorn måste alltid ha minst ett nätverksgränssnitt som är kopplade till den. Om du tar bort ett primärt nätverksgränssnitt Azure tilldelar attributet primära nätverksgränssnittet som kopplas till den virtuella datorn den längsta. 

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen ägare, deltagare eller Network-deltagare för din prenumeration. Läs mer om att tilldela roller till konton i [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *virtuella datorer*. När **virtuella datorer** visas i sökresultaten klickar du på den.
3. I den **virtuella datorer** bladet som visas, klicka på namnet på den virtuella datorn som du vill ta bort ett nätverksgränssnitt för.
4. I den **inställningar** valt avsnitt i bladet för virtuella datorer som visas för den virtuella datorn, klickar du på **nätverk**. Mer information om inställningar för nätverksgränssnitt och hur du ändrar dem, läsa den [hantera nätverksgränssnitt](virtual-network-network-interface.md) artikel. Mer information om hur du lägger till, ändrar eller tar bort IP-adresser tilldelas ett nätverksgränssnitt finns [hantera IP-adresser](virtual-network-network-interface-addresses.md).
5. Klicka på **X koppla från nätverksgränssnittet**.
6. Välj nätverksgränssnitt som du vill koppla från den nedrullningsbara listan och klicka sedan på **OK**.

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
