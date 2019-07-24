---
title: Använd en virtuell Windows-Felsökning i Azure Portal | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Windows-datorer i Azure genom att ansluta OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881163"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
Om din virtuella Windows-dator (VM) i Azure påträffar ett start-eller diskfel kan du behöva utföra fel söknings stegen på den virtuella hård disken. Ett vanligt exempel är en misslyckad program uppdatering som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure Portal för att ansluta din virtuella hård disk till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort de virtuella DATORerna som har problem och Behåll de virtuella hård diskarna.
2. Anslut och montera den virtuella hård disken till en annan virtuell Windows-dator i fel söknings syfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den ursprungliga virtuella hård disken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med den ursprungliga virtuella hård disken.

För den virtuella datorn som använder hanterad disk kan vi nu använda Azure PowerShell för att ändra OS-disk för en virtuell dator. Vi behöver inte längre ta bort och återskapa den virtuella datorn. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure PowerShell](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Den här artikeln gäller inte för den virtuella datorn med en ohanterad disk.

## <a name="determine-boot-issues"></a>Fastställa start problem
Du kan ta reda på varför den virtuella datorn inte kan starta korrekt genom att granska skärm bilden Boot Diagnostics VM. Ett vanligt exempel är en misslyckad program uppdatering eller en underliggande virtuell hård disk som tas bort eller flyttas.

Välj din virtuella dator i portalen och rulla ned till avsnittet **support och fel sökning** . Klicka på **Starta diagnostik** för att Visa skärm bilden. Observera eventuella fel meddelanden eller felkoder som kan hjälpa dig att avgöra varför den virtuella datorn påträffar ett problem.

![Visa loggfiler för VM Boot Diagnostic-konsolen](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Du kan också klicka på **Hämta skärm bild** för att ladda ned en bild av skärm bilden för den virtuella datorn.

## <a name="view-existing-virtual-hard-disk-details"></a>Visa befintlig virtuell hård disk information
Innan du kan koppla en virtuell hård disk till en annan virtuell dator måste du identifiera namnet på den virtuella hård disken (VHD).

Välj den virtuella dator som har problem och välj sedan **diskar**. Anteckna namnet på OS-disken som i följande exempel:

![Välj Storage-blobbar](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Ta bort befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hård disk är den plats där själva operativ systemet, program och konfigurationer lagras. Den virtuella datorn är bara metadata som definierar storleken eller platsen och refererar till resurser, till exempel en virtuell hård disk eller ett virtuellt nätverks gränssnitts kort (NIC). Varje virtuell hård disk tilldelas ett lån när det kopplas till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att koppla OS-disken till en virtuell dator även om den virtuella datorn är i ett stoppat och friallokerat tillstånd.

Det första steget för att återställa den virtuella datorn är att ta bort den virtuella dator resursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort kopplar du den virtuella hård disken till en annan virtuell dator för att felsöka och lösa felen.

Välj den virtuella datorn i portalen och klicka sedan på **ta bort**:

![Skärm bild av startdiagnostik för virtuell dator visas vid start fel](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Vänta tills den virtuella datorn har tagits bort innan du ansluter den virtuella hård disken till en annan virtuell dator. Lånet på den virtuella hård disken som associeras med den virtuella datorn måste släppas innan du kan koppla den virtuella hård disken till en annan virtuell dator.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Koppla en befintlig virtuell hård disk till en annan virtuell dator
För kommande steg använder du en annan virtuell dator i fel söknings syfte. Du ansluter den befintliga virtuella hård disken till den här felsöka virtuella datorn för att kunna bläddra och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurations fel eller granska ytterligare program-eller systemloggfiler, till exempel. Välj eller skapa en annan virtuell dator som ska användas i fel söknings syfte.

1. Välj din resurs grupp i portalen och välj sedan din fel söknings dator. Välj **diskar**, Välj **Redigera**och klicka sedan på **Lägg till data disk**:

    ![Bifoga befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. I listan **data diskar** väljer du OS-disken för den virtuella dator som du har identifierat. Om du inte ser OS-disken måste du kontrol lera att den virtuella datorn felsöks och att operativ system disken finns i samma region (plats).
3. Välj **Spara** för att tillämpa ändringarna.

## <a name="mount-the-attached-data-disk"></a>Montera den anslutna data disken

1. Öppna en fjärr skrivbords anslutning till den virtuella datorn. Välj din virtuella dator i portalen och klicka på **Anslut**. Ladda ned och öppna RDP-anslutningssträngen. Ange dina autentiseringsuppgifter för att logga in på den virtuella datorn på följande sätt:

    ![Logga in på den virtuella datorn med fjärr skrivbord](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Öppna **Serverhanteraren**och välj **fil-och lagrings tjänster**. 

    ![Välj fil-och lagrings tjänster inom Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Data disken identifieras och bifogas automatiskt. Om du vill se en lista över de anslutna diskarna väljer du **diskar**. Du kan välja din datadisk om du vill visa volym information, inklusive enhets beteckningen. I följande exempel visas data disken som är ansluten och med hjälp av **F:**

    ![Disk ansluten och volym information i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hård disken
Med den befintliga virtuella hård disken monterad kan du nu utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hård disken
När dina fel har åtgärd ATS kopplar du från den befintliga virtuella hård disken från den virtuella fel söknings datorn. Du kan inte använda den virtuella hård disken med någon annan virtuell dator förrän lånet som ansluter till den virtuella hård disken till den virtuella fel söknings datorn har släppts.

1. Från RDP-sessionen till den virtuella datorn öppnar du **Serverhanteraren**och väljer **fil-och lagrings tjänster**:

    ![Välj fil-och lagrings tjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Välj **diskar** och välj sedan din datadisk. Högerklicka på din data disk och välj **ta offline**:

    ![Ange data disken som offline i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Koppla nu från den virtuella hård disken från den virtuella datorn. Välj den virtuella datorn i Azure Portal och klicka på **diskar**. 
4. Välj **Redigera**, Välj den OS-disk som du har anslutit och klicka sedan på **Koppla från**:

    ![Koppla från befintlig virtuell hård disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Vänta tills den virtuella datorn har kopplats från data disken innan du fortsätter.

## <a name="create-vm-from-original-hard-disk"></a>Skapa en virtuell dator från den ursprungliga hård disken

### <a name="method-1-use-azure-resource-manager-template"></a>Metod 1 Använd Azure Resource Manager mall
Använd [den här Azure Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet)för att skapa en virtuell dator från den ursprungliga virtuella hård disken. Mallen distribuerar en virtuell dator till ett befintligt eller nytt virtuellt nätverk med hjälp av VHD-URL: en från det tidigare kommandot. Klicka på knappen **distribuera till Azure** enligt följande:

![Distribuera virtuell dator från mall från GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Mallen läses in i Azure Portal för distribution. Ange namnen på den nya virtuella datorn och befintliga Azure-resurser och klistra in webb adressen till din befintliga virtuella hård disk. Starta distributionen genom att klicka på **köp**:

![Distribuera virtuell dator från mall](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>Metod 2 Skapa en virtuell dator från disken

1. I Azure Portal väljer du din resurs grupp från portalen och letar sedan upp OS-disken. Du kan också söka efter disken med hjälp av disk namnet:

    ![Sök disk från Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Välj **Översikt**och välj sedan **Skapa virtuell dator**.
    ![Skapa en virtuell dator från en disk från Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. Följ guiden för att skapa den virtuella datorn.

## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostik
När du skapar en virtuell dator från den befintliga virtuella hård disken aktive ras kanske inte startdiagnostik automatiskt. Om du vill kontrol lera statusen för startdiagnostiken och aktivera vid behov väljer du den virtuella datorn i portalen. Under **övervakning**klickar du på **Inställningar för diagnostik**. Se till att statusen är **på**och att bock markeringen bredvid startdiagnostik är markerad. Om du gör några ändringar klickar du på **Spara**:

![Uppdatera inställningarna för startdiagnostik](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Windows-dator](troubleshoot-app-connection.md).

Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/resource-group-overview.md).

