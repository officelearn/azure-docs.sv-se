## <a name="overview"></a>Översikt
När du skapar en ny virtuell dator (VM) i en resursgrupp genom att distribuera en avbildning från [Azure Marketplace](https://azure.microsoft.com/marketplace/), OS-Standardenheten är ofta 127 GB (vissa bilder har mindre diskstorlekar för OS som standard). Även om det är möjligt att lägga till datadiskar i den virtuella datorn (hur många beror på vilken SKU som har valts) rekommenderas du att installera program och processorintensiva arbetsbelastningar på de här tilläggsdiskarna eftersom kunder ofta behöver expandera operativsystemenheten för att kunna hantera följande typ av scenarier:

1. Stöd för äldre program som installerar komponenter på operativsystemenheten.
2. Migrera en fysisk eller virtuell dator från lokal plats med större operativsystemenhet.

> [!IMPORTANT]
> I Azure finns två olika distributionsmodeller för att skapa och arbeta med resurser: Resource Manager och klassisk. I den här artikeln beskrivs hur du använder Resource Manager-modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> 
> 
> [!WARNING]
> Ändra storlek på OS-disken på en virtuell dator i Azure kommer den att starta om.
>

## <a name="resize-the-os-drive"></a>Ändra storlek på operativsystemenheten
I den här artikeln ska vi ändra storlek på operativsystemenheten med hjälp av Resource Manager-moduler i [Azure Powershell](/powershell/azureps-cmdlets-docs). Ändra storlek på OS-enhet för både Unamanged och hanterade diskar eftersom metoden att ändra storlek på diskar som skiljer sig mellan både disktyper visas.

### <a name="for-resizing-unmanaged-disks"></a>För att ändra storlek på ohanterade diskar:

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på Microsoft Azure-kontot i resurshanteringsläge och välj din prenumeration enligt följande:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ange namn på resursgrupp och virtuell dator på följande sätt:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Hämta en referens till din virtuella dator på följande sätt:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Stoppa den virtuella datorn innan du ändrar storlek på disken på följande sätt:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Och nu till det vi väntat på! Ange storleken på ohanterade OS-disken till önskat värde och uppdatera den virtuella datorn enligt följande:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2 048 GB för OS-diskar. (Det är möjligt att utöka VHD-blobben utöver den storleken, men Operativsystemet kommer bara att kunna arbeta med den första 2048 GB utrymme.)
   > 
   > 
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>För att ändra storlek på hanterade diskar:

Öppna Powershell ISE eller Powershell-fönstret i administrativt läge och följ anvisningarna nedan:

1. Logga in på Microsoft Azure-kontot i resurshanteringsläge och välj din prenumeration enligt följande:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ange namn på resursgrupp och virtuell dator på följande sätt:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Hämta en referens till din virtuella dator på följande sätt:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Stoppa den virtuella datorn innan du ändrar storlek på disken på följande sätt:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Hämta en referens till hanterade OS-disk. Ange storleken på den hanterade OS-disken till önskat värde och uppdatera disken enligt följande:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Den nya storleken måste vara större än den befintliga. Det högsta tillåtna är 2 048 GB för OS-diskar. (Det är möjligt att utöka VHD-blobben utöver den storleken, men Operativsystemet kommer bara att kunna arbeta med den första 2048 GB utrymme.)
   > 
   > 
6. Det kan ta några sekunder att uppdatera den virtuella datorn. När kommandot har körts klart startar du om den virtuella datorn på följande sätt:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Klart! Anslut till den virtuella datorn via RDP, öppna Datorhantering (eller Diskhantering) och expandera enheten med det nya utrymmet.

## <a name="summary"></a>Sammanfattning
I den här artikeln har vi använt Azure Resource Manager-modulerna i Powershell för att öka storleken på operativsystemenheten för en virtuell IaaS-dator. Nedan är det fullständiga skriptet som referens för både ohanterade och hanterade diskar:

Unamanged diskar:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Hanterade diskar:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Nästa steg
Men i den här artikeln fokuserar huvudsakligen på expanderar Unamanged/hanterad OS-disken på den virtuella datorn, användas utvecklade skriptet också för att expandera datadiskar som är kopplade till den virtuella datorn. Om du exempelvis vill expandera den första disken som är ansluten till den virtuella datorn, ersätter du objektet ```OSDisk``` i ```StorageProfile``` med matrisen ```DataDisks``` och använder ett numeriskt index för att hämta en referens till den första anslutna disken, på följande sätt:

Unamanged Disk:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Hanterade diskar:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

På samma sätt kan du referera till andra datadiskar som är kopplade till den virtuella datorn antingen genom att använda ett index som ovan eller med diskens ```Name```-egenskap som på bilden nedan:

Unamanged Disk:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Hanterade Disk:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Om du vill ta reda på hur du kopplar diskar till en virtuell Azure Resource Manager-dator läser du [artikeln](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

