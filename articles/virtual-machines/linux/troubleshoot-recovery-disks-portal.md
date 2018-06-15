---
title: Använda en Linux felsökning VM i Azure portal | Microsoft Docs
description: Lär dig hur du felsöker problem för Linux virtuella datorer genom att ansluta OS-disken till en VM som använder Azure portal för återställning
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: 89c4c5c986375177918f14417c6b5a9a24925908
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271750"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en Linux VM genom att koppla OS-disken till en VM som använder Azure portal för återställning
Om Linux-dator (VM) påträffar ett fel vid start- eller disk, kan du behöva utför felsökning på den virtuella hårddisken sig själv. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab` som förhindrar att den virtuella datorn kan starta korrekt. Den här artikeln beskriver hur du använder Azure-portalen för att ansluta den virtuella hårddisken till en annan Linux VM att åtgärda eventuella fel och återskapa den ursprungliga virtuella datorn.

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn får problem, hålla de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan Linux VM i felsökningssyfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med hjälp av den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Fastställa startproblem
Granska startdiagnostikinställningar och skärmbilden Virtuella att avgöra varför den virtuella datorn kan inte starta korrekt. Ett vanligt exempel är ett ogiltigt värde i `/etc/fstab`, eller en underliggande virtuell hårddisk som tagits bort eller flyttats.

Välj den virtuella datorn i portalen och rulla ned till den **stöd + felsökning** avsnitt. Klicka på **starta diagnostik** visa console-meddelanden som strömmas från den virtuella datorn. Granska loggarna för konsolen för att se om du kan fastställa varför den virtuella datorn har uppstått ett problem. I följande exempel visas en virtuell dator som fastnat i underhållsläge som kräver manuell interaktion:

![Visa VM konsolen startdiagnostikinställningar loggar](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Du kan också klicka på **skärmbild** längst upp i boot diagnostik loggen att hämta en avbildning av VM-skärmbilden.


## <a name="view-existing-virtual-hard-disk-details"></a>Visa information om befintlig virtuell hårddisk
Innan du kan koppla den virtuella hårddisken till en annan virtuell dator, måste du identifiera namnet på den virtuella hårddisken (VHD). 

Välj din resursgrupp från portalen och sedan ditt lagringskonto. Klicka på **Blobbar**, som i följande exempel:

![Välj storage-blobbar](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Du har vanligtvis en behållare med namnet **virtuella hårddiskar** som lagrar dina virtuella hårddiskar. Välj behållare för att visa en lista över virtuella hårddiskar. Notera namnet på den virtuella Hårddisken (prefixet är vanligtvis namnet på den virtuella datorn):

![Identifiera VHD i lagringsbehållaren](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Välj en befintlig virtuell hårddisk i listan och kopiera Webbadressen för användning i följande steg:

![Kopiera URL: en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Ta bort en befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är enbart metadata som definierar storlek eller plats, och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverksgränssnittskortet (NIC). Varje virtuell hårddisk har tilldelats när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att koppla OS-disken till en virtuell dator även om den virtuella datorn är i tillståndet stoppad och frigjord.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen sig själv. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kopplar du den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

Välj den virtuella datorn i portalen och klicka sedan på **ta bort**:

![VM-Start diagnostik skärmbild som visar start-fel](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste släppas innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Koppla en befintlig virtuell hårddisk till en annan virtuell dator
För nästa steg använder du en annan virtuell dator i felsökningssyfte. Du bifoga en befintlig virtuell hårddisk för den här felsökningsinformationen VM för att kunna bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella fel i programkonfigurationen eller granska ytterligare program eller system loggfiler, till exempel. Välj eller skapa en annan virtuell dator ska användas för felsökning.

1. Välj din resursgrupp från portalen och välj sedan den virtuella datorn med felsökning. Välj **diskar** och klicka sedan på **bifoga befintliga**:

    ![Bifoga den befintliga disken i portalen](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Välj din befintliga virtuella hårddisk genom att klicka på **VHD-fil**:

    ![Bläddra till den befintliga virtuella hårddisken](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Välj ditt lagringskonto och en behållare och sedan på den befintliga virtuella Hårddisken. Klicka på den **Välj** för att bekräfta valet:

    ![Välj din befintliga virtuella hårddisk](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Med den virtuella Hårddisken nu markerade, klickar du på **OK** att koppla en befintlig virtuell hårddisk:

    ![Bekräfta att bifoga en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Efter några sekunder den **diskar** fönstret för den virtuella datorn visas en befintlig virtuell hårddisk ansluten som en datadisk:

    ![Befintlig virtuell hårddisk ansluten som en datadisk](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Bifogade data disken

> [!NOTE]
> I följande exempel innehåller information om de steg som krävs på en Ubuntu VM. Om du använder en annan Linux distro, till exempel Red Hat Enterprise Linux eller SUSE, loggen filplatser och `mount` kommandon kan vara lite annorlunda. Finns i dokumentationen för din specifika distro för ändringarna i kommandona.

1. SSH till den felsökning virtuella datorn med rätt autentiseringsuppgifter. Om den här disken är den första datadisk som är kopplade till den virtuella datorn med felsökning, det förmodligen är anslutet till `/dev/sdc`. Använd `dmseg` att visa anslutna diskar:

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
Koppla bort den befintliga virtuella hårddisken från den virtuella datorn med felsökning när din fel har åtgärdats. Du kan inte använda din virtuella hårddisk med andra Virtuella förrän lånet bifoga den virtuella hårddisken till Virtuellt datorn felsökning släpps.

1. Demontera den befintliga virtuella hårddisken från SSH-session till den virtuella datorn med felsökning. Ändra utanför den överordnade katalogen för din monteringspunkt:

    ```bash
    cd /
    ```

    Nu demontera befintlig virtuell hårddisk. I följande exempel demonterar enheten på `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nu ska du koppla från den virtuella hårddisken från den virtuella datorn. Välj den virtuella datorn i portalen och klicka på **diskar**. Välj en befintlig virtuell hårddisk och klicka sedan på **Detach**:

    ![Koppla från en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Vänta tills den virtuella datorn har kopplats från datadisken innan du fortsätter.

## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Så här skapar du en virtuell dator från den ursprungliga virtuella hårddisken [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Mallen distribuerar en virtuell dator i ett befintligt virtuellt nätverk med hjälp av VHD-Webbadressen från tidigare kommandot. Klicka på den **till Azure** knappen på följande sätt:

![Distribuera virtuell dator från mall från GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Mallen läses in i Azure-portalen för distribution. Ange namn för din nya virtuella datorn och befintliga Azure-resurser och klistra in Webbadressen till en befintlig virtuell hårddisk. Om du vill starta distributionen, klickar du på **inköp**:

![Distribuera virtuell dator från mall](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostikinställningar
När du skapar den virtuella datorn från den befintliga virtuella hårddisken får startdiagnostikinställningar inte automatiskt aktiveras. Om du vill kontrollera status för startdiagnostikinställningar och aktivera vid behov, väljer du den virtuella datorn i portalen. Under **övervakning**, klickar du på **diagnostikinställningarna**. Se till att statusen är **på**, och kryssrutan bredvid **starta diagnostik** är markerad. Om du gör några ändringar klickar du på **spara**:

![Uppdatera startdiagnostikinställningar](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda hanteras Disk Windows VM.
2. [Skapa en ögonblicksbild för hanterade diskar](../windows/snapshot-copy-managed-disk.md) av OS-disken på VM: hanterade disken.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla den hantera disken som en datadisk för den virtuella datorn](../windows/attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn finns [felsökning av SSH-anslutningar till en Azure VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsökning av problem med nätverksanslutningen på en Linux-VM](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Mer information om hur du använder Resource Manager finns [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
