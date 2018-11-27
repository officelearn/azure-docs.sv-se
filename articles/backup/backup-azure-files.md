---
title: Säkerhetskopiera Azure-filresurser
description: Den här artikeln beskriver hur du säkerhetskopierar och återställer Azure-filresurser och förklarar hanteringsuppgifterna.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 3/23/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 14a6e295eebcc3a7cb3f190a09afd65b0e959d7e
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265346"
---
# <a name="back-up-azure-file-shares"></a>Säkerhetskopiera Azure-filresurser
Den här artikeln förklarar hur du använder Azure Portal för att säkerhetskopiera och återställa [Azure-filresurser](../storage/files/storage-files-introduction.md).

I den här guiden får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera ett Recovery Services-valv till att säkerhetskopiera Azure Files
> * Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt
> * Återställa en eller flera filer från en återställningspunkt
> * Hantera säkerhetskopieringsjobb
> * Avbryta skyddet i Azure Files
> * Ta bort dina säkerhetskopierade data

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du kan säkerhetskopiera en Azure-filresurs bör du kontrollera att den finns i någon av de [lagringskontotyper som stöds](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). När du har kontrollerat detta kan du skydda din filresurser.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-filresurser i förhandsversionen
Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-konton stöds. Följande säkerhetskopieringsscenarier stöds inte för Azure-filresurser:
- Du kan inte skydda Azure-filresurser i lagringskonton med RA-GRS-replikering\* [geo-redundant lagring med läsbehörighet](../storage/common/storage-redundancy-grs.md).
- Du kan inte skydda av Azure-filresurser i lagringskonton som har virtuella nätverk eller brandvägg aktiverade.
- Det finns ingen tillgänglig PowerShell eller CLI som skyddar Azure Files med Azure Backup.
- Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
- Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
- Förhindra att säkerhetskopior i Recovery Services-valvet oavsiktligt tas bort genom att använda [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot.
- Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.
- Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicksbilder som har tagits av Azure Backup när filresursen har tagits bort och förlorar därför alla återställningspunkter

\*Azure-filresurser i lagringskonton med replikeringsfunktionen [Read-Access Geo-Redundant Storage](../storage/common/storage-redundancy-grs.md) (RA-GRS) som GRS och fakturerade GRS-priser.

Säkerhetskopiering av Azure-filresurser i lagringskonton med replikering med [zonredundant lagring](../storage/common/storage-redundancy-zrs.md) (ZRS) är för närvarande endast tillgängligt i USA, centrala (CUS), USA, östra (EUS), USA, östra 2 (EUS2), Europa, norra (NE), Sydostasien (SEA), Europa, västra (WE) och USA, västra 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurera säkerhetskopiering för en Azure-filresurs
Alla säkerhetskopierade data lagras i Recovery Services-valv. Den här självstudien förutsätter att du redan har skapat en filresurs i Azure. Säkerhetskopiera Azure-filresurser:

1. Skapa ett Recovery Services-valv i samma region som din filresurs. Om du redan har ett valv öppnar du valvets översiktssida och klickar på **Backup**.

    ![Välj Azure-filresursen som mål för säkerhetskopieringen](./media/backup-file-shares/overview-backup-page.png)

2. I menyn Säkerhetskopieringsmål från **Vad vill du säkerhetskopiera?** väljer du Azure-filresurs.

    ![Välj Azure-filresursen som mål för säkerhetskopieringen](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Klicka på **Säkerhetskopiering** för att konfigurera Azure-filresursen till Recovery Services-valvet. 

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/set-backup-goal.png)

    När valvet är associerat med Azure-filresursen öppnas säkerhetskopieringsmenyn där du väljer ett lagringskonto. Menyn visar alla lagringskonton som stöds i den region där ditt valv finns, och som inte redan är associerade med ett Recovery Services-valv.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/list-of-storage-accounts.png)

4. Välj ett konto i listan med lagringskonton och klicka på **OK**. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till filresurser och inte ser dem i listan, bör du vänta lite på att filresurserna ska visas.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/discover-file-shares.png)

5. I listan **Filresurser** väljer du en eller flera filresurser som du vill säkerhetskopiera. Klicka på **OK**.

