---
title: Använd en virtuell Windows-Felsökning i Azure Portal | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Windows-datorer i Azure genom att ansluta OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
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
ms.openlocfilehash: 5b7fc4a120f5a4b513e1852fc6e2cf5ab68e9631
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101263"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
Om din virtuella Windows-dator (VM) i Azure påträffar ett start-eller diskfel kan du behöva utföra fel söknings stegen på den virtuella hård disken. Ett vanligt exempel är en misslyckad program uppdatering som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure Portal för att ansluta din virtuella hård disk till en annan virtuell Windows-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den virtuella datorn som påverkas.
1. Skapa en ögonblicks bild för den virtuella datorns OS-disk.
1. Skapa en virtuell hård disk från ögonblicks bilden.
1. Anslut och montera den virtuella hård disken till en annan virtuell Windows-dator i fel söknings syfte.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den ursprungliga virtuella hård disken.
1. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
1. Byt OS-disk för den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller inte för den virtuella datorn med en ohanterad disk.

## <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicks bild av OS-disken
En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk (VHD). Vi rekommenderar att du stänger den virtuella datorn i klartext innan du tar en ögonblicks bild, för att ta bort alla processer som pågår. Följ dessa steg om du vill ta en ögonblicks bild av en OS-disk:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **virtuella datorer** på sid panelen och välj sedan den virtuella dator som har problem.
1. Välj **diskar** i den vänstra rutan och välj sedan namnet på operativ system disken.
    ![Bild om namnet på OS-disken](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. På sidan **Översikt** på OS-disken och välj sedan **skapa ögonblicks bild** .
1. Skapa en ögonblicks bild på samma plats som OS-disken.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicks bilden
Följ dessa steg om du vill skapa en disk från ögonblicks bilden:

1. Välj **Cloud Shell** från Azure Portal.

    ![Bild om öppen Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Kör följande PowerShell-kommandon för att skapa en hanterad disk från ögonblicks bilden. Du bör ersätta dessa exempel namn med lämpliga namn.

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
3. Om kommandona körs utan problem visas den nya disken i den resurs grupp som du har angett.

## <a name="attach-the-disk-to-another-vm"></a>Ansluta disken till en annan virtuell dator
För kommande steg använder du en annan virtuell dator i fel söknings syfte. När du har kopplat disken till fel söknings datorn kan du bläddra och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurations fel eller granska ytterligare program-eller systemloggfiler. Följ dessa steg om du vill koppla disken till en annan virtuell dator:

1. Välj din resurs grupp i portalen och välj sedan din fel söknings dator. Välj **diskar** , Välj **Redigera** och klicka sedan på **Lägg till data disk** :

    ![Bifoga befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. I listan **data diskar** väljer du OS-disken för den virtuella dator som du har identifierat. Om du inte ser OS-disken måste du kontrol lera att den virtuella datorn felsöks och att operativ system disken finns i samma region (plats). 
3. Välj **Spara** för att tillämpa ändringarna.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Montera den anslutna data disken på den virtuella datorn

1. Öppna en fjärr skrivbords anslutning till den virtuella datorn för fel sökning. 
2. Öppna **Serverhanteraren** i Felsöka virtuell dator och välj fil- **och lagrings tjänster** . 

    ![Välj fil-och lagrings tjänster inom Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Data disken identifieras och bifogas automatiskt. Om du vill se en lista över de anslutna diskarna väljer du **diskar** . Du kan välja din datadisk om du vill visa volym information, inklusive enhets beteckningen. I följande exempel visas data disken som är ansluten och med hjälp av **F:**

    ![Disk ansluten och volym information i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hård disken
Med den befintliga virtuella hård disken monterad kan du nu utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hård disken
När dina fel har åtgärd ATS kopplar du från den befintliga virtuella hård disken från den virtuella fel söknings datorn. Du kan inte använda den virtuella hård disken med någon annan virtuell dator förrän lånet som ansluter till den virtuella hård disken till den virtuella fel söknings datorn har släppts.

1. Från RDP-sessionen till den virtuella datorn öppnar du **Serverhanteraren** och väljer **fil-och lagrings tjänster** :

    ![Välj fil-och lagrings tjänster i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Välj **diskar** och välj sedan din datadisk. Högerklicka på din data disk och välj **ta offline** :

    ![Ange data disken som offline i Serverhanteraren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Koppla nu från den virtuella hård disken från den virtuella datorn. Välj den virtuella datorn i Azure Portal och klicka på **diskar** . 
4. Välj **Redigera** , Välj den OS-disk som du har anslutit och klicka sedan på **ta bort** :

    ![Koppla från befintlig virtuell hård disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Vänta tills den virtuella datorn har tagits bort från den virtuella datorn i IE., koppla bort data disken innan du fortsätter.

## <a name="swap-the-os-disk-for-the-vm"></a>Byt OS-disk för den virtuella datorn

Azure Portal har nu stöd för att ändra den virtuella datorns OS-disk. Det gör du genom att följa dessa steg:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **virtuella datorer** på sid panelen och välj sedan den virtuella dator som har problem.
1. I det vänstra fönstret väljer du **diskar** och sedan **Växla OS-disk** .
        ![Avbildningen om växling av OS-disk i Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Välj den nya disken som du reparerat och skriv sedan namnet på den virtuella datorn för att bekräfta ändringen. Om du inte ser disken i listan väntar du 10 ~ 15 minuter efter att du kopplar bort disken från den virtuella fel söknings datorn. Kontrol lera också att disken finns på samma plats som den virtuella datorn.
1. Välj OK.

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Windows-dator](troubleshoot-app-connection.md).

Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).


