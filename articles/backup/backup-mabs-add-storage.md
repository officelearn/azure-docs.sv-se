---
title: Använda Modern Backup Storage med Azure Backup Server
description: Läs mer om de nya funktionerna i Azure Backup Server. Den här artikeln beskriver hur du uppgraderar Backup Server-installationen.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: markgal; adigan; kasinh
ms.openlocfilehash: daa7d6ee13cf55703b71bea321e65d2518a59979
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578526"
---
# <a name="add-storage-to-azure-backup-server"></a>Lägga till lagringsutrymme på Azure Backup Server

Azure Backup Server V2 och senare levereras med System Center 2016 Data Protection Manager Modern Backup Storage. Modern Backup Storage ger lagringsbesparingar på 50 procent, säkerhetskopieringar som är tre gånger snabbare och effektivare lagring. Den erbjuder även arbetsbelastningsmedveten lagring.

> [!NOTE]
> Om du vill använda Modern Backup Storage måste du köra Backup Server V2 eller V3 på Windows Server 2016 eller V3 på Windows Server 2019.
> Om du kör Backup Server V2 på en tidigare version av Windows Server, kan inte Azure Backup Server dra nytta av Modern Backup Storage. I stället skyddar arbetsbelastningar, vilket är fallet med Backup Server V1. Mer information finns i Backup Server-version [skyddsöversikt](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Volymer i Backup Server

Säkerhetskopiera servern V2 eller senare stöder lagringsvolymer. När du lägger till en volym, formateras volymen till flexibelt filsystem (ReFS), vilket kräver att Modern Backup Storage Backup Server. Lägga till en volym och expandera den senare om du behöver, föreslår vi att du använder det här arbetsflödet:

1.  Konfigurera Backup-Server på en virtuell dator.
2.  Skapa en volym på en virtuell disk i en lagringspool:
    1.  Lägg till en disk till en lagringspool och skapa en virtuell disk med enkel layout.
    2.  Lägg till ytterligare diskar och utöka den virtuella disken.
    3.  Skapa volymer på den virtuella disken.
3.  Lägg till volymerna i Backup Server.
4.  Konfigurera arbetsbelastningsmedveten lagring.

## <a name="create-a-volume-for-modern-backup-storage"></a>Skapa en volym för Modern Backup Storage

Använda Backup Server V2 eller senare med volymer som disklagring kan hjälpa dig att behålla kontrollen över lagringen. En volym kan vara en enskild disk. Om du vill utöka lagring i framtiden, skapa en volym från en disk som skapats med hjälp av lagringsutrymmen. Detta kan hjälpa om du vill utöka volymen för lagring av säkerhetskopior. Det här avsnittet innehåller metodtips för att skapa en volym med den här installationen.

1. I Serverhanteraren väljer **fil- och lagringstjänster** > **volymer** > **lagringspooler**. Under **fysiska DISKAR**väljer **ny Lagringspool**.

    ![Skapa en ny lagringspool](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. I den **uppgifter** listrutan, väljer **nya virtuella disken**.

    ![Lägg till en virtuell disk](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Välj lagringspoolen och välj sedan **Lägg till fysisk Disk**.

    ![Lägg till en fysisk disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Välj den fysiska disken och välj sedan **Utöka virtuell Disk**.

    ![Utöka den virtuella disken](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Välj den virtuella disken och välj sedan **ny volym**.

    ![Skapa en ny volym](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. I den **väljer du server och disk** dialogrutan Välj servern och den nya disken. Välj sedan **Nästa**.

    ![Välj server och disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Lägg till volymer Backup Server disklagring

Lägga till en volym på Backup-Server i den **Management** fönstret Genomsök igen lagringen och välj sedan **Lägg till**. En lista över alla volymer som är tillgängliga som ska läggas till för Backup Server-lagring visas. När tillgängliga volymer har lagts till i listan över valda volymer kan ge du dem ett eget namn som hjälper dig att hantera dem. Om du vill formatera volymerna till ReFS så att Backup Server kan använda fördelarna med Modern Backup Storage, Välj **OK**.

![Lägg till tillgängliga volymer](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurera arbetsbelastningsmedveten lagring

Du kan välja de volymer som prioritera lagrar av särskilda typer av arbetsbelastningar med arbetsbelastningsmedveten lagring. Du kan till exempel ange dyra volymer som stöder ett stort antal indata/utdataåtgärder per sekund (IOPS) för att lagra de arbetsbelastningar som kräver frekventa, stora volymer säkerhetskopieringar. Ett exempel är SQL Server med transaktionsloggar. Andra arbetsbelastningar som säkerhetskopieras mer sällan, t.ex. virtuella datorer, kan säkerhetskopieras till låg kostnad volymer.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Du kan konfigurera arbetsbelastningsmedveten lagring med hjälp av PowerShell-cmdlet Update-DPMDiskStorage, som uppdaterar egenskaperna för en volym i lagringspoolen på en server med Data Protection Manager.

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Följande skärmbild visar cmdlet: en Update-DPMDiskStorage i PowerShell-fönstret.

![Kommandot Update-DPMDiskStorage i PowerShell-fönstret](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

De ändringar du gör med hjälp av PowerShell avspeglas i administratörskonsolen för Backup Server.

![Diskar och volymer i administratörskonsolen](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrera äldre lagring till Modern Backup Storage
När du uppgraderar eller installerar Backup Server V2 och uppgradera operativsystemet till Windows Server 2016 kan du uppdatera dina skyddsgrupper för att använda Modern Backup Storage. Som standard ändras skyddsgrupperna inte. De kan fortsätta att fungera som de ursprungligen har ställts in.

Uppdatering av skyddsgrupperna för att använda Modern Backup Storage är valfritt. Stoppa skyddet av alla datakällor med hjälp av alternativet Behåll data för att uppdatera skyddsgruppen. Lägg sedan till datakällorna i en ny skyddsgrupp.

1. I administratörskonsolen, väljer den **Protection** funktionen. I den **Skyddsgruppsmedlem** listan, högerklickar du på medlemmen och välj sedan **stoppa skyddet av medlem**.

  ![Stoppa skyddet av medlem](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. I den **ta bort från grupp** dialogrutan granskar du använt diskutrymme och tillgängligt ledigt utrymme för lagringspoolen. Standardvärdet är att lämna återställningspunkterna på disken och låta dem upphöra per deras associerade bevarandeprincip. Klicka på **OK**.

  Om du vill returnera använt diskutrymme direkt till den lediga lagringspoolen, väljer du den **ta bort replik på disk** kryssrutan för att ta bort säkerhetskopierade data (och återställningspunkter) som är associerade med medlemmen.

  ![Ta bort från dialogrutan grupp](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Skapa en skyddsgrupp som använder Modern Backup Storage. Inkludera de oskyddade datakällorna.

## <a name="add-disks-to-increase-legacy-storage"></a>Lägg till diskar för att öka äldre lagring

Om du vill använda äldre lagring med Backup Server kan du behöva lägga till diskar för att öka äldre lagring.

Lägg till disklagring:

1. I administratörskonsolen, väljer **Management** > **disklagring** > **Lägg till**.

    ![Lägg till disklagring dialogrutan](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. I den **Lägg till disklagring** dialogrutan **lägga till diskar**.

5. Välj de diskar som du vill lägga till, Välj i listan över tillgängliga diskar, **Lägg till**, och välj sedan **OK**.

## <a name="next-steps"></a>Nästa steg
När du har installerat Backup Server lär du dig hur du förbereder din server eller börjar skydda en arbetsbelastning.

- [Förbereda Backup Server-arbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
