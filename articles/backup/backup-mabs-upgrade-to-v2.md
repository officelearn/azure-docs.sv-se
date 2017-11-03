---
title: Installera Azure Backup-Server v2 | Microsoft Docs
description: "Azure Backup-Server v2 ger förbättrad säkerhetskopiering för att skydda virtuella datorer, filer och mappar, arbetsbelastningar och mer. Lär dig mer om att installera eller uppgradera till Azure Backup Server v2."
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
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-backup-server-v2"></a>Installera Azure Backup-Server v2

Azure Backup-Server skyddar dina virtuella datorer (VM), arbetsbelastningar, filer och mappar och mer. Azure Backup Server v2 bygger på Azure Backup Server v1 och innehåller nya funktioner som inte är tillgängliga i v1. En jämförelse av funktioner mellan v1 och v2 finns [Azure Backup Server protection matrisen](backup-mabs-protection-matrix.md). 

Ytterligare funktioner i Backup Server v2 är en uppgradering från Backup Server v1. Backup Server v1 är dock inte ett krav för att installera Backup Server v2. Om du vill uppgradera från Backup Server v1 till Backup Server v2 installera Backup Server v2 på skydd av Backup-server. De befintliga inställningarna för säkerhetskopiering förblir intakta.

Du kan installera Backup Server v2 på Windows Server 2012 R2 eller Windows Server 2016. Om du vill dra nytta av nya funktioner som System Center 2016 Data Protection Manager Modern Backup Storage, måste du installera Backup Server v2 på Windows Server 2016. Innan du uppgraderar till eller installera Backup Server v2, Läs om de [installationskrav](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure Backup-Server har samma kodbas som System Center Data Protection Manager. Backup Server v1 motsvarar Data Protection Manager 2012 R2 och Backup Server v2 är likvärdig med Data Protection Manager 2016. Ibland refererar Data Protection Manager-dokumentationen till den här artikeln.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Uppgradera reservserver till v2
Uppgradera från Backup Server v1 till Backup Server v2, kontrollera att installationen har nödvändiga uppdateringar:

- [Uppdatera skyddsagenter](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent) på skyddade servrar.
- Uppgradera Windows Server 2012 R2 till Windows Server 2016.
- Uppgradera Azure Backup Server fjärradministratör på alla produktionsservrar.
- Se till att säkerhetskopieringen angetts fortsätta utan att starta om produktionsservern för.


### <a name="upgrade-steps-for-backup-server-v2"></a>Uppgradering för säkerhetskopiering v2

1. I Download Center [hämta uppgraderingen installationsprogrammet](https://go.microsoft.com/fwlink/?LinkId=626082).

2. När du har extraherat guiden kontrollerar du att **köra setup.exe** är markerad och välj sedan **Slutför**.

  ![Installationsprogram - konfigurationen](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. I guiden Microsoft Azure Backup Server under **installera**väljer **Microsoft Azure Backup Server**.

  ![Installationsprogram - väljer installera](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. På den **Välkommen** , läser du varningarna och väljer sedan **nästa**.

  ![Installationsprogram – välkomstsida](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. Installationsguiden genomför nödvändiga kontroller för att kontrollera att din miljö kan uppgradera. På den **nödvändiga kontrollerar** väljer **Kontrollera**.

  ![Installationsprogram - nödvändiga kontrollerar sida](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Din miljö måste klara de nödvändiga kontrollerna. Om din miljö inte klarar kontrollerna, Tänk på och åtgärda dem. Markera **kontrollen igen**. När du skickar de nödvändiga kontrollerna, Välj **nästa**.

  ![Installationsprogram - knappen Kontrollera igen](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. På den **SQL-inställningar** , väljer du lämpligt alternativ för SQL-installationen och väljer sedan **kontrollera och installera**.

  ![Installationsprogram - sidan SQL-inställningar](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Kontrollerna som kan ta några minuter. När kontrollen är klar väljer du **nästa**.

  ![Installationsprogram – kontrollera om SQL-inställningar och installera](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. På den **installationsinställningar** sidan, gör ändringar till den plats där säkerhetskopian Server är installerat eller till Scratch-plats. Välj **nästa**.

  ![Installationsprogram - installationsinställningssidan](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Om du vill avsluta installationsguiden genom att markera **Slutför**.

  ![Installationsprogram - Slutför](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Lägga till lagringsenheter till moderna Backup Storage

Lägger till stöd för volymer för att förbättra effektiviteten för säkerhetskopieringslagring reservserver v2. Som reservserver v1, v2 Backup Server har stöd för diskar.

### <a name="add-volumes-and-disks"></a>Lägg till volymer och diskar
Om du kör reservserver v2 på Windows Server 2016 kan du använda volymer för att lagra säkerhetskopierade data. Volymer ger lagringsbesparingar och snabbare säkerhetskopieringar. Eftersom volymer har använt Backup Server, måste du lägga till dem. 

När du lägger till en volym Backup Server, kan du ge volymen ett eget namn. Klicka på den **eget namn** kolumn på den volym som du vill namnge. Du kan ändra namnet senare, om det behövs. Du kan också använda PowerShell för att lägga till eller ändra eget namn för volymer.

Lägg till en volym i administratörskonsolen:

1. I Azure Backup Server administratörskonsolen, Välj **Management** > **disklagring** > **Lägg till**.

    ![Öppna guiden Lägg till disklagring](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Guiden Lägg till disklagring öppnas.

2. På den **lägger till disklagring** sidan den **tillgängliga volymer** rutan, Välj en volym och välj sedan **Lägg till**.
3. I den **valda volymer** , ange ett eget namn för volymen och välj sedan **OK**.

      ![Disklagring guiden Lägg till – Lägg till volym](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Om du vill lägga till en disk, måste disken tillhöra en skyddsgrupp som har äldre lagring. Diskarna kan endast användas för dessa skyddsgrupper. Om servern för säkerhetskopiering inte har datakällor som har äldre skydd, visas disken inte.

  Läs mer om att lägga till diskar i [att lägga till diskar för att öka äldre lagring](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Du kan inte ge en disk ett eget namn.


### <a name="assign-workloads-to-volumes"></a>Tilldela volymer arbetsbelastningar

I Backup Server anger du vilka arbetsbelastningar som är tilldelade till vilka volymer. Du kan exempelvis ange dyra volymer som stöder ett stort antal i/o-åtgärder per sekund (IOPS) för att lagra arbetsbelastningar som kräver ofta, högvolyms-säkerhetskopieringar. Ett exempel är SQL Server med transaktionsloggar.

#### <a name="update-dpmdiskstorage"></a>Uppdatera DPMDiskStorage

Uppdatera egenskaperna för en volym i lagringspoolen i Backup Server med PowerShell-cmdlet Update DPMDiskStorage.

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Alla ändringar som du gör med hjälp av PowerShell visas i Användargränssnittet.


## <a name="protect-data-sources"></a>Skydda datakällor
Om du vill börja skydda datakällorna måste du skapa en skyddsgrupp. Följande steg markerar ändringar eller tillägg i guiden Ny Skyddsgrupp.

Skapa en skyddsgrupp:

1. I konsolen för säkerhetskopiering serveradministratören väljer **skydd**.

2. Välj på verktygsfliken **ny**.

    Då öppnas guiden Skapa ny Skyddsgrupp.

  ![Guiden Skapa ny Skyddsgrupp](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. På den **Välkommen** väljer **nästa**.
4. På den **Välj typ av Skyddsgrupp** markerar du typ av skyddsgrupp som du vill skapa och välj sedan **nästa**.

  ![Sidan Välj Skyddsgruppen](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. På den **Välj gruppmedlemmar** sidan den **tillgängliga medlemmar** rutan, medlemmar med protection Agent finns. I det här exemplet väljer du volym D:\ och E:\ och lägga till dem i den **markerade medlemmar** fönstret. Välj **nästa**.

  ![Välj grupp för medlemmar sida](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. På den **Välj Dataskyddsmetod** anger en **skyddsgruppnamn**, Välj skyddsmetod och välj sedan **nästa**. Om du vill ha kortvarigt skydd, måste du välja den **Disk** metoden att säkerhetskopiera.

  ![Välj Dataskyddsmetod sida](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. På den **ange kortvariga mål** markerar du informationen för **Kvarhållningsintervall** och **Synkroniseringsfrekvens**. Markera **nästa**. Om du vill ändra schemat för när återställningspunkter tas, markera **ändra**.

  ![Ange kortvariga mål-sida](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. På den **granska Disklagringstilldelning** sidan Granska information om datakällorna som du har valt, deras storlek och värden för utrymme som ska etableras och lagring målvolymen.

  ![Granska Disklagringstilldelning sidan](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Lagringsvolymer baserat på arbetsbelastning volym fördelningen (anges med hjälp av PowerShell) och tillgängligt lagringsutrymme. Du kan ändra lagringsvolymer genom att välja andra volymer i den nedrullningsbara menyn. Om du ändrar du värdet för **Mållagringsenhet**, värdet för **ledigt lagringsutrymme** dynamiskt anpassas efter värden under **ledigt utrymme** och **Underprovisioned utrymme**.

  Om datakällorna växa som planerat värdet för den **Underprovisioned utrymme** kolumn i **ledigt lagringsutrymme** visar mängden ytterligare lagringsutrymme som behövs. Använd det här värdet för att planera dina lagringsbehov för smooth säkerhetskopieringar. Om värdet är noll, finns det inga problem med lagring inom en förutsägbar framtid. Om värdet är ett tal som inte är noll, du har inte tillräckligt lagringsutrymme som allokerats (baserat på din skyddsprincip och storleken på data av dina skyddade medlemmar).

  ![Underbelagd disklagring](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Slutför guiden Slutför skyddsgruppen.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrera lagring för äldre till moderna Backup Storage
När du uppgraderar eller installerar Backup Server v2 och uppgradera operativsystemet till Windows Server 2016, uppdatera dina skyddsgrupper för att använda moderna Backup Storage. Som standard ändras inte skyddsgrupper. De fortsätter att fungera som de ursprungligen har ställts in. 

Uppdatera skyddsgrupperna för att använda moderna Backup Storage är valfria. Avbryt skyddet av alla datakällor för att uppdatera skyddsgruppen med hjälp av alternativet behålla data. Lägg sedan till datakällorna i en ny skyddsgrupp.

1. I administratörskonsolen, Välj den **skydd** funktion. I den **Skyddsgruppsmedlem** högerklickar medlemmen, och välj sedan **stoppa skyddet av medlem**.

  ![Stoppa skyddet av medlem](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. I den **ta bort från gruppen** dialogrutan igenom diskutrymmet som används och det tillgängliga utrymmet för lagringspoolen. Standardvärdet är att lämna återställningspunkterna på disken och att de kan gälla per deras associerade bevarandeprincip. Klicka på **OK**.

  Om du vill returnera omedelbart diskutrymmet som används i ledigt lagringspoolen väljer du den **ta bort replik på disk** kryssrutan för att ta bort säkerhetskopieringsdata (och återställningspunkter) som är associerade med medlemmen.

  ![Ta bort från dialogrutan grupp](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Skapa en skyddsgrupp som använder Modern Backup Storage. Inkludera oskyddade datakällorna.


## <a name="add-disks-to-increase-legacy-storage"></a>Lägg till diskar för att öka äldre lagring

Om du vill använda gamla lagringsenheter med Backup Server kan du behöva lägga till diskar för att öka äldre lagring. 

Lägg till disklagring:

1. I administratörskonsolen, Välj **Management** > **disklagring** > **Lägg till**.

    ![Disklagring dialogrutan Lägg till](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. I den **lägger till disklagring** markerar **lägga till diskar**.

5. Välj de diskar som du vill lägga till, Välj i listan över tillgängliga diskar **Lägg till**, och välj sedan **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Uppdatera Data Protection Manager protection agent

Backup-servern använder System Center Data Protection Manager protection agent för uppdateringar. Om du uppgraderar en skyddsagent som inte är ansluten till nätverket, kan du inte använda Data Protection Manager-administratörskonsolen för att slutföra en ansluten agent-uppgradering. Du måste uppgradera skyddsagenten i en domänmiljö som inaktiva. Tills datorn är ansluten till nätverket, visar Data Protection Manager-administratörskonsolen att uppdateringen av skyddsagenten är i vänteläge.

I följande avsnitt beskrivs hur du uppdaterar skyddsagenter för klientdatorer som är anslutna och klientdatorer som inte är anslutna.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Uppdatera en skyddsagent för en ansluten klientdator

1. I konsolen för säkerhetskopiering serveradministratören väljer **Management** > **agenter**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.

  > [!NOTE]
  > Den **Agentuppdateringar** kolumnen visas när en uppdatering till skyddsagenten är tillgänglig för varje skyddad dator. I den **åtgärder** rutan i **uppdatering** åtgärden är tillgänglig endast när en skyddad dator är markerad och uppdateringar är tillgängliga.
  >
  >

3. Du installerar uppdaterade skyddsagenter på de valda datorerna i den **åtgärder** väljer **uppdatering**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Uppdatera en skyddsagent på en klientdator som inte är ansluten

1. I konsolen för säkerhetskopiering serveradministratören väljer **Management** > **agenter**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.

  > [!NOTE]
   > Den **Agentuppdateringar** kolumnen visas när en uppdatering till skyddsagenten är tillgänglig för varje skyddad dator. I den **åtgärder** rutan i **uppdatering** åtgärden är inte tillgänglig när en skyddad dator markeras om det finns uppdateringar.
  >
  >

3. Du installerar uppdaterade skyddsagenter på de valda datorerna genom att markera **uppdatering**.

4. För en klientdator som inte är ansluten till nätverket, tills datorn är ansluten till nätverket, den **Agentstatus** kolumnen visar statusen **väntande uppdatering**.

  När en klientdator är ansluten till nätverket, den **Agentuppdateringar** kolumn för klientdatorn visar statusen **uppdatering**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Flytta äldre skyddsgrupper från tidigare version och synkronisera den nya versionen med Azure

När både Azure Backup Server och Operativsystemet uppdateras, är du redo att skydda nya datakällor som använder Modern Backup Storage. Men redan skyddade datakällor fortsätter att skyddas på äldre sätt som de var i Azure Backup Server, men alla nya protection använder moderna Backup Storage.

Nedanstående steg är att migrera datakällor från bakåtkompatibelt läge skydd till moderna lagring för säkerhetskopiering.

• Lägg till de nya volymerna DPM-lagringspoolen och tilldela eget namn och data källkod om så önskas.
• För varje datakälla som är i bakåtkompatibelt läge, stoppskydd av datakällor och ”Kvarhåll skyddade Data”.  Detta gör att återställning av den äldre återställningspunkter efter migreringen.

• Skapa en ny sida och välj de datakällor som ska lagras med nya format.
• DPM gör en kopia av replik från den äldre lagringen för säkerhetskopiering i moderna säkerhetskopiering lagringsvolymen lokalt.
Obs: Detta visas som en återställning efter jobb • alla nya synkronisering och återställningspunkter sparas i den moderna Backup Storage.
• Gamla återställningspunkterna rensas ut eftersom de går ut och slutligen Frigör diskutrymmet.
• När alla äldre volymer tas bort från den gamla lagringsenheter, disken kan tas bort från Azure-säkerhetskopiering och systemet.
• Ta en säkerhetskopia av Azure-DPMDB.

Del 2:-viktiga objekt > den nya servern måste ha namnet samma som den ursprungliga Azure Backup-servern. Du kan inte ändra namnet på den nya Azure backup-servern om du vill använda gamla lagringspoolen och DPMDB för att behålla återställningspunkter - måste ha säkerhetskopiering av DPM-databasen som ska återställas

1) Avstängning ursprungliga Azure backup server eller starta kabeln.
2) Återställa datorkontot i active directory.
3) Installera Server 2016 på den nya datorn och kalla det samma datornamn som den ursprungliga Azure Backup-servern.
4) Ansluta till domänen
5) Installera Azure Backup server V2 (flytta DPM-lagringspooldiskar från den gamla servern och importera)
6) Återställ DPMDB tas från slutet av del 2
7) Bifoga lagring från den ursprungliga servern för säkerhetskopiering till den nya servern.
8) Återställ DPMDB från SQL
9) Admin kommandoraden på den nya servern cd till Microsoft Azure Backup för att installera platsen och bin-mappen

Exempel på sökvägen: C:\windows\system32 > cd ”c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
Säkerhetskopiera till Azure kör DPMSYNC-SYNC

10) Kör DPMSYNC-SYNC Obs Om du har lagt till nya diskar i DPM lagringspoolen i stället för att flytta gamla kör DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Nya PowerShell-cmdlets i v2

Två nya cmdlets finns tillgängliga när du installerar Azure Backup Server v2: 
* [Montera DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Demontera DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du förbereder din server eller börja skydda en arbetsbelastning:
- [Förbereda säkerhetskopiering serverarbetsbelastningar](backup-azure-microsoft-azure-backup.md)
- [Använd Backup Server för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md)
- [Använd Backup Server för att säkerhetskopiera SQL Server](backup-azure-sql-mabs.md)
- [Använda moderna lagring för säkerhetskopiering med Backup-Server](backup-mabs-add-storage.md)

