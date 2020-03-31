---
title: Använda modern lagring för säkerhetskopiering med Azure Backup Server
description: Lär dig mer om de nya funktionerna i Azure Backup Server. I den hÃ¤r artikeln beskrivs hur du uppgraderar installationen av Ã¤ringar på Ã¤ringar på kopieringsservern.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172384"
---
# <a name="add-storage-to-azure-backup-server"></a>Lägga till lagringsutrymme på Azure Backup Server

Azure Backup Server V2 och senare stöder modern säkerhetskopieringslagring som erbjuder lagringsbesparingar på 50 procent, säkerhetskopior som är tre gånger snabbare och effektivare lagring. Det erbjuder också arbetsbelastningsmedveten lagring.

> [!NOTE]
> Om du vill använda Modern Backup Storage måste du köra Backup Server V2 eller V3 på Windows Server 2016 eller V3 på Windows Server 2019.
> Om du kör Backup Server V2 på en tidigare version av Windows Server kan Azure Backup Server inte dra nytta av modern säkerhetskopieringslagring. I stället skyddar den arbetsbelastningar som med Backup Server V1. Mer information finns i [versionsskyddsmatrisen](backup-mabs-protection-matrix.md)för säkerhetskopieringsserver .
>
> För att uppnå förbättrade prestanda för säkerhetskopiering rekommenderar vi att du distribuerar MABS v3 med nivåindelningslagring på Windows Server 2019. Se DPM-artikeln "[Konfigurera MBS med nivåindelningslagring](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)" för steg för att konfigurera nivåindelad lagring.

## <a name="volumes-in-backup-server"></a>Volymer i Säkerhetskopieringsserver

Backup Server V2 eller senare accepterar lagringsvolymer. När du lägger till en volym formateras volymen till Resilient File System (ReFS), som modern säkerhetskopieringslagring kräver. Om du vill lägga till en volym och expandera den senare om du behöver föreslår vi att du använder det här arbetsflödet:

1. Konfigurera säkerhetskopieringsserver på en virtuell dator.
2. Skapa en volym på en virtuell disk i en lagringspool:
    1. Lägg till en disk i en lagringspool och skapa en virtuell disk med enkel layout.
    2. Lägg till ytterligare diskar och utöka den virtuella disken.
    3. Skapa volymer på den virtuella disken.
3. Lägg till volymerna i Säkerhetskopieringsservern.
4. Konfigurera arbetsbelastningsmedveten lagring.

## <a name="create-a-volume-for-modern-backup-storage"></a>Skapa en volym för modern säkerhetskopieringslagring

Om du använder Backup Server V2 eller senare med volymer som disklagring kan du behålla kontrollen över lagringen. En volym kan vara en enda disk. Men om du vill utöka lagringsutrymmet i framtiden skapar du en volym av en disk som skapats med hjälp av lagringsutrymmen. Detta kan hjälpa om du vill utöka volymen för säkerhetskopiering lagring. Det här avsnittet innehåller metodtips för att skapa en volym med den här inställningen.

