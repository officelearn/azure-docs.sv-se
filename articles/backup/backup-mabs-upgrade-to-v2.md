---
title: Installera Azure Backup Server v2
description: Azure Backup Server v2 får du förbättrade funktioner som säkerhetskopiering för att skydda virtuella datorer, filer och mappar, arbetsbelastningar och mycket mer. Lär dig mer om att installera eller uppgradera till Azure Backup Server v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: a458a46f3775a593f369d5acb967fc90d61efde8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628349"
---
# <a name="install-azure-backup-server-v2"></a>Installera Azure Backup Server v2

Azure Backup Server kan du skydda dina virtuella datorer (VM), arbetsbelastningar, filer och mappar och mycket mer. Azure Backup Server v2 bygger på Azure Backup Server v1 och ger dig nya funktioner som inte är tillgängliga i v1. En jämförelse av v1 och v2-funktionerna i den [skyddsöversikt för Azure Backup Server](backup-mabs-protection-matrix.md). 

Ytterligare funktioner i v2 Backup Server är en uppgradering från Backup Server v1. Backup Server v1 är dock inte ett krav för att installera Backup Server v2. Om du vill uppgradera från Backup Server v1 till v2 Backup Server installerar du Backup Server v2 på servern för Backup Server-skydd. Dina befintliga Backup Server-inställningar förblir intakta.

