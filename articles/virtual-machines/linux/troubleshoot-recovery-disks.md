---
title: Använda en Linux felsökning av virtuell dator med Azure CLI 2.0 | Microsoft Docs
description: Lär dig hur du felsöker problem med Linux VM genom att ansluta OS-disken till en återställning VM som använder Azure CLI 2.0
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: bff31dafdf3263ec189f67da7de8fea6eb3d2662
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Felsöka en Linux VM genom att koppla OS-disken till återställning av en virtuell dator med Azure CLI 2.0
Om Linux-dator (VM) påträffar ett fel vid start- eller disk, kan du behöva utför felsökning på den virtuella hårddisken sig själv. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab` som förhindrar att den virtuella datorn kan starta korrekt. Den här artikeln beskriver hur du använder Azure CLI 2.0 för att ansluta den virtuella hårddisken till en annan Linux VM att åtgärda eventuella fel och återskapa den ursprungliga virtuella datorn. Du kan också utföra dessa steg med [Azure CLI 1.0](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn får problem, hålla de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan Linux VM i felsökningssyfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med hjälp av den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Om du vill utföra dessa åtgärder för felsökning, behöver du senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

Ersätt parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.


## <a name="determine-boot-issues"></a>Fastställa startproblem
Granska seriella utdata för att avgöra varför den virtuella datorn kan inte starta korrekt. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab`, eller den underliggande virtuella hårddisken som tagits bort eller flyttats.

Hämta Start loggar med [az vm-startdiagnostikinställningar get-Start-loggen](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). I följande exempel hämtas seriella utdata från den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Granska seriella utdata för att avgöra varför den virtuella datorn inte kan starta. Om seriella utdata är inte något indikerar måste du kan behöva Granska loggfilerna i `/var/log` när du har den virtuella hårddisken är ansluten till en felsökning virtuell dator.


## <a name="view-existing-virtual-hard-disk-details"></a>Visa information om befintlig virtuell hårddisk
Innan du kan koppla en virtuell hårddisk (VHD) till en annan virtuell dator, måste du identifiera URI för OS-disk. 

