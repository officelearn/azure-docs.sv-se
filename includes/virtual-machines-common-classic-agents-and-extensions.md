

Med hjälp av VM-tillägg kan du göra följande:

* Ändra säkerhets- och identitetsfunktioner, till exempel återställa kontovärden och använda program mot skadlig kod
* Starta, stoppa och konfigurera övervakning och diagnostik
* Återställa/installera anslutningsfunktioner, till exempel RDP och SSH
* Diagnostisera, övervaka och hantera virtuella datorer

Det finns många andra funktioner också. Nya funktioner för virtuella datortillägg släpps regelbundet. I den här artikeln beskrivs Azure VM-agenterna för Windows och Linux och hur de ger stöd för funktionerna i VM-tillägget. En lista över virtuella datortillägg efter funktionskategori finns i [Azure VM-tillägg och -funktioner](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Azure VM-agenter för Windows- och Linux
Azure Virtual Machines-agenten (VM-agenten) är en säker, lätt processen som installerar, konfigurerar och tar bort VM-tillägg på instanser av virtuella datorer i Azure. VM-agenten fungerar som en säker lokal kontrolltjänst för Azure-VM. De tillägg som agenten läser in anger specifika funktioner för att öka produktiviteten med instansen.

Två Azure VM-agenter finns, en för virtuella datorer i Windows och en för virtuella Linux-datorer.

Om du vill att en virtuell datorinstans ska använda ett eller flera virtuella datortillägg måste instansen ha en VM-agent installerad. En avbildning av en virtuell dator som skapats med hjälp av Azure Portal och en avbildning från **Marketplace** installerar automatiskt en VM-agent under skapandeprocessen. Om en virtuell datorinstans saknar en VM-agent kan du installera den virtuella datoragenten när den virtuella datorinstansen har skapats. Du kan även installera agenten i en anpassad virtuell datoravbildning som du sedan laddar upp.

> [!IMPORTANT]
> Dessa VM-agenter är mycket lätta tjänster som möjliggör säker administration av virtuella datorinstanser. Det kan finnas fall där du inte vill ha den virtuella datoragenten. I så fall måste du skapa virtuella datorer som inte har VM-agenten som installerats med Azure CLI eller PowerShell. Även om den virtuella datoragenten kan tas bort fysiskt har beteendet för VM-tillägg på instansen inte definierats. Därför finns det inte stöd för att ta bort en installerad VM-agent.
>

Den virtuella datoragenten aktiveras i följande situationer:

* När du skapar en instans av en virtuell dator via Azure Portal och väljer en avbildning från **Marketplace**,
* När du skapar en instans av en virtuell dator med hjälp av cmdlet:en [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) eller [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Du kan skapa en virtuell dator utan en agent för virtuell dator genom att lägga till parametern **– DisableGuestAgent** i cmdlet:en [Add-AzureProvisioningConfigg](https://msdn.microsoft.com/library/azure/dn495299.aspx),

* När du manuellt hämtar och installerar den virtuella datoragenten på en befintlig VM-instans och anger värdet **ProvisionGuestAgent** till **sant**. Du kan använda denna teknik för Windows- och Linux-agenter med hjälp av ett PowerShell-kommando eller ett REST-anrop. (Om du inte anger värdet **ProvisionGuestAgent** när du har installerat den virtuella datoragenten kommer agenten inte att identifieras korrekt när den läggs till.) I följande kodexempel visas hur du gör detta med hjälp av PowerShell där argumentet `$svc` och `$name` redan har fastställts:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* När du skapar en VM-avbildning som innehåller en installerad VM-agent. När avbildningen med VM-agenten finns kan du ladda upp avbildningen till Azure. För en virtuell dator i Windows, hämtar du [MSI-filen till den virtuella Windows-datoragenten](http://go.microsoft.com/fwlink/?LinkID=394789) och installerar VM-agenten. För en virtuell Linux-dator installerar du den virtuella datoragenten från GitHub-lagringsplatsen som finns på <https://github.com/Azure/WALinuxAgent>. Mer information om hur du installerar den virtuella datoragenten i Linux finns i [användarhandboken för Azure Linux VM-agent](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> I PaaS kallas den virtuella datoragenten för **WindowsAzureGuestAgent** och alltid är tillgänglig på virtuella datorer med webb- och arbetsroll. (Mer information finns i [Azure-rollarkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) VM-agenten för roll-VM kan nu lägga till tillägg i molntjänst-VM på samma sätt som den gör för permanenta virtuella datorer. Den största skillnaden mellan VM-tillägg för roll-VM och permanenta virtuella datorer är processen för att lägga till VM-tillägg. Med roll-VM läggs tillägg först till i molntjänsten och sedan i distributionerna inom den molntjänsten.
>
> Använd cmdlet:en [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) för att visa en lista över VM-tillägg med alla tillgängliga roller.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Hitta, lägga till, uppdatera och ta bort tillägg för virtuell dator
Mer information om dessa uppgifter finns i [Lägg till, hitta, uppdatera och ta bort Azure VM-tillägg](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
