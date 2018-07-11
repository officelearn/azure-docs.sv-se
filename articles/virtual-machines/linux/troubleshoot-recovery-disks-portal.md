---
title: Använda en Linux felsöknings-VM i Azure portal | Microsoft Docs
description: Lär dig hur du felsöker problem med Linux virtuell dator genom att ansluta operativsystemdisken till en virtuell dator med Azure-portalen för återställning
services: virtual-machines-linux
documentationCenter: ''
authors: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: cynthn
ms.openlocfilehash: efa001a24be3fb646a2a10afe72cb9b4ebfbf836
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932021"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en Linux-VM genom att koppla OS-disken till en virtuell dator med Azure-portalen för återställning
Om din Linux-dator (VM) påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på den virtuella hårddisken. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab` som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure-portalen för att ansluta den virtuella hårddisken till en annan Linux VM att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn uppstår några problem, som de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan Linux-VM i felsökningssyfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Fastställa startproblem
Granska startdiagnostik och VM-skärmbild att avgöra varför den virtuella datorn är inte kan starta korrekt. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab`, eller en underliggande virtuell hårddisk som tagits bort eller flyttats.

Välj den virtuella datorn i portalen och rullar ned till den **stöd + felsökning** avsnittet. Klicka på **Startdiagnostik** att visa konsolmeddelanden som strömmas direkt från den virtuella datorn. Granska loggarna för konsolen för att se om du kan fastställa varför den virtuella datorn har uppstått ett problem. I följande exempel visas en virtuell dator som har fastnat i underhållsläge som kräver manuell interaktion:

![Visa VM datorloggar startdiagnostik](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Du kan också klicka på **skärmbild** överst på Start-diagnostikloggen för att hämta en avbildning av VM-skärmbild.


## <a name="view-existing-virtual-hard-disk-details"></a>Se information om en befintlig virtuell hårddisk
Innan du kan koppla den virtuella hårddisken till en annan virtuell dator, måste du identifiera namnet på den virtuella hårddisken (VHD). 

Välj en resursgrupp från portalen och välj sedan ditt lagringskonto. Klicka på **Blobar**, som i följande exempel:

![Välj storage-blobbar](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Du har vanligtvis en behållare med namnet **virtuella hårddiskar** som lagrar virtuella hårddiskar. Välj behållaren för att visa en lista över virtuella hårddiskar. Anteckna namnet på en virtuell Hårddisk (prefixet är vanligtvis namnet på den virtuella datorn):

![Identifiera virtuella Hårddisken i storage-behållare](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Välj din befintliga virtuella hårddisk i listan och kopiera URL: en för användning i följande steg:

![Kopiera URL: en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Ta bort befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är bara metadata som definierar storleken eller platsen och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverkskort (NIC). Varje virtuell hårddisk har ett lån när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att associera operativsystemdisken med en virtuell dator, även om den virtuella datorn är i stoppat eller frigjort läge.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kan du koppla den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

Välj den virtuella datorn i portalen och klicka sedan på **ta bort**:

![VM boot diagnostics skärmbild som visar fel vid start](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste frigöras innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Bifoga befintlig virtuell hårddisk till en annan virtuell dator
De efterföljande stegen använder du en annan virtuell dator för felsökning. Du kan koppla den befintliga virtuella hårddisken till den här virtuella datorn för felsökning kan bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program eller systemloggfilerna, till exempel. Välj eller skapa en annan virtuell dator för felsökning.

1. Välj en resursgrupp från portalen och välj sedan Virtuellt felsökningsdatorn. Välj **diskar** och klicka sedan på **bifoga befintlig**:

    ![Bifoga befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Välj din befintliga virtuella hårddisk genom att klicka på **VHD-fil**:

    ![Bläddra till den befintliga virtuella hårddisken](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Välj ditt lagringskonto och en behållare och sedan på en befintlig virtuell Hårddisk. Klicka på den **Välj** för att bekräfta ditt val:

    ![Välj din befintliga virtuella hårddisk](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Med en virtuell Hårddisk nu markerade, klickar du på **OK** att bifoga den befintliga virtuella hårddisken:

    ![Bekräfta att koppla en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Efter några sekunder i **diskar** fönstret för den virtuella datorn visas din befintliga virtuella hårddisk ansluten som en datadisk:

    ![Befintlig virtuell hårddisk ansluten som en datadisk](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montera ansluten datadisk

> [!NOTE]
> I följande exempel förklarar vi de steg som krävs på en Ubuntu-VM. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise Linux- eller SUSE, loggen filplatser och `mount` kommandon kan vara lite annorlunda. I dokumentationen för din distribution som är specifika för ändringarna som behövs i kommandon.

1. SSH till den felsökning virtuella datorn med rätt autentiseringsuppgifter. Om den här disken är den första disken kopplade till Virtuellt felsökningsdatorn, det sannolikt är anslutet till `/dev/sdc`. Använd `dmseg` visa en lista över anslutna diskar:

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
Koppla bort den befintliga virtuella hårddisken från Virtuellt felsökningsdatorn när din fel har åtgärdats. Du kan inte använda den virtuella hårddisken med andra virtuella datorer tills frigörs lånet som kopplar den virtuella hårddisken till Virtuellt felsökningsdatorn.

1. Demontera den befintliga virtuella hårddisken från SSH-sessionen till Virtuellt felsökningsdatorn. Byta ut från den överordnade katalogen för din monteringspunkt börjar:

    ```bash
    cd /
    ```

    Nu demontera den befintliga virtuella hårddisken. I följande exempel demonteras enheten på `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nu ska du koppla från den virtuella hårddisken från den virtuella datorn. Välj den virtuella datorn i portalen och klicka på **diskar**. Välj den befintliga virtuella hårddisken och klicka sedan på **Detach**:

    ![Koppla från en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Vänta tills den virtuella datorn har har kopplats bort datadisken innan du fortsätter.

## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Du kan skapa en virtuell dator från den ursprungliga virtuella hårddisken med [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Mallen distribuerar en virtuell dator i ett befintligt virtuellt nätverk med hjälp av VHD-URL: en från det tidigare kommandot. Klicka på den **distribuera till Azure** knappen på följande sätt:

![Distribuera virtuell dator från mall från GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Mallen läses in i Azure-portalen för distribution. Ange namn för din nya virtuella datorn och befintliga Azure-resurser och klistra in Webbadressen till din befintliga virtuella hårddisk. Du börjar distributionen genom att klicka på **köp**:

![Distribuera virtuell dator från mall](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostiken
När du skapar den virtuella datorn från den befintliga virtuella hårddisken kan startdiagnostik inte automatiskt att aktivera. För att kontrollera status för startdiagnostik och aktivera vid behov, väljer du den virtuella datorn i portalen. Under **övervakning**, klickar du på **diagnostikinställningar**. Kontrollera statusen är **på**, och bock bredvid **Startdiagnostik** har valts. Om du gör några ändringar klickar du på **spara**:

![Uppdatera inställningarna för startdiagnostik](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda Managed Disk Windows virtuella datorn.
2. [Skapa en hanterad disk ögonblicksbild](../windows/snapshot-copy-managed-disk.md) av OS-disken för den hanterade disken i virtuella datorn.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla hanterad disk som en datadisk på den virtuella datorn](../windows/attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [Felsök SSH-anslutningar till en Azure VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en Linux VM](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Mer information om hur du använder Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