6. När du har valt dina filresurser växlar säkerhetskopieringsmenyn över till **Säkerhetskopieringspolicy**. I den här menyn väljer du antingen en befintlig säkerhetskopieringspolicy eller skapar en ny. Klicka sedan på **Aktivera säkerhetskopiering**. 

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/apply-backup-policy.png)

    När du har etablerat en säkerhetskopieringspolicy tas en ögonblicksbild av filresurserna vid den schemalagda tiden och en återställningspunkt sparas för den valda perioden.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran
Ibland kanske du vill skapa en ögonblicksbild eller återställningspunkt på en annan tid än de tider som schemalagts i säkerhetskopieringspolicyn. En vanlig tid att generera en säkerhetskopiering på begäran är direkt efter att du har konfigurerat säkerhetskopieringspolicyn. Baserat på schemat i säkerhetskopieringspolicyn kan det ta timmar eller dagar tills en ögonblicksbild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Du behöver ofta skapa en säkerhetskopiering på begäran innan du utför planerade ändringar i dina filresurser. 

### <a name="to-create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

1. Öppna det Recovery Services-valv som innehåller filresursens återställningspunkter och klicka på **Säkerhetskopieringsobjekt**. En lista med typer av säkerhetskopieringsobjekt visas.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/list-of-backup-items.png)

2. I listan väljer du **Azure Storage (Azure Files)**. Listan med Azure-filresurser visas.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Välj önskad filresurs i listan med Azure-filresurser. Menyn Säkerhetskopieringsobjekt för den valda filresursen öppnas.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/backup-item-menu.png)

4. I menyn Säkerhetskopieringsobjekt klickar du på **Säkerhetskopiera nu**. Eftersom detta är en säkerhetskopiering på begäran finns det ingen bevarandeprincip som är associerad med återställningspunkten. Dialogrutan **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. 
  
   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Återställa från en säkerhetskopia av Azure-filresurser
Om du behöver återställa en hel filresurs eller enskilda filer och mappar från en återställningspunkt, går du till Säkerhetskopieringsobjekt enligt anvisningarna i föregående avsnitt. Välj **Återställ resursen** för att återställa en hel filresurs från en önskad tidpunkt. I listan med återställningspunkter som visas väljer du en för att kunna skriva över den aktuella filresursen, eller återställa den till en annan filresurs i samma region.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Återställa enskilda filer och mappar från en säkerhetskopia av Azure-filresurser
I Azure Backup finns möjligheten att bläddra i en återställningspunkt i Azure-portalen. Om du vill återställa en fil eller mapp klickar du på Filåterställning på sidan Säkerhetskopieringsobjekt och väljer i listan med återställningspunkter. Välj Återställningsmål och klicka sedan på **Välj fil** för att bläddra i återställningspunkten. Välj den fil eller mapp du önskar och **Återställ**.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Hantera säkerhetskopior av Azure-filresurser

Du kan köra flera hanteringsåtgärder för säkerhetskopior av filresurser på sidan **Säkerhetskopieringsjobb**, inklusive:
- [Övervaka jobb](backup-azure-files.md#monitor-jobs)
- [Skapa en ny policy](backup-azure-files.md#create-a-new-policy)
- [Avbryta skyddet av en filresurs](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Återuppta skyddet av en filresurs](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Ta bort säkerhetskopierade data](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Övervaka jobb

Du kan övervaka förloppet för alla jobb på sidan **Säkerhetskopieringsjobb**.

Öppna sidan **Säkerhetskopieringsjobb**:

- Öppna Recovery Services-valvet som du vill övervaka. I menyn klickar du på **Jobb** och sedan på **Säkerhetskopieringsjobb**.
   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/open-backup-jobs.png)

    Listan med säkerhetskopieringsjobb och status för dessa jobb visas.
   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Skapa en ny policy

Du kan skapa en ny policy för att säkerhetskopiera Azure-filresurser i **Principer för säkerhetskopiering** i Recovery Services-valvet. Alla principer som skapas när du konfigurerar säkerhetskopiering av filresurser visas med principtypen Azure-filresurs.

Visa befintliga principer för säkerhetskopiering:

- Öppna önskat Recovery Services-valv. I menyn klickar du på **Principer för säkerhetskopiering**. Alla principer för säkerhetskopiering visas i listan.

   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/list-of-backup-policies.png)

Skapa en ny princip för säkerhetskopiering:

1. På menyn i Recovery Services-valvet klickar du på **Principer för säkerhetskopiering**.
2. I listan Principer för säkerhetskopiering klickar du på **Lägg till**.

   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/new-backup-policy.png)

