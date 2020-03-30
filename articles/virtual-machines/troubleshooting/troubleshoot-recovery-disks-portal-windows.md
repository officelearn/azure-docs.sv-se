---
title: Använda en virtuell dator med Windows-felsökning i Azure-portalen | Microsoft-dokument
description: Lär dig hur du felsöker problem med virtuella Windows-datorer i Azure genom att ansluta OS-disken till en virtuell återställnings-dator med Azure-portalen
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250002"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings-DATOR med Azure-portalen
Om din virtuella Windows-dator (VM) i Azure stöter på ett start- eller diskfel kan du behöva utföra felsökningssteg på själva den virtuella hårddisken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar att den virtuella datorn kan starta. I den här artikeln beskrivs hur du använder Azure-portalen för att ansluta den virtuella hårddisken till en annan Windows-virtuell dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den berörda virtuella datorn.
1. Skapa en ögonblicksbild för OS-disken för den virtuella datorn.
1. Skapa en virtuell hårddisk från ögonblicksbilden.
1. Koppla och montera den virtuella hårddisken på en annan Virtuell Windows-dator för felsökning.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
1. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
1. Byt os-disken mot den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller inte för den virtuella datorn med ohanterat disk.

## <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicksbild av OS-disken
En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk (VHD). Vi rekommenderar att du stänger av den virtuella datorn på ett rent sätt innan du tar en ögonblicksbild för att rensa alla processer som pågår. Så här tar du en ögonblicksbild av en OS-disk:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **Virtuella datorer** i sidofältet och välj sedan den virtuella datorn som har problem.
1. Välj **Diskar**i den vänstra rutan och välj sedan namnet på OS-disken.
    ![Bild om namnet på OS-disken](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. På sidan **Översikt** på OS-disken och välj sedan **Skapa ögonblicksbild**.
1. Skapa en ögonblicksbild på samma plats som OS-disken.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicksbilden
Så här skapar du en disk från ögonblicksbilden:

1. Välj **Cloud Shell** från Azure-portalen.

    ![Bild om Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Kör följande PowerShell-kommandon för att skapa en hanterad disk från ögonblicksbilden. Du bör ersätta dessa exempelnamn med lämpliga namn.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Om kommandona har körts visas den nya disken i resursgruppen som du angav.

## <a name="attach-the-disk-to-another-vm"></a>Koppla disken till en annan virtuell dator
För de följande stegen använder du en annan virtuell dator för felsökning. När du har bifogat disken till den virtuella felsökningsdatorn kan du bläddra bland och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program- eller systemloggfiler. Så här ansluter du disken till en annan virtuell dator:

1. Välj resursgruppen från portalen och välj sedan den virtuella felsökningsdatorn. Välj **Diskar**, välj **Redigera**och klicka sedan på Lägg **till datadisk:**

    ![Koppla befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Välj OS-disken för den virtuella datorn som du identifierade i listan **Datadiskar.** Om os-disken inte visas kontrollerar du att felsökningen av den virtuella datorn och OS-disken finns i samma region (plats). 
3. Välj **Spara** om du vill tillämpa ändringarna.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montera den bifogade datadisken på den virtuella datorn

1. Öppna en anslutning till fjärrskrivbord till den virtuella felsökningsdatorn. 
2. Öppna **Serverhanteraren**i felsÃ¶kningen av den virtuella datorn och välj sedan **Fil- och lagringstjänster**. 

    ![Välj Fil- och lagringstjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datadisken identifieras och kopplas automatiskt. Om du vill visa en lista över de anslutna diskarna väljer du **Diskar**. Du kan välja datadisken om du vill visa volyminformation, inklusive enhetsbeteckningen. I följande exempel visas den bifogade datadisken och användning av **F:**:

    ![Diskansluten och volyminformation i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monterad kan du nu utföra alla underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Avmontera och koppla från den ursprungliga virtuella hårddisken
När dina fel har lösts kopplar du bort den befintliga virtuella hårddisken från den virtuella felsökningsdatorn. Du kan inte använda den virtuella hårddisken med någon annan virtuell dator förrän lånet som kopplar den virtuella hårddisken till felsökningsdatorn släpps.

1. Öppna **Serverhanteraren**från RDP-sessionen till den virtuella datorn och välj sedan **Fil- och lagringstjänster:**

    ![Välj Fil- och lagringstjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Välj **Diskar** och välj sedan datadisken. Högerklicka på datadisken och välj **Ta offline:**

    ![Ange datadisken som offline i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Nu koppla den virtuella hårddisken från den virtuella datorn. Välj din virtuella dator i Azure-portalen och klicka på **Diskar**. 
4. Välj **Redigera**, markera den OS-disk som du har bifogat och klicka sedan på **Koppla från:**

    ![Koppla från befintlig virtuell hårddisk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Vänta tills den virtuella datorn har kopplats bort datadisken innan du fortsätter.

## <a name="swap-the-os-disk-for-the-vm"></a>Byt os-disk för den virtuella datorn

Azure-portalen stöder nu ändring av OS-disken för den virtuella datorn. Det gör du genom att följa dessa steg:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **Virtuella datorer** i sidofältet och välj sedan den virtuella datorn som har problem.
1. Välj **Diskar**i den vänstra rutan och välj sedan **Byta OS-disk**.
        ![Avbildningen om Swap OS-disk i Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Välj den nya disken som du reparerade och skriv sedan namnet på den virtuella datorn för att bekräfta ändringen. Om du inte ser disken i listan väntar du 10 ~ 15 minuter efter att du har löst från disken från felsökningsdatorn. Kontrollera också att disken är på samma plats som den virtuella datorn.
1. Välj OK.

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn läser [du Felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med programanslutning på en Windows VM](troubleshoot-app-connection.md).

Mer information om hur du använder Resource Manager finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).