Du kan installera Backup Server v2 på Windows Server 2016 eller Windows Server 2012 R2. Om du vill dra nytta av nya funktioner som System Center 2016 Data Protection Manager Modern Backup Storage, måste du installera Backup Server v2 på Windows Server 2016. Innan du uppgraderar till eller installera Backup Server v2, Läs om de [installationskrav](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure Backup Server har samma kod som System Center Data Protection Manager. Backup Server v1 motsvarar Data Protection Manager 2012 R2. Backup Server v2 motsvarar Data Protection Manager 2016. Den här artikeln är ibland refererar till Data Protection Manager-dokumentationen.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Uppgradera Backup Server till v2
Uppgradera från Backup Server v1 till v2 Backup Server kan du kontrollera att installationen har de nödvändiga uppdateringarna:

- [Uppdatera skyddsagenter](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) på skyddade servrar.
- Uppgradera Windows Server 2012 R2 till Windows Server 2016.
- Uppgradera Azure Backup Server-fjärradministratör på alla produktionsservrar.
- Se till att säkerhetskopieringarna är inställda på att fortsätta utan att starta om produktionsservern.


### <a name="upgrade-steps-for-backup-server-v2"></a>Uppgraderingssteg för Backup Server v2

1. I Download Center [ladda ned installationsprogrammet till uppgradera](https://go.microsoft.com/fwlink/?LinkId=626082).

2. När du har extraherat installationsguiden, se till att **köra setup.exe** är markerat och välj sedan **Slutför**.

  ![Konfigurera installer - körningen](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. I guiden Microsoft Azure Backup Server under **installera**väljer **Microsoft Azure Backup Server**.

  ![Konfigurera installer - Välj installera](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. På den **Välkommen** granskar varningar, och välj sedan **nästa**.

  ![Konfigurera installer - välkomstsidan](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Installationsguiden utför nödvändiga kontroller för att se till att uppgradera din miljö. På den **Kravkontroller** väljer **Kontrollera**.

  ![Konfigurera installer - Kravkontroller sidan](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Din miljö måste klara de nödvändiga kontrollerna. Om din miljö inte skickar kontrollerna, Tänk på och åtgärda dem. Välj **kontrollen igen**. När du skickar de nödvändiga kontrollerna, väljer **nästa**.

  ![Konfigurera installer - kontrollen igen knappen](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. På den **SQL-inställningar** , väljer du lämpligt alternativ för din SQL-installation och välj sedan **kontrollera och installera**.

  ![Konfigurera installer - sidan SQL-inställningar](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Kontrollerna kan ta några minuter. När kontrollerna är klar väljer du **nästa**.

  ![Konfigurera installer - SQL-inställningar-kontrollera och installera knapp](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. På den **installationsinställningar** sidan, göra önskade ändringar till den plats där Backup Server är installerat eller till tillfällig plats. Välj **Nästa**.

  ![Konfigurera installer - installationsinställningssidan](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Om du vill avsluta installationsguiden väljer **Slutför**.

  ![Installationsprogram – Slutför](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Lägga till lagring för Modern Backup Storage

Lägger till stöd för volymer för att förbättra effektiviteten för lagring av säkerhetskopior, säkerhetskopieringsserver v2. Backup Server v1 och v2 Backup Server stöd för diskar.

### <a name="add-volumes-and-disks"></a>Lägg till volymer och diskar

Om du kör säkerhetskopieringsserver v2 på Windows Server 2016 kan använda du volymer för att lagra säkerhetskopierade data. Volymer ger lagringsbesparingar och snabbare säkerhetskopieringar. Eftersom volymerna är nya för Backup Server, måste du lägga till dem. 

När du lägger till en volym Backup Server kan ge du volymen ett eget namn. Välj den **eget namn** kolumn på den volym som du vill att namnet. Du kan ändra namnet senare, om det behövs. Du kan också använda PowerShell för att lägga till eller ändra egna namn för volymer.

Lägga till en volym i administratörskonsolen:

1. I Azure Backup Server-administratörskonsolen, Välj **Management** > **disklagring** > **Lägg till**.

  ![Öppna guiden Lägg till disklagring](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  Guiden Lägg till disklagring öppnas.

2. På den **Lägg till disklagring** sidan den **tillgängliga volymer** väljer en volym, och välj sedan **Lägg till**.

3. I den **valda volymer** rutan, ange ett eget namn för volymen och välj sedan **OK**.

  ![Disklagring guiden Lägg till – Lägg till volym](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Om du vill lägga till en disk måste disken tillhöra en skyddsgrupp med äldre lagring. Du kan använda dessa diskar endast för dessa skyddsgrupper. Om Backup Server inte har källor som har äldre skydd, visas disken inte.

  Läs mer om att lägga till diskar i [lägga till diskar för att öka äldre lagring](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Du kan inte ge en disk ett eget namn.


### <a name="assign-workloads-to-volumes"></a>Tilldela arbetsbelastningar till volymer

I Backup-Server anger du vilka arbetsbelastningar som är tilldelade till vilka volymer. Du kan till exempel ange dyra volymer som stöder ett stort antal indata/utdataåtgärder per sekund (IOPS) för att lagra endast arbetsbelastningar som kräver frekventa, stora volymer säkerhetskopieringar. Ett exempel är SQL Server med transaktionsloggar.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Använd PowerShell-cmdlet för att uppdatera egenskaperna för en volym i lagringspoolen i Backup Server, **Update-DPMDiskStorage**.

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
```

Alla ändringar du gör med hjälp av PowerShell avspeglas i Användargränssnittet.


## <a name="protect-data-sources"></a>Skydda datakällor
Skapa en skyddsgrupp om du vill börja skydda datakällor. Följande steg Markera ändringar eller tillägg i guiden Ny Skyddsgrupp.

Skapa en skyddsgrupp:

1. I Backup Server-administratörskonsolen väljer **Protection**.

2. Välj på verktygsfliken **New**.

  Då öppnas guiden Skapa ny Skyddsgrupp.

  ![Guiden Skapa ny Skyddsgrupp](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. På sidan **Välkommen** klickar du på **Nästa**.

4. På den **Välj typ av Skyddsgrupp** väljer du typ av skyddsgrupp som du vill skapa och välj sedan **nästa**.

  ![Sidan Välj Skyddsgrupp](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. På den **Välj gruppmedlemmar** sidan den **tillgängliga medlemmar** rutan medlemmar med skydd som agenter visas. I det här exemplet väljer du volym D:\ och E:\, och lägga till dem i **valda medlemmar**. Välj **Nästa**.

  ![Sidan Välj medlemmar](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. På den **Välj Dataskyddsmetod** anger en **skyddsgruppnamn**, Välj skyddsmetod och välj sedan **nästa**. Om du vill ha kortvarigt skydd, väljer du den **Disk** metod för säkerhetskopiering.

  ![Välj Dataskyddsmetod sida](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. På den **ange kortsiktiga mål** väljer du informationen för **Kvarhållningsintervall** och **Synkroniseringsfrekvens**. Välj sedan **Nästa**. Du kan också, om du vill ändra schemat för när återställningspunkter skapas, välja **ändra**.

  ![Ange kortvariga mål-sida](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. På den **granska Disklagringstilldelning** sidan Granska information om datakällor som du har valt, inklusive datakällans storlek, värden för utrymme som ska etableras och lagring målvolymen.

  ![Sidan för granska Disklagringstilldelning](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Lagringsvolymer baseras på arbetsbelastningens volymfördelning (anges med hjälp av PowerShell) och tillgängligt lagringsutrymme. Du kan ändra lagringsvolymer genom att välja andra volymer i den nedrullningsbara menyn. Om du ändrar värdet för **Mållagring**, värdet för **ledigt lagringsutrymme** dynamiskt ändras för att återspegla värden under **ledigt utrymme** och ** Underetablerat utrymme**.

  Om datakällorna växer som planerat värde för den **Underetablerat utrymme** kolumn i **ledigt lagringsutrymme** visar mängden ytterligare lagringsutrymme som behövs. Använd det här värdet för att planera lagringsbehoven för smidiga säkerhetskopieringar. Om värdet är noll, finns det inga potentiella problem med lagringen inom en snar framtid. Om värdet är ett annat tal än noll kan du inte har tillräckligt med lagringsutrymme som allokerats (baserat på din skyddsprincip och datastorleken på dina skyddade medlemmar).

  ![Undertilldelad disklagring](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  Slutför guiden för att slutföra skapandet av skyddsgruppen.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrera äldre lagring till Modern Backup Storage
När du uppgraderar eller installerar Backup Server v2 och sedan uppgradera operativsystemet till Windows Server 2016 kan du uppdatera dina skyddsgrupper för att använda Modern Backup Storage. Som standard ändras skyddsgrupperna inte. De kan fortsätta att fungera som de ursprungligen har ställts in. 

Uppdatering av skyddsgrupperna för att använda Modern Backup Storage är valfritt. Stoppa skyddet av alla datakällor med hjälp av alternativet Behåll data för att uppdatera skyddsgruppen. Lägg sedan till datakällorna i en ny skyddsgrupp.

1. I System Center 2016 DPM-administratörskonsolen, väljer du den **Protection** funktionen. I den **Skyddsgruppsmedlem** listan, högerklickar du på medlemmen och välj sedan **stoppa skyddet av medlem**.

  ![Stoppa skyddet av medlem](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. I den **ta bort från grupp** dialogrutan granskar du använt diskutrymme och tillgängligt ledigt utrymme för lagringspoolen. Standardvärdet är att lämna återställningspunkterna på disken och låta dem upphöra per deras associerade bevarandeprincip. Välj **OK**.

  Om du vill returnera använt diskutrymme direkt till den lediga lagringspoolen, väljer du den **ta bort replik på disk** kryssrutan för att ta bort säkerhetskopierade data (och återställningspunkter) som är associerade med medlemmen.

  ![Ta bort från dialogrutan grupp](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Skapa en skyddsgrupp som använder Modern Backup Storage. Inkludera de oskyddade datakällorna.


## <a name="add-disks-to-increase-legacy-storage"></a>Lägg till diskar för att öka äldre lagring

Om du vill använda äldre lagring med Backup Server kan du behöva lägga till diskar för att öka äldre lagring. 

Lägg till disklagring:

1. I System Center 2016 DPM-administratörskonsolen, väljer **Management** > **disklagring** > **Lägg till**.

  ![Lägg till disklagring dialogrutan](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. I den **Lägg till disklagring** dialogrutan **lägga till diskar**.

3. Välj de diskar som du vill lägga till i listan över tillgängliga diskar. Välj **Lägg till**, och välj sedan **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Uppdatera Data Protection Manager protection agent

Säkerhetskopieringsserver använder System Center Data Protection Manager protection-agenten för uppdateringar. Om du uppgraderar en skyddsagent som inte är ansluten till nätverket, kan du inte använda administratörskonsolen för Data Protection Manager för att slutföra en ansluten agent-uppgradering. Du måste uppgradera skyddsagenten i en inaktiv domänmiljö. Tills klientdatorn ansluts till nätverket, visar Data Protection Manager-administratörskonsolen att uppdateringen av skyddsagenten är väntande.

I följande avsnitt beskrivs hur du uppdaterar skyddsagenter för klientdatorer som är anslutna och för klientdatorer som inte är anslutna.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Uppdatera en skyddsagent för en ansluten klientdator

1. I Backup Server-administratörskonsolen väljer **Management** > **agenter**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.

  > [!NOTE]
  > Den **Agentuppdateringar** kolumnen visar när en uppdatering till skyddsagenten finns tillgänglig för varje skyddad dator. I den **åtgärder** fönstret den **uppdatering** åtgärden är tillgänglig endast när en skyddad dator är markerad och uppdateringar är tillgängliga.

3. Installerar uppdaterade skyddsagenter på de valda datorerna i den **åtgärder** väljer **uppdatering**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Uppdatera en skyddsagent på en klientdator som inte är ansluten

1. I Backup Server-administratörskonsolen väljer **Management** > **agenter**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.

  > [!NOTE]
  > Den **Agentuppdateringar** kolumnen visar när en uppdatering till skyddsagenten finns tillgänglig för varje skyddad dator. I den **åtgärder** fönstret den **uppdatering** instruktionen är inte tillgänglig när en skyddad dator markeras om det inte uppdateringar är tillgängliga.

3. Du installerar uppdaterade skyddsagenter på de valda datorerna genom att välja **uppdatering**.

4. För en klientdator som inte är ansluten till nätverket, tills datorn är ansluten till nätverket, den **Agentstatus** kolumnen visar statusen **väntande uppdatering**.

  När en klientdator är ansluten till nätverket, den **Agentuppdateringar** kolumn för klientdatorn visar statusen **uppdaterar**.
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>Flytta äldre skyddsgrupper från den gamla versionen och synkronisera den nya versionen med Azure

När både Azure Backup Server och Operativsystemet uppdateras är du redo att skydda nya datakällor med Modern Backup Storage. Datakällor som redan skyddas fortsätter att skyddas som de var i Azure Backup Server (äldre). Alla nya skyddsgrupper använda Modern Backup Storage.

Migrera datakällor från bakåtkompatibelt läge skydd till Modern Backup Storage:

1.  Lägga till nya volymer i lagringspoolen för Data Protection Manager. Du kan också tilldela ett eget namn och välja data källkod.

2. Stoppa skyddet av datakällor för varje datakälla som är i bakåtkompatibelt läge. Välj **Kvarhåll skyddade Data**.  Detta gör att återställning av den äldre återställningspunkter efter migreringen.

3. Skapa en ny skyddsgrupp. Välj de datakällor som du vill lagra med hjälp av det nya formatet.

  Data Protection Manager lagrar en kopia för replik från den äldre lagringen av säkerhetskopior i Modern Backup Storage volymen lokalt.
    > [!NOTE] 
    > Skapar kopian visas som en åtgärd efter återställning-jobb.

  Alla nya synkronisering och återställningspunkter lagras sedan i Modern Backup Storage.

  Gamla återställningspunkterna rensas ut när de går ut. Eftersom gamla återställningspunkter tas bort frigjort utrymme.

  När alla äldre volymer har tagits bort från den gamla lagringsenheter, kan du ta bort disken från Azure Backup. Du kan sedan ta bort disken från systemet.

4. Skapa en säkerhetskopia av Data Protection Manager-databasen.

  > [!IMPORTANT]
  > - Det nya servernamnet måste vara samma namn som den ursprungliga Azure Backup Server-instansen. Du kan inte ändra namnet på den nya Azure Backup Server-instansen om du vill använda tidigare lagringspoolen och Data Protection Manager-databasen för att behålla återställningspunkter.
  > - Du måste ha en säkerhetskopia av Data Protection Manager-databasen. Du måste återställa databasen.

5. Stäng av den ursprungliga Azure Backup Server-instansen eller koppla från servern.

6. Återställa datorkontot i Active Directory.

7. Installera Windows Server 2016 på en ny dator. Använda samma datornamn som den ursprungliga Azure Backup Server-instansen för server-namn.

8. Ansluta till domänen.

9. Installera Azure Backup Server v2. (Ta bort Data Protection Manager-lagringspooldiskar från den gamla servern och importera dem till den nya servern.)

10. Återställa Data Protection Manager-databasen som du skapade i steg 4.

11. Bifoga lagringen från den ursprungliga backup-servern till den nya servern.

12. Återställa Data Protection Manager-databasen i SQL Server.

13. På kommandoraden för administratör på den nya servern, använda `cd` att gå till Microsoft Azure Backup plats och bin installationsmappen.  

  Exempel:  
  C:\Windows\System32 > cd ”c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\ till Azure Backup

14. Kör `DPMSYNC -SYNC`.
  
  > [!NOTE]
  > Om du har lagt till *nya* diskar i lagringspoolen för Data Protection Manager i stället för att flytta gamla kör `DPMSYNC -Reallocatereplica`.

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Nya PowerShell-cmdlets i Backup Server v2

Två nya-cmdlets är tillgängliga när du installerar Azure Backup Server v2: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Nästa steg

Lär dig att förbereda din server eller börjar skydda en arbetsbelastning:
- [Förbereda Backup Server-arbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en VMware-server med hjälp av Backup Server](backup-azure-backup-server-vmware.md)
- [Säkerhetskopiera SQL Server med hjälp av Backup Server](backup-azure-sql-mabs.md)
- [Använda Modern Backup Storage med Backup Server](backup-mabs-add-storage.md)