Visa information om den virtuella datorn med [az vm visa](/cli/azure/vm#az_vm_show). Använd den `--query` flagga för att extrahera URI till OS-disk. I följande exempel hämtas diskinformation för den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

URI: N liknar **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Ta bort en befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är enbart metadata som definierar storlek eller plats, och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverksgränssnittskortet (NIC). Varje virtuell hårddisk har tilldelats när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att koppla OS-disken till en virtuell dator även om den virtuella datorn är i tillståndet stoppad och frigjord.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen sig själv. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kopplar du den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

Ta bort den virtuella datorn med [az vm ta bort](/cli/azure/vm#az_vm_delete). I följande exempel tar bort den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste släppas innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Koppla en befintlig virtuell hårddisk till en annan virtuell dator
För nästa steg använder du en annan virtuell dator i felsökningssyfte. Du bifoga en befintlig virtuell hårddisk för den här felsökningsinformationen VM att bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella fel i programkonfigurationen eller granska ytterligare program eller system loggfiler, till exempel. Välj eller skapa en annan virtuell dator ska användas för felsökning.

Anslut den befintliga virtuella hårddisken med [az vm ohanterad disk bifoga](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). När du ansluter en befintlig virtuell hårddisk, ange URI: N på disken som hämtades i föregående `az vm show` kommando. I följande exempel bifogar en befintlig virtuell hårddisk till den Virtuella datorn med namnet felsökning `myVMRecovery` i resursgrupp med namnet `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Bifogade data disken

> [!NOTE]
> I följande exempel innehåller information om de steg som krävs på en Ubuntu VM. Om du använder en annan Linux distro, till exempel Red Hat Enterprise Linux eller SUSE, loggen filplatser och `mount` kommandon kan vara lite annorlunda. Finns i dokumentationen för din specifika distro för ändringarna i kommandona.

1. SSH till den felsökning virtuella datorn med rätt autentiseringsuppgifter. Om den här disken är den första datadisk som är kopplade till den virtuella datorn med felsökning, disken sannolikt är ansluten till `/dev/sdc`. Använd `dmseg` att visa anslutna diskar:

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

    I föregående exempel är OS-disken på `/dev/sda` och tillfällig disketten för varje virtuell dator är på `/dev/sdb`. Om du har flera datadiskar, bör de visas på `/dev/sdd`, `/dev/sde`och så vidare.

2. Skapa en katalog om du vill montera en befintlig virtuell hårddisk. I följande exempel skapas en katalog med namnet `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Om du har flera partitioner i en befintlig virtuell hårddisk kan du montera partitionen som krävs. I följande exempel monterar den första primära partitionen på `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Det är bra att montera hårddiskar på virtuella datorer i Azure med hjälp av universellt Unik identifierare (UUID) för den virtuella hårddisken. Den här korta felsökning scenariot är montera den virtuella hårddisken med hjälp av UUID inte nödvändigt. Men vid normal användning, redigering `/etc/fstab` för att montera den virtuella hårddiskar med enhetens namn i stället för UUID kanske den virtuella datorn inte startar.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problemen på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monteras, kan du nu utföra eventuella underhåll och felsökning efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
När din felen är löst kan du demontera och koppla från den befintliga virtuella hårddisken från den virtuella datorn med felsökning. Du kan inte använda din virtuella hårddisk med andra Virtuella förrän lånet bifoga den virtuella hårddisken till Virtuellt datorn felsökning släpps.

1. Demontera den befintliga virtuella hårddisken från SSH-session till den virtuella datorn med felsökning. Ändra utanför den överordnade katalogen för din monteringspunkt:

    ```bash
    cd /
    ```

    Nu demontera befintlig virtuell hårddisk. I följande exempel demonterar enheten på `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nu ska du koppla från den virtuella hårddisken från den virtuella datorn. Avsluta SSH-session till den virtuella datorn med felsökning. Visa bifogade datadiskar till din felsökning virtuell dator med [az vm ohanterad disk listan](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). I följande exempel visar datadiskar som är kopplade till den virtuella datorn med namnet `myVMRecovery` i resursgrupp med namnet `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Notera namnet för en befintlig virtuell hårddisk. Till exempel namnet på en disk med URI för **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** är **myVHD**. 

    Koppla från datadisk från den virtuella datorn [az vm ohanterad disk frånkoppling](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). I följande exempel tar bort disken med namnet `myVHD` från den virtuella datorn med namnet `myVMRecovery` i den `myResourceGroup` resursgrupp:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Så här skapar du en virtuell dator från den ursprungliga virtuella hårddisken [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Den faktiska JSON-mallen finns på följande länk:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

Mallen distribuerar en virtuell dator med hjälp av VHD-URI från tidigare kommandot. Distribuera mallen med [az distribution skapa](/cli/azure/group/deployment#az_group_deployment_create). Ange URI: N till den ursprungliga virtuella Hårddisken och ange typ av operativsystem, VM-storlek och namn på virtuell dator på följande sätt:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostikinställningar
När du skapar den virtuella datorn från den befintliga virtuella hårddisken får startdiagnostikinställningar inte automatiskt aktiveras. Aktivera startdiagnostikinställningar med [az vm-startdiagnostikinställningar aktivera](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). I följande exempel aktiveras diagnostiska tillägget på den virtuella datorn med namnet `myDeployedVM` i resursgrupp med namnet `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda hanteras Disk Windows VM.
2. [Skapa en ögonblicksbild för hanterade diskar](../windows/snapshot-copy-managed-disk.md) av OS-disken på VM: hanterade disken.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla den hantera disken som en datadisk för den virtuella datorn](../windows/attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn finns [felsökning av SSH-anslutningar till en Azure VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsökning av problem med nätverksanslutningen på en Linux-VM](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).