---
title: "Använda moderna lagring för säkerhetskopiering med Azure Backup-Server v2 | Microsoft Docs"
description: "Läs mer om de nya funktionerna i Azure Backup Server v2. Den här artikeln beskriver hur du uppgraderar din Backup Server-installation."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Lägga till lagringsenheter i Azure Backup Server v2

Azure Backup Server v2 levereras med System Center 2016 Data Protection Manager Modern Backup Storage. Moderna Backup Storage erbjuder lagringsbesparingar på 50 procent, säkerhetskopior som är tre gånger snabbare och effektivare lagring. Den erbjuder också arbetsbelastningen-medvetna lagring. 

> [!NOTE]
> Om du vill använda moderna Backup Storage, måste du köra Backup Server v2 på Windows Server 2016. Om du kör Backup Server v2 på en tidigare version av Windows Server Azure Backup Server kan inte utnyttja moderna Backup Storage. I stället skyddar arbetsbelastningar som med Backup Server v1. Mer information finns i säkerhetskopian serverversionen [skydd matrisen](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>Volymer i reservserver v2

Backup Server v2 accepterar lagringsvolymer. När du lägger till en volym formateras Backup Server volymen till flexibelt filsystem (ReFS), vilket kräver att moderna Backup Storage. Vi rekommenderar att du använder det här arbetsflödet, för att lägga till en volym, och expandera den senare om du behöver:

1.  Konfigurera säkerhetskopiering v2 på en virtuell dator.
2.  Skapa en volym på en virtuell disk i en lagringspool:
    1.  Lägg till en disk till en lagringspool och skapa en virtuell disk med enkel layout.
    2.  Lägg till ytterligare diskar och utöka den virtuella disken.
    3.  Skapa volymer på den virtuella disken.
3.  Lägg till volymerna Backup-servern.
4.  Konfigurera lagring av arbetsbelastning-medveten.

## <a name="create-a-volume-for-modern-backup-storage"></a>Skapa en volym för moderna Backup Storage

Med Backup Server v2 med volymer som disklagring kan hjälpa dig att behålla kontrollen över lagringen. En volym kan vara en enskild disk. Men om du vill utöka lagring i framtiden, skapa en volym från en disk som skapats med hjälp av lagringsutrymmen. Det kan hjälpa dig om du vill utöka volymen för lagring av säkerhetskopior. Det här avsnittet innehåller metodtips för att skapa en volym med de här inställningarna.

1. I Serverhanteraren väljer **fil- och lagringstjänster** > **volymer** > **lagringspooler**. Under **fysiska DISKAR**väljer **ny Lagringspool**. 

    ![Skapa en ny lagringspool](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. I den **uppgifter** listrutan, Välj **ny virtuell Disk**.

    ![Lägg till en virtuell disk](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Välj lagringspoolen och välj sedan **Lägg till fysisk Disk**.

    ![Lägg till en fysisk disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Välj den fysiska disken och välj sedan **Utöka virtuell Disk**.

    ![Utöka den virtuella disken](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Välj den virtuella disken och välj sedan **ny volym**.

    ![Skapa en ny volym](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. I den **Välj server och disk** dialogrutan Välj servern och den nya disken. Markera **nästa**.

    ![Välj server och disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Lägga till volymer till disk-säkerhetskopiering serverlagring

Lägga till en volym på servern för säkerhetskopiering, i den **Management** rutan Sök igenom lagring och välj sedan **Lägg till**. En lista över alla volymer som ska läggas till för säkerhetskopiering serverlagring visas. När tillgängliga volymerna har lagts till i listan över valda volymer, kan du ge dem ett eget namn som hjälper dig att hantera dem. Om du vill formatera volymerna i ReFS så Backup Server kan använda fördelarna med moderna Backup Storage, Välj **OK**.

![Lägg till tillgängliga volymer](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurera arbetsbelastning-medvetna lagring

Du kan välja de volymer som lagrar företrädesvis vissa typer av arbetsbelastningar med arbetsbelastning-medvetna lagring. Du kan exempelvis ange dyra volymer som stöder ett stort antal i/o-åtgärder per sekund (IOPS) för att lagra de arbetsbelastningar som kräver ofta, högvolyms-säkerhetskopieringar. Ett exempel är SQL Server med transaktionsloggar. Andra arbetsbelastningar som säkerhetskopieras mindre ofta som virtuella datorer, kan säkerhetskopieras till låg kostnad volymer.

### <a name="update-dpmdiskstorage"></a>Uppdatera DPMDiskStorage

Du kan ställa in arbetsbelastningen-medvetna lagring med hjälp av PowerShell-cmdlet Update-DPMDiskStorage som uppdaterar egenskaperna för en volym i lagringspoolen på en server med Data Protection Manager.

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Följande skärmbild visar cmdlet Update DPMDiskStorage i PowerShell-fönstret.

![Kommandot Update DPMDiskStorage i PowerShell-fönstret](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

De ändringar du gör med hjälp av PowerShell visas i administratörskonsolen för säkerhetskopiering Server.

![Diskar och volymer i administratörskonsolen](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Nästa steg
När du har installerat Backup Server lär du dig hur du förbereder din server eller börja skydda en arbetsbelastning.

- [Förbereda säkerhetskopiering serverarbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Använd Backup Server för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använd Backup Server för att säkerhetskopiera SQL Server](backup-azure-sql-mabs.md)

