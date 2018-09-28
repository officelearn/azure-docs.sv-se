<a name="-headarticlesvirtual-machinestroubleshootingtroubleshoot-recovery-disks-linuxmd"></a><<<<<<< HEAD:articles/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux.md
---

Rubrik: använda en Linux felsöknings-VM med Azure CLI | Microsoft Docs beskrivning: Lär dig att felsöka problem med Linux VM genom att ansluta operativsystemdisken till en virtuell dator med Azure CLI-tjänster för återställning: virtuella datorer linux documentationCenter: '' författare: genlin manager: jeconnoc editor: ''

MS.Service: virtuella datorer linux ms.devlang: azurecli ms.topic: felsökning ms.tgt_pltfrm: vm-linux ms.workload: infrastruktur ms.date: 2017-02-16 ms.author: genli

---

# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Felsöka en Linux-VM genom att koppla OS-disken till en virtuell dator med Azure CLI för återställning
Om din Linux-dator (VM) påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på den virtuella hårddisken. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab` som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure CLI för att ansluta den virtuella hårddisken till en annan Linux VM att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 


## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn uppstår några problem, som de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan Linux-VM i felsökningssyfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Om du vill utföra de här felsökningsstegen du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

I följande exempel, ersätter du parameternamn med dina egna värden. Parametern exempelnamnen inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.


## <a name="determine-boot-issues"></a>Fastställa startproblem
Granska seriell utdata för att avgöra varför den virtuella datorn är inte kan starta korrekt. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab`, eller den underliggande virtuella hårddisken tas bort eller flyttas.

Hämta startlogg med [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). I följande exempel hämtas seriell utdata från den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Granska seriell utdata för att avgöra varför den virtuella datorn kan inte starta. Om seriell utdata inte är att tillhandahålla ingen indikation kan du behöva granska loggfiler i `/var/log` när du har den virtuella hårddisken är ansluten till en Virtuella felsökningsdatorn.


## <a name="view-existing-virtual-hard-disk-details"></a>Se information om en befintlig virtuell hårddisk
Innan du kan koppla din virtuella hårddisk (VHD) till en annan virtuell dator, måste du identifiera URI för OS-disken. 

Visa information om den virtuella datorn med [az vm show](/cli/azure/vm#az_vm_show). Använd den `--query` flagga för att extrahera URI som OS-disken. I följande exempel hämtas diskinformation för den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

URI: N liknar **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Ta bort befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är bara metadata som definierar storleken eller platsen och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverkskort (NIC). Varje virtuell hårddisk har ett lån när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att associera operativsystemdisken med en virtuell dator, även om den virtuella datorn är i stoppat eller frigjort läge.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kan du koppla den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

Ta bort den virtuella datorn med [az vm ta bort](/cli/azure/vm#az_vm_delete). I följande exempel tar bort den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste frigöras innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Bifoga befintlig virtuell hårddisk till en annan virtuell dator
De efterföljande stegen använder du en annan virtuell dator för felsökning. Du kan koppla den befintliga virtuella hårddisken till den här virtuella datorn för felsökning Bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program eller systemloggfilerna, till exempel. Välj eller skapa en annan virtuell dator för felsökning.

Bifoga den befintliga virtuella hårddisken med [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). När du ansluter den befintliga virtuella hårddisken, ange URI: N på disken som hämtades i föregående `az vm show` kommando. Följande exempel ansluter en befintlig virtuell hårddisk för felsökning VM med namnet `myVMRecovery` i resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Montera ansluten datadisk

> [!NOTE]
> I följande exempel förklarar vi de steg som krävs på en Ubuntu-VM. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise Linux- eller SUSE, loggen filplatser och `mount` kommandon kan vara lite annorlunda. I dokumentationen för din distribution som är specifika för ändringarna som behövs i kommandon.

1. SSH till den felsökning virtuella datorn med rätt autentiseringsuppgifter. Om den här disken är den första disken kopplade till Virtuellt felsökningsdatorn, disken sannolikt är ansluten till `/dev/sdc`. Använd `dmseg` att visa anslutna diskar:

    ```bash
    dmesg | grep SCSI
    ```

    Utdata ser ut ungefär så här:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    I föregående exempel är OS-disken på `/dev/sda` och den temporära disken som angetts för varje virtuell dator är på `/dev/sdb`. Om du har flera datadiskar, de ska vara på `/dev/sdd`, `/dev/sde`och så vidare.

2. Skapa en katalog för att montera den befintliga virtuella hårddisken. I följande exempel skapas en katalog med namnet `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Om du har flera partitioner på dina befintliga virtuella hårddisken kan du montera nödvändiga partitionen. I följande exempel monterar den första primära partitionen på `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Det är bra att montera datadiskar på virtuella datorer i Azure med hjälp av universell unik identifierare (UUID) för den virtuella hårddisken. För den här korta felsöknings är montera den virtuella hårddisken med hjälp av UUID inte nödvändigt. Men under normal användning, redigering `/etc/fstab` för att montera virtuella hårddiskar med hjälp av enhetens namn i stället för UUID kanske inte kan starta den virtuella datorn.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monteras, kan du nu utföra underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
När din fel har åtgärdats du demontera och koppla från den befintliga virtuella hårddisken från Virtuellt felsökningsdatorn. Du kan inte använda den virtuella hårddisken med andra virtuella datorer tills frigörs lånet som kopplar den virtuella hårddisken till Virtuellt felsökningsdatorn.

1. Demontera den befintliga virtuella hårddisken från SSH-sessionen till Virtuellt felsökningsdatorn. Byta ut från den överordnade katalogen för din monteringspunkt börjar:

    ```bash
    cd /
    ```

    Nu demontera den befintliga virtuella hårddisken. I följande exempel demonteras enheten på `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nu ska du koppla från den virtuella hårddisken från den virtuella datorn. Avsluta SSH-sessionen till Virtuellt felsökningsdatorn. Lista de anslutna diskarna till Virtuellt felsökningsdatorn med [az vm unmanaged-disk lista](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). I följande exempel visas de diskar som är anslutna till den virtuella datorn med namnet `myVMRecovery` i resursgruppen med namnet `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Notera namnet för din befintliga virtuella hårddisken. Till exempel namnet på en disk med URI: N för **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** är **myVHD**. 

    Koppla från datadisk från den virtuella datorn [az vm unmanaged-disk koppla från](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). I följande exempel kopplar från disken med namnet `myVHD` från den virtuella datorn med namnet `myVMRecovery` i den `myResourceGroup` resursgrupp:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Du kan skapa en virtuell dator från den ursprungliga virtuella hårddisken med [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Faktiska JSON-mallen finns på följande länk:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Mallen distribuerar en virtuell dator med VHD-URI från det tidigare kommandot. Distribuera mallen med [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create). Ange URI: N till den ursprungliga virtuella Hårddisken och sedan ange OS-typ, storlek och namn på virtuell dator på följande sätt:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostiken
När du skapar den virtuella datorn från den befintliga virtuella hårddisken kan startdiagnostik inte automatiskt att aktivera. Aktivera startdiagnostik med [az vm boot-diagnostics aktivera](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). I följande exempel aktiveras diagnostiktillägget på den virtuella datorn med namnet `myDeployedVM` i resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda virtuella datorn.
2. [Skapa en hanterad disk ögonblicksbild](../linux/snapshot-copy-managed-disk.md) av OS-disken för den hanterade disken i virtuella datorn.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla hanterad disk som en datadisk på den virtuella datorn](../windows/attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [Felsök SSH-anslutningar till en Azure VM](troubleshoot-ssh-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en Linux VM](troubleshoot-app-connection.md).

