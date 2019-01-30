---
title: Använda en Windows felsöknings-VM i Azure portal | Microsoft Docs
description: Lär dig att felsöka problem med Windows virtuell dator i Azure genom att ansluta operativsystemdisken till en virtuell dator med Azure-portalen för återställning
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/013/2018
ms.author: genli
ms.openlocfilehash: 2c5fac377dfab4b4c85991dcb8f4e15f4e3cb61a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225945"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator med Azure-portalen för återställning
Om din Windows virtuell dator (VM) i Azure påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på den virtuella hårddisken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure-portalen för att ansluta den virtuella hårddisken till en annan virtuell Windows-dator att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn uppstår några problem, som de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan virtuell Windows-dator för felsökning.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med den ursprungliga virtuella hårddisken.

Vi kan nu använda Azure PowerShell ändra OS-disk för en virtuell dator för den virtuella datorn som använder hanterade diskar. Vi behöver inte längre ta bort och återskapa den virtuella datorn. Mer information finns i [felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator med Azure PowerShell för återställning](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Fastställa startproblem
Granska startdiagnostik VM skärmbild för att avgöra varför den virtuella datorn är inte kan starta korrekt. Ett vanligt exempel skulle vara en misslyckad programuppdatering eller en underliggande virtuell hårddisk som tagits bort eller flyttats.

Välj den virtuella datorn i portalen och rullar ned till den **stöd + felsökning** avsnittet. Klicka på **Startdiagnostik** att visa skärmbilden. Observera några felmeddelanden eller felkoder för att fastställa varför den virtuella datorn har uppstått ett problem. 

![Visa VM datorloggar startdiagnostik](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Du kan också klicka på **ladda ned skärmbild** att hämta en avbildning av VM-skärmbild.

## <a name="view-existing-virtual-hard-disk-details"></a>Se information om en befintlig virtuell hårddisk
Innan du kan koppla den virtuella hårddisken till en annan virtuell dator, måste du identifiera namnet på den virtuella hårddisken (VHD). 

Välj en resursgrupp från portalen och välj sedan ditt lagringskonto. Klicka på **Blobar**, som i följande exempel:

![Välj storage-blobbar](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

Du har vanligtvis en behållare med namnet **virtuella hårddiskar** som lagrar virtuella hårddiskar. Välj behållaren för att visa en lista över virtuella hårddiskar. Anteckna namnet på en virtuell Hårddisk (prefixet är vanligtvis namnet på den virtuella datorn):

![Identifiera virtuella Hårddisken i storage-behållare](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Välj din befintliga virtuella hårddisk i listan och kopiera URL: en för användning i följande steg:

![Kopiera URL: en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Ta bort befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är bara metadata som definierar storleken eller platsen och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverkskort (NIC). Varje virtuell hårddisk har ett lån när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att associera operativsystemdisken med en virtuell dator, även om den virtuella datorn är i stoppat eller frigjort läge.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kan du koppla den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

Välj den virtuella datorn i portalen och klicka sedan på **ta bort**:

![VM boot diagnostics skärmbild som visar fel vid start](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste frigöras innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Bifoga befintlig virtuell hårddisk till en annan virtuell dator
De efterföljande stegen använder du en annan virtuell dator för felsökning. Du kan koppla den befintliga virtuella hårddisken till den här virtuella datorn för felsökning kan bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program eller systemloggfilerna, till exempel. Välj eller skapa en annan virtuell dator för felsökning.

1. Välj en resursgrupp från portalen och välj sedan Virtuellt felsökningsdatorn. Välj **diskar** och klicka sedan på **bifoga befintlig**:

    ![Bifoga befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Välj din befintliga virtuella hårddisk genom att klicka på **VHD-fil**:

    ![Bläddra till den befintliga virtuella hårddisken](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Välj ditt lagringskonto och en behållare och sedan på en befintlig virtuell Hårddisk. Klicka på den **Välj** för att bekräfta ditt val:

    ![Välj din befintliga virtuella hårddisk](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Med en virtuell Hårddisk nu markerade, klickar du på **OK** att bifoga den befintliga virtuella hårddisken:

    ![Bekräfta att koppla en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Efter några sekunder i **diskar** fönstret för den virtuella datorn visas din befintliga virtuella hårddisk ansluten som en datadisk:

    ![Befintlig virtuell hårddisk ansluten som en datadisk](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montera ansluten datadisk

1. Öppna en fjärrskrivbordsanslutning till den virtuella datorn. Välj den virtuella datorn i portalen och klicka på **Connect**. Ladda ned och öppna filen RDP-anslutning. Ange dina autentiseringsuppgifter för att logga in på den virtuella datorn på följande sätt:

    ![Logga in på den virtuella datorn med hjälp av fjärrskrivbord](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Öppna **Serverhanteraren**och välj sedan **fil- och lagringstjänster**. 

    ![Välj fil- och lagringstjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datadisken har identifierats och anslutna automatiskt. Om du vill se en lista över de anslutna diskarna **diskar**. Du kan välja din datadisk för att visa volume information, inklusive enhetsbeteckningen. I följande exempel visas datadisken ansluten och använder **F:**:

    ![Disk som är ansluten och volyminformation i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monteras, kan du nu utföra underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
Koppla bort den befintliga virtuella hårddisken från Virtuellt felsökningsdatorn när din fel har åtgärdats. Du kan inte använda den virtuella hårddisken med andra virtuella datorer tills frigörs lånet som kopplar den virtuella hårddisken till Virtuellt felsökningsdatorn.

1. RDP-session till den virtuella datorn och öppna **Serverhanteraren**och välj sedan **fil- och lagringstjänster**:

    ![Välj fil- och lagringstjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Välj **diskar** och välj sedan din datadisk. Högerklicka på din datadisk och välj **koppla från**:

    ![Ange datadisken som offline i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Nu ska du koppla från den virtuella hårddisken från den virtuella datorn. Välj den virtuella datorn i Azure-portalen och klicka på **diskar**. Välj den befintliga virtuella hårddisken och klicka sedan på **Detach**:

    ![Koppla från en befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Vänta tills den virtuella datorn har har kopplats bort datadisken innan du fortsätter.

## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Du kan skapa en virtuell dator från den ursprungliga virtuella hårddisken med [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). Mallen distribuerar en virtuell dator till en befintlig eller ny virtuellt nätverk, med VHD-URL: en från det tidigare kommandot. Klicka på den **distribuera till Azure** knappen på följande sätt:

![Distribuera virtuell dator från mall från GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Mallen läses in i Azure-portalen för distribution. Ange namn för din nya virtuella datorn och befintliga Azure-resurser och klistra in Webbadressen till din befintliga virtuella hårddisk. Du börjar distributionen genom att klicka på **köp**:

![Distribuera virtuell dator från mall](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostiken
När du skapar den virtuella datorn från den befintliga virtuella hårddisken kan startdiagnostik inte automatiskt att aktivera. För att kontrollera status för startdiagnostik och aktivera vid behov, väljer du den virtuella datorn i portalen. Under **övervakning**, klickar du på **diagnostikinställningar**. Kontrollera statusen är **på**, och bock bredvid **Startdiagnostik** har valts. Om du gör några ändringar klickar du på **spara**:

![Uppdatera inställningarna för startdiagnostik](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en virtuell Windows-dator](troubleshoot-app-connection.md).

Mer information om hur du använder Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