3. I menyn **Lägg till** väljer du **Azure-filresurs**. Menyn Säkerhetskopieringspolicy för Azure-filresursen öppnas. Ange ett namn på principen, säkerhetskopieringsfrekvens och kvarhållningsintervall för återställningspunkterna. Klicka på OK när du har definierat principen.

   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Sluta skydda en Azure-filresurs

Om du vill sluta skydda en Azure-filresurs, tillfrågas du om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda Azure-filresurser:

- Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter, eller
- Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna

Det kan finnas en kostnad för att lämna kvar återställningspunkterna i minnet eftersom de underliggande ögonblicksbilder som skapats av Azure Backup kommer att behållas. Fördelen med att lämna kvar återställningspunkterna är dock att du kan återställa filresursen senare, om så önskas. Information om kostnaden för att behålla återställningspunkter finns i prissättningsinformationen. Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

Sluta skydda en Azure-filresurs:

1. Öppna det Recovery Services-valv som innehåller filresursens återställningspunkter och klicka på **Säkerhetskopieringsobjekt**. En lista med typer av säkerhetskopieringsobjekt visas.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/list-of-backup-items.png) 

2. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)**. Listan med säkerhetskopieringsobjekt för (Azure Storage (Azure Files)) visas.

   ![klicka på objektet om du vill öppna ytterligare en meny](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. I listan med säkerhetskopieringsobjekt (Azure Storage (Azure Files)), väljer du det säkerhetskopieringsobjekt som du vill stoppa.

4. I Azure-filresursobjekten klickar du i menyn **Mer** och väljer **Stoppa säkerhetskopiering**. 

   ![klicka på objektet om du vill öppna ytterligare en meny](./media/backup-file-shares/stop-backup.png)

5. I menyn Stoppa säkerhetskopiering väljer du **Behåll** eller **Ta bort säkerhetskopieringsdata** och klickar på **Stoppa säkerhetskopiering**.

   ![klicka på objektet om du vill öppna ytterligare en meny](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Återuppta skyddet av Azure-filresurser

Om du valde alternativet Behåll säkerhetskopieringsdata när skyddet av filresursen stoppades, går det att återuppta skyddet. Om alternativet **Ta bort säkerhetskopieringsdata** valdes går det inte att återuppta skyddet av filresursen.

Om du vill återuppta skyddet av filresursen, går du till Säkerhetskopieringsobjekt och klickar på Återuppta säkerhetskopiering. Säkerhetskopieringspolicyn öppnas och du kan välja en policy för att återuppta säkerhetskopieringen.

   ![Välj det jobb som du vill övervaka](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata 

Du kan ta bort säkerhetskopian av en resurs under jobbet Stoppa säkerhetskopiering eller efter att du har stoppat skyddet. Det kan vara en fördel att vänta dagar eller veckor innan du tar bort återställningspunkterna. Till skillnad från återställning av återställningspunkter när du tar bort säkerhetskopierade data, kan du inte välja specifika återställningspunkter som ska tas bort. Om du väljer att ta bort säkerhetskopierade data, tar du bort alla återställningspunkter som är associerade med objektet.

Följande procedur förutsätter att säkerhetskopieringen av den virtuella datorn har stoppats. När säkerhetskopieringen har stoppats är alternativen Återuppta säkerhetskopiering och Ta bort säkerhetskopieringsdata tillgängliga i instrumentpanelen Säkerhetskopieringsobjekt. Klicka på Ta bort säkerhetskopieringsdata och skriv namnet på filresursen för att bekräfta borttagningen. Du kan också ange en orsak till borttagningen eller en kommentar.

## <a name="see-also"></a>Se även
Mer information om Azure-filresurser finns i
- [Vanliga frågor och svar om säkerhetskopiering av Azure-filresurser](backup-azure-files-faq.md)
- [Felsöka säkerhetskopiering av Azure-filresurser](troubleshoot-azure-files.md)
 

