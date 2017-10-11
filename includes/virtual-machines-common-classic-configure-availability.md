


En tillgänglighetsuppsättning hjälper dig att hålla dina virtuella datorer som är tillgänglig under driftstopp, som under underhåll. Placera två eller flera liknande konfigurerad virtuella datorer i en tillgänglighetsuppsättning skapar redundans krävs för att bibehålla tillgängligheten för program eller tjänster som den virtuella datorn körs. Mer information om hur det fungerar finns [hantera tillgängligheten för virtuella datorer][Manage the availability of virtual machines].

Det är bäst att använda både tillgänglighetsuppsättningar och belastningsutjämning slutpunkter för att säkerställa att programmet alltid är tillgängliga och fungerar effektivt. Mer information om belastningsutjämnade slutpunkter finns [belastningsutjämning för Azure infrastrukturtjänster][Load balancing for Azure infrastructure services].

Du kan lägga till den klassiska virtuella datorer i en tillgänglighetsuppsättning med hjälp av ett av två alternativ:

* [Alternativ 1: Skapa en virtuell dator och en tillgänglighetsuppsättning samtidigt][Option 1: Create a virtual machine and an availability set at the same time]. Sedan kan lägga till nya virtuella datorer till uppsättningen med när du skapar de virtuella datorerna.
* [Alternativ 2: Lägg till en befintlig virtuell dator i en tillgänglighetsuppsättning][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> I den klassiska modellen, måste virtuella datorer som du vill ska ingå i samma tillgänglighetsuppsättning tillhöra samma tjänst i molnet.
> 
> 

## <a id="createset"></a>Alternativ 1: skapa en virtuell dator och en tillgänglighetsuppsättning på samma gång
Du kan använda Azure-portalen eller Azure PowerShell-kommandon för att göra detta.

Använda Azure portal:

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).
2. På navmenyn klickar du på **+ ny**, och klicka sedan på **virtuella**.
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. Välj Marketplace-virtuell-avbildning som du vill använda. Du kan välja att skapa en Linux- eller Windows virtuell dator.
4. För den valda virtuella datorn kontrollerar du att distributionsmodell är inställd på **klassiska** och klicka sedan på **skapa**
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Ange ett virtuellt datornamn, användarnamn och lösenord (för Windows-datorer) eller offentlig SSH-nyckel (för Linux-datorer). 
6. Välj VM-storlek och klicka sedan på **Välj** att fortsätta.
7. Välj **valfri konfiguration > tillgänglighetsuppsättning**, och välj tillgänglighetsuppsättning du vill lägga till den virtuella datorn till.
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Granska inställningarna. När du är klar klickar du på **skapa**.
9. Medan Azure skapar den virtuella datorn, du kan följa förloppet under **virtuella datorer** på navmenyn.

Om du vill använda Azure PowerShell-kommandon för att skapa en virtuell Azure-dator och lägga till den i en ny eller befintlig tillgänglighetsuppsättning, se [använda Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"></a>Alternativ 2: Lägg till en befintlig virtuell dator i en tillgänglighetsuppsättning
Du kan lägga till befintliga klassiska virtuella datorer i en befintlig tillgänglighetsuppsättning ange eller skapa en ny för dem i Azure-portalen. (Tänk på att de virtuella datorerna i samma tillgänglighetsuppsättning måste tillhöra samma Molntjänsten.) Stegen är nästan desamma. Du kan använda Azure PowerShell för att lägga till den virtuella datorn till en befintlig tillgänglighetsuppsättning.

1. Om du inte redan har gjort det, logga in på den [Azure-portalen](https://portal.azure.com).
2. På navmenyn klickar du på **virtuella datorer (klassisk)**.
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Lista över virtuella datorer, Välj namnet på den virtuella dator som du vill lägga till i uppsättningen.
4. Välj **tillgänglighetsuppsättning** från den virtuella datorn **inställningar**.
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Välj den tillgänglighetsuppsättningen som du vill lägga till den virtuella datorn till. Den virtuella datorn måste tillhöra samma Molntjänsten som tillgänglighetsuppsättningen.
   
    ![ALT bildtext](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Klicka på **Spara**.

Öppna en administratörsnivå Azure PowerShell-session och kör följande kommando för att använda Azure PowerShell-kommandon. Platshållare (exempelvis &lt;VmCloudServiceName&gt;), Ersätt allt inom citattecken, inklusive den < och > tecken, med rätt namn.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Den virtuella datorn kanske måste startas om för att du har lagt till den tillgänglighetsuppsättningen.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

