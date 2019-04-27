---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770862"
---
Om din virtuella Azure-dator (VM) får start- eller diskproblem kan du behöva utföra felsökningssteg direkt på den virtuella hårddisken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar att den virtuella datorn startar korrekt. Den här artikeln beskriver hur du använder Azure Portal för att ansluta den virtuella hårddisken till en annan virtuell dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.


## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn som har problem, men behåll de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken på en annan virtuell dator för felsökning.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör verktyg för att åtgärda fel på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med hjälp av den ursprungliga virtuella hårddisken.

## <a name="delete-the-original-vm"></a>Ta bort den ursprungliga virtuella datorn
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är den virtuella disk där operativsystemet, program och konfigurationer lagras. Den virtuella datorn är bara metadata som definierar storleken eller platsen och som refererar till resurser, till exempel en virtuell hårddisk eller ett virtuellt nätverksgränssnittskort (NIC). Varje virtuell hårddisk tilldelas ett lån när disken kopplas till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att associera operativsystemdisken med en virtuell dator även om den virtuella datorn är i stoppat eller frigjort läge.

Det första steget när du återställer en virtuell dator är att ta bort den virtuella datorresursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort kan du koppla den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen. 

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Klicka på **Virtuella datorer (klassiska)** på menyn till vänster.
3. Välj den virtuella dator där problemet har uppstått, klicka på **Diskar** och identifiera namnet på den virtuella hårddisken. 
4. Välj den virtuella hårddisk som operativsystemet finns på och kontrollera **platsen** för att identifiera lagringskontot som innehåller den virtuella hårddisken. I följande exempel är strängen direkt före ”.blob.core.windows.net” namnet på lagringskontot.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Bild som visar den virtuella datorns plats](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Högerklicka på den virtuella datorn och välj sedan **Ta bort**. Kontrollera att diskarna inte är markerade när du tar bort den virtuella datorn.
6. Skapa en ny virtuell återställningsdator. Den här virtuella datorn måste finnas i samma region och resursgrupp (Cloud Services) som den virtuella dator som problemet finns på.
7. Välj den virtuella återställningsdatorn och välj sedan **Diskar** > **Koppla befintlig**.
8. Välj din befintliga virtuella hårddisk genom att klicka på **VHD-fil**:

    ![Bläddra till den befintliga virtuella hårddisken](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Välj lagringskontot &gt; VHD-containern &gt; den virtuella hårddisken och bekräfta valet genom att klicka på **Välj**.

    ![Välj din befintliga virtuella hårddisk](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. När du har valt den virtuella hårddisken väljer du **OK** för att koppla den befintliga virtuella hårddisken.
11. Efter några sekunder visas din befintliga virtuella hårddisk ansluten som en datadisk i fönstret **Diskar** för din virtuella dator:

    ![Befintlig virtuell hårddisk ansluten som en datadisk](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hårddisken
När den befintliga virtuella hårddisken har monterats kan du utföra underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
När eventuella fel har åtgärdats demonterar du och kopplar från den befintliga virtuella hårddisken från den virtuella felsökningsdatorn. Du kan inte använda den virtuella hårddisken med en annan virtuell dator förrän lånet som kopplar den virtuella hårddisken till den virtuella felsökningsdatorn har frisläppts.  

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj **Virtuella datorer (klassiska)** på menyn till vänster.
3. Leta upp den virtuella återställningsdatorn. Välj Diskar, högerklicka på disken och välj sedan **Koppla från**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Skapa en virtuell dator från den ursprungliga hårddisken

Du kan skapa en virtuell dator från den ursprungliga virtuella hårddisken med [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Längst upp till vänster i portalen, väljer **skapa en resurs** > **Compute** > **VM** > **från Galleriet**.
3. Välj **My disks** (Mina diskar) i avsnittet **Choose an Image** (Välj en avbildning) och välj sedan den ursprungliga virtuella hårddisken. Kontrollera platsinformationen. Det här är den region där den virtuella datorn måste distribueras. Klicka på Nästa.
4. I avsnittet **Konfiguration av virtuell dator** skriver du namnet på den virtuella datorn och väljer en storlek för den virtuella datorn.
