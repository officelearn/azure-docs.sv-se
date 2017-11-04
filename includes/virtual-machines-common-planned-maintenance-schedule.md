

## <a name="multi-and-single-instance-vms"></a>Flera och instans virtuella datorer
Många kunder som körs på Azure antal kritiska kan schemalägga om deras virtuella datorer genomgår planerat underhåll på grund av avbrott--cirka 15 minuter--som uppstår under underhåll. Du kan använda tillgänglighetsuppsättningar för att kontrollera när etablerade virtuella datorer får planerat underhåll.

Det finns två möjliga konfigurationer för virtuella datorer som körs på Azure. Virtuella datorer är konfigurerade som flerinstans- eller eninstanskategori. Om virtuella datorer i en tillgänglighetsuppsättning, är de konfigurerade som flera instanser. Observera, även enstaka virtuella datorer kan distribueras i en tillgänglighetsuppsättning, så att de behandlas som flera instanser. Om virtuella datorer som inte är i en tillgänglighetsuppsättning är de konfigurerade som eninstanskategori.  Mer information om tillgänglighetsuppsättningar finns [hantera tillgängligheten för din virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [hantera tillgängligheten för din virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Planerat underhåll uppdateringar till single instance och flera instanser VMs inträffa separat. Du kan styra när deras virtuella datorer får planerat underhåll genom att omkonfigurera din virtuella dator ska single instance (om de finns flera instanser) eller att flera instanser (om de är single instance). Se [planerat underhåll för virtuella Azure Linux-datorer](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [planerat underhåll för Windows Azure virtuella datorer](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) information om planerat underhåll för virtuella Azure-datorer.

## <a name="for-multi-instance-configuration"></a>För konfiguration av flera instanser
Du kan välja den tid som planerat underhåll påverkar dina virtuella datorer som distribueras i en Tillgänglighetsuppsättning konfiguration genom att ta bort dessa virtuella datorer från tillgänglighetsuppsättningar.

1. Ett e-postmeddelande har skickats till dig sju kalenderdagar innan planerat underhåll till dina virtuella datorer i en konfiguration med flera instanser. Prenumerations-ID: N och alla berörda flera instanser på virtuella datorer ingår i brödtexten i e-postmeddelandet.
2. Under sju dagar, kan du välja den tid dina instanser uppdateras genom att ta bort dina virtuella datorer med flera instanser i den regionen från sina tillgänglighetsuppsättning. Den här ändringen i konfigurationen gör en omstart, som den virtuella datorn flyttas från en fysisk värd som mål för underhåll, till en annan fysisk värd som inte är avsedda för underhåll.
3. Du kan ta bort den virtuella datorn från dess tillgänglighetsuppsättning i Azure-portalen.

   1. Välj den virtuella datorn att ta bort från Tillgänglighetsuppsättningen i portalen.  

   2. Under **inställningar**, klickar du på **tillgänglighetsuppsättning**.

      ![Val av Tillgänglighetsuppsättning](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. Ange listrutan i tillgängligheten, markerar ”inte en del av en tillgänglighetsuppsättning”.

      ![Ta bort från](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Överst, klickar du på **spara**. Klicka på **Ja** att bekräfta att den här åtgärden startar om den virtuella datorn.

   >[!TIP]
   >Du kan konfigurera den virtuella datorn till flera instanser senare genom att välja något av de listade tillgänglighetsuppsättningarna.

4. Virtuella datorer tas bort från tillgänglighetsuppsättningar flyttas till Single-Instance värdar och har uppdaterats inte under planerat underhåll tillgänglighet ange konfigurationer.
5. När uppdateringen till tillgänglighet ange virtuella datorer är klar (enligt schema som beskrivs i det ursprungliga e-postmeddelandet), bör du lägga till de virtuella datorerna tillbaka till sina tillgänglighetsuppsättningar. Bli en del av en tillgänglighetsuppsättning automatiskt om de virtuella datorerna som flera instanser och resulterar i en omstart. Vanligtvis när alla uppdateringar i flera instanser har slutförts över hela Azure-miljön, single instance Underhåll visas nedan.

Om du tar bort en virtuell dator från en tillgänglighetsuppsättning kan även uppnås med hjälp av Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>För Single-instance-konfiguration
Du kan välja den tid som planerat underhåll påverkar du virtuella datorer i en enda instans konfiguration genom att lägga till dessa virtuella datorer i tillgänglighetsuppsättningar.

Steg för steg

1. Ett e-postmeddelande har skickats till dig sju kalenderdagar innan planerat underhåll till virtuella datorer i en enda instans-konfiguration. Prenumerations-ID: N och alla berörda Single-Instance virtuella datorer ingår i brödtexten i e-postmeddelandet.
2. Du kan välja den tid som din instans startas om genom att lägga till Single-instance-VMs en tillgänglighetsuppsättning i samma regionen under de sju dagarna. Den här ändringen i konfigurationen gör en omstart, som den virtuella datorn flyttas från en fysisk värd som mål för underhåll, till en annan fysisk värd som inte är avsedda för underhåll.
3. Följ anvisningarna här för att lägga till befintliga virtuella datorer i tillgänglighetsuppsättningar med hjälp av Azure-portalen och Azure PowerShell. (Se Azure PowerShell-exempel som följer de här stegen.)
4. När dessa virtuella datorer konfigureras som flera instanser, är de undantagna från planerat underhåll till Single-instance virtuella datorer.
5. När single instance VM-uppdateringen är klar (enligt schema i det ursprungliga e-postmeddelandet) returnera du de virtuella datorerna till single instance genom att ta bort de virtuella datorerna från sina tillgänglighetsuppsättningar.

Lägga till en virtuell dator i en tillgänglighetsuppsättning också kan uppnås med hjälp av Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