1. I Serverhanteraren väljer du**Lagringspooler**för **fil- och lagringstjänster.** > **Volumes** >  Under **FYSISKA DISKAR**väljer du **Ny lagringspool**.

    ![Skapa en ny lagringspool](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Välj **Ny virtuell disk**i listrutan **UPPGIFTER** .

    ![Lägga till en virtuell disk](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Markera lagringspoolen och välj sedan **Lägg till fysisk disk**.

    ![Lägga till en fysisk disk](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Markera den fysiska disken och välj sedan **Utöka virtuell disk**.

    ![Utöka den virtuella disken](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Markera den virtuella disken och välj sedan **Ny volym**.

    ![Skapa en ny volym](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Välj servern och den nya disken i dialogrutan **Välj server och disk.** Välj sedan **Nästa**.

    ![Välj server och disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Lägga till volymer i disklagring av Säkerhetskopieringsserver

> [!NOTE]
>
> - Lägg bara till en disk i poolen för att hålla kolumnantalet till 1. Du kan sedan lägga till diskar efter behov efteråt.
> - Om du lägger till flera diskar i lagringspoolen samtidigt lagras antalet diskar som antalet kolumner. När fler diskar läggs till kan de bara vara en multipel av antalet kolumner.

Om du vill lägga till en volym i Säkerhetskopieringsservern skannar du om lagringsutrymmet i **hanteringsfönstret** och väljer sedan **Lägg till**. En lista över alla tillgängliga volymer som ska läggas till för lagring av säkerhetskopieringsserver visas. När tillgängliga volymer har lagts till i listan över valda volymer kan du ge dem ett eget namn som hjälper dig att hantera dem. Om du vill formatera dessa volymer till ReFS så att Säkerhetskopieringsserver kan använda fördelarna med modern säkerhetskopieringslagring väljer du **OK**.

![Lägg till tillgängliga volymer](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Konfigurera arbetsbelastningsmedveten lagring

Med arbetsbelastningsmedveten lagring kan du välja de volymer som med företrädesvis lagrar vissa typer av arbetsbelastningar. Du kan till exempel ange dyra volymer som stöder ett stort antal in-/utdataåtgärder per sekund (IOPS) för att lagra endast arbetsbelastningar som kräver frekventa säkerhetskopior med hög volym. Ett exempel är SQL Server med transaktionsloggar. Andra arbetsbelastningar som säkerhetskopieras mindre ofta, till exempel virtuella datorer, kan säkerhetskopieras till billiga volymer.

### <a name="update-dpmdiskstorage"></a>Uppdate-DPMDiskStorage

Du kan ställa in arbetsbelastningsmedveten lagring med hjälp av PowerShell cmdlet Update-DPMDiskStorage, som uppdaterar egenskaperna för en volym i lagringspoolen på en Azure Backup Server.

Syntax:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Följande skärmbild visar cmdleten Update-DPMDiskStorage i PowerShell-fönstret.

![Kommandot Update-DPMDiskStorage i PowerShell-fönstret](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

De ändringar du gör med PowerShell återspeglas i administratörskonsolen för säkerhetskopieringsserver.

![Diskar och volymer i administratörskonsolen](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrera äldre lagring till modern lagring av säkerhetskopiering

När du har uppgraderat till eller installerat Backup Server V2 och uppgraderat operativsystemet till Windows Server 2016 uppdaterar du skyddsgrupperna så att de använder modern säkerhetskopieringslagring. Som standard ändras inte skyddsgrupper. De fortsätter att fungera som de ursprungligen inrättades.

Uppdatering av skyddsgrupper för användning av Modern Backup Storage är valfritt. Om du vill uppdatera skyddsgruppen stoppar du skyddet av alla datakällor med hjälp av alternativet behåll data. Lägg sedan till datakällorna i en ny skyddsgrupp.

1. Välj funktionen **Skydd** i administratörskonsolen. Högerklicka på medlemmen i listan **Medlem i skyddsgrupp** och välj sedan **Stoppa skyddet för medlemmen**.

   ![Stoppa skyddet av medlemmar](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. I dialogrutan **Ta bort från grupp** granskar du det använda diskutrymmet och det lediga utrymmet för lagringspoolen. Standardinställningen är att lämna återställningspunkterna på disken och låta dem upphöra per deras associerade bevarandeprincip. Klicka på **OK**.

   Om du omedelbart vill returnera det använda diskutrymmet till den kostnadsfria lagringspoolen markerar du kryssrutan **Ta bort replik på disken** för att ta bort säkerhetskopierade data (och återställningspunkter) som är associerade med medlemmen.

   ![Dialogrutan Ta bort från grupp](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Skapa en skyddsgrupp som använder modern säkerhetskopieringslagring. Inkludera de oskyddade datakällorna.

## <a name="add-disks-to-increase-legacy-storage"></a>Lägga till diskar för att öka äldre lagring

Om du vill använda äldre lagring med Backup Server kan du behöva lägga till diskar för att öka äldre lagring.

Lägga till disklagring:

1. Välj Lägg**till**lägg till**för lagring av hanteringsdiskar** >  **Management** > i administratörskonsolen .

    ![Dialogrutan Lägg till disklagring](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. Välj **Lägg till diskar**i dialogrutan **Lägg till disklagring** .

3. I listan över tillgängliga diskar väljer du de diskar du vill lägga till, väljer **Lägg till**och väljer sedan **OK**.

## <a name="next-steps"></a>Nästa steg

När du har installerat Backup Server kan du läsa om hur du förbereder servern eller börjar skydda en arbetsbelastning.

- [Förbereda arbetsbelastningar för säkerhetskopieringsserver](backup-azure-microsoft-azure-backup.md)
- [Använda Säkerhetskopieringsserver för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använda Säkerhetskopieringsserver för säkerhetskopiering av SQL Server](backup-azure-sql-mabs.md)
