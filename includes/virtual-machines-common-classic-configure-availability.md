---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 32f533d06b7db0284459951e65f9c04fe0bb0285
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485389"
---
En tillgänglighetsuppsättning som hjälper dig att hålla dina virtuella datorer som är tillgängliga under avbrott, till exempel under underhåll. Placera två eller flera liknande konfiguration virtuella datorer i en tillgänglighetsuppsättning skapar stabilitet som krävs för att behålla tillgängligheten för program eller tjänster som den virtuella datorn körs. Mer information om hur det fungerar finns i [hantera tillgängligheten för virtuella datorer][Manage the availability of virtual machines].

Det är en bra idé att använda både tillgänglighetsuppsättningar och belastningsutjämnande slutpunkter för att säkerställa att ditt program alltid är tillgänglig och körs effektivt. Mer information om belastningsutjämnade slutpunkter finns [belastningsutjämning för Azures infrastrukturtjänster][Load balancing for Azure infrastructure services].

Du kan lägga till klassiska virtuella datorer i en tillgänglighetsuppsättning med hjälp av ett av två alternativ:

* [Alternativ 1: Skapa en virtuell dator och en tillgänglighetsuppsättning på samma gång][Option 1: Create a virtual machine and an availability set at the same time]. Lägg sedan till nya virtuella datorer i uppsättningen när du skapar de virtuella datorerna.
* [Alternativ 2: Lägg till en befintlig virtuell dator i en tillgänglighetsuppsättning][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> I den klassiska modellen, måste virtuella datorer som ska placeras i samma tillgänglighetsuppsättning tillhöra samma molntjänst.
> 
> 

## <a id="createset"> </a>Alternativ 1: Skapa en virtuell dator och en tillgänglighetsuppsättning på samma gång
Du kan använda Azure portal eller Azure PowerShell-kommandon för att göra detta.

Använda Azure portal:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs** > **Compute**.
3. Välj Marketplace-VM-avbildning som du vill använda. Du kan välja att skapa en Linux eller Windows-dator.
4. För den valda virtuella datorn kontrollerar du att distributionsmodellen är inställd på **klassiska** och klicka sedan på **skapa**
   
    ![Alternativ bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Ange ett virtuellt datornamn, användarnamn och lösenord (för Windows-datorer) eller offentlig SSH-nyckel (för Linux-datorer). 
6. Välj virtuella datorstorlek och klicka sedan på **Välj** att fortsätta.
7. Välj **valfri konfiguration > tillgänglighetsuppsättning**, och välj den tillgänglighetsuppsättning som du vill lägga till den virtuella datorn till.
   
    ![Alternativ bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Granska dina inställningar. När du är klar klickar du på **skapa**.
9. Medan Azure skapar den virtuella datorn kan du följa förloppet under **virtuella datorer** på navmenyn.

Om du vill använda Azure PowerShell-kommandon för att skapa en Azure virtuell dator och lägga till den i en ny eller befintlig tillgänglighetsuppsättning, se [använder Azure PowerShell för att skapa och förkonfigurera en Windows-baserade virtuella datorer](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Alternativ 2: Lägg till en befintlig virtuell dator i en tillgänglighetsuppsättning
Du kan lägga till befintliga klassiska virtuella datorer i en befintlig tillgänglighetsuppsättning ange eller skapa ett nytt lösenord för dem i Azure-portalen. (Kom ihåg att de virtuella datorerna i samma tillgänglighetsuppsättning måste tillhöra samma molntjänst.) Stegen är nästan desamma. Du kan använda Azure PowerShell för att lägga till den virtuella datorn till en befintlig tillgänglighetsuppsättning.

1. Om du inte redan har gjort det loggar du in den [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster klickar du på **virtuella datorer (klassiska)**.
   
    ![Alternativ bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Listan över virtuella datorer, Välj namnet på den virtuella dator som du vill lägga till i uppsättningen.
4. Välj **tillgänglighetsuppsättning** från den virtuella datorn **inställningar**.
   
    ![Alternativ bildtext](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Välj tillgänglighetsuppsättning som du vill lägga till den virtuella datorn. Den virtuella datorn måste tillhöra samma molntjänst som tillgänglighetsuppsättning.
   
    ![Alternativ bildtext](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Klicka på **Spara**.

Öppna en behörighet på Azure PowerShell-session och kör följande kommando för att använda Azure PowerShell-kommandon. Platshållare (till exempel &lt;VmCloudServiceName&gt;), Ersätt allt inom citattecken, inklusive den < och > tecken, med rätt namn.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Den virtuella datorn kanske startas om för att slutföra registreringen i tillgänglighetsuppsättningen.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

