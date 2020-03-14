---
title: Hantera säkerhetskopierade SAP HANA databaser på virtuella Azure-datorer
description: I den här artikeln lär du dig vanliga uppgifter för att hantera och övervaka SAP HANA databaser som körs på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252407"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Hantera och övervaka säkerhetskopierade SAP HANA-databaser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka SAP HANA databaser som körs på en virtuell Azure-dator (VM) och som säkerhets kopie ras till en Azure Backup Recovery Services valvet av [Azure backups](https://docs.microsoft.com/azure/backup/backup-overview) tjänsten. Du lär dig hur du övervakar jobb och aviseringar, utlöser en säkerhets kopiering på begäran, redigerar principer, stoppar och återupptar databas skyddet och avregistrerar en virtuell dator från säkerhets kopior.

Om du inte har konfigurerat säkerhets kopieringar ännu för dina SAP HANA-databaser, se [säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuella säkerhets kopierings jobb i portalen

Azure Backup visar alla manuellt utlösta jobb i avsnittet **säkerhets kopierings jobb** på Azure Portal.

![Avsnittet säkerhets kopierings jobb](./media/sap-hana-db-manage/backup-jobs.png)

De jobb som visas i den här portalen är identifiering och registrering av databaser samt säkerhets kopierings-och återställnings åtgärder. Schemalagda jobb, inklusive logg säkerhets kopior visas inte i det här avsnittet. Manuellt utlösta säkerhets kopieringar från SAP HANA inbyggda klienter (Studio/cockpit/DBA cockpit) visas inte här.

![Lista över säkerhets kopierings jobb](./media/sap-hana-db-manage/backup-jobs-list.png)

Om du vill veta mer om övervakning går du till [övervakning i Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) och [övervakning med hjälp av Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Aviseringar är ett enkelt sätt att övervaka säkerhets kopieringar av SAP HANA databaser. Aviseringar hjälper dig att fokusera på de händelser du bryr dig om utan att förlora antalet händelser som en säkerhets kopia genererar. Med Azure Backup kan du ange aviseringar och de kan övervakas på följande sätt:

* Logga in på [Azure Portal](https://portal.azure.com/).
* På instrument panelen för valv väljer du **säkerhets kopierings aviseringar**.

  ![Säkerhets kopierings aviseringar på valv instrument panel](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Du kommer att kunna se aviseringarna:

  ![Lista över säkerhets kopierings aviseringar](./media/sap-hana-db-manage/backup-alerts-list.png)

* Klicka på aviseringarna om du vill se mer information:

  ![Aviseringsinformation](./media/sap-hana-db-manage/alert-details.png)

I dag kan Azure Backup skicka aviseringar via e-post. Dessa aviseringar är:

* Utlöses för alla säkerhets kopierings problem.
* Konsol IDE rad på databas nivå efter felkod.
* Skickas bara för första säkerhets kopierings försöket i databasen.

Skickas Läs mer om övervakning genom att gå till [övervakning i Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) och [övervakning med hjälp av Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Hanteringsåtgärder

Azure Backup gör hanteringen av en säkerhetskopierad SAP HANA-databas lätt med en mängd hanterings åtgärder som stöds. Dessa åtgärder beskrivs i detalj i följande avsnitt.

### <a name="run-an-ad-hoc-backup"></a>Köra en ad hoc-säkerhetskopiering

Säkerhets kopieringar körs enligt princip schemat. Du kan köra en säkerhets kopiering på begäran på följande sätt:

1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**väljer du den virtuella dator som kör SAP HANA databasen och klickar sedan på **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka sedan på **OK**.
4. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > **pågår**. Det kan ta en stund att skapa den första säkerhets kopieringen, beroende på databasens storlek.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Kör SAP HANA inbyggd klient säkerhets kopiering på en databas med Azure Backup aktiverat

Om du vill ta en lokal säkerhets kopia (med HANA Studio/cockpit) för en databas som säkerhets kopie ras med Azure Backup gör du följande:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio/cockpit.
2. Inaktivera logg säkerhets kopior och ange säkerhets kopierings katalogen till fil systemet för relevant databas.
3. Det gör du genom att dubbelklicka på **systemdb** > **konfiguration** > **välja databas** > **filter (logg)** .
4. Ange **enable_auto_log_backup** till **Nej**.
5. Ange **log_backup_using_backint** till **false**.
6. Utför en fullständig säkerhets kopiering på begäran av databasen.
7. Vänta tills den fullständiga säkerhets kopieringen och katalog säkerhets kopieringen har slutförts.
8. Återställ tidigare inställningar tillbaka till dem för Azure:
   * Ange **enable_auto_log_backup** till **Ja**.
   * Ange **log_backup_using_backint** till **Sant**.

### <a name="change-policy"></a>Ändra princip

Du kan ändra den underliggande principen för ett SAP HANA säkerhets kopierings objekt.

* På instrument panelen för valv går du till **säkerhets kopierings objekt**:

  ![Välj säkerhets kopierings objekt](./media/sap-hana-db-manage/backup-items.png)

* Välj **SAP HANA i virtuell Azure-dator**

  ![Välj SAP HANA i virtuell Azure-dator](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Välj det säkerhets kopierings objekt vars underliggande princip du vill ändra
* Klicka på den befintliga säkerhets kopierings principen

  ![Välj befintlig säkerhets kopierings princip](./media/sap-hana-db-manage/existing-backup-policy.png)

* Ändra principen genom att välja i listan. [Skapa en ny säkerhets kopierings policy om det](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) behövs.

  ![Välj princip i listruta](./media/sap-hana-db-manage/choose-backup-policy.png)

* Spara ändringarna

  ![Spara ändringarna](./media/sap-hana-db-manage/save-changes.png)

* Princip ändringen påverkar alla tillhör ande säkerhets kopierings objekt och utlöser motsvarande **Konfigurera skydds** jobb.

>[!NOTE]
> Eventuella ändringar i kvarhållningsperioden tillämpas retroaktivt för alla äldre återställnings punkter förutom de nya.
>
> Det går inte att använda principer för stegvis säkerhets kopiering för SAP HANA databaser. Stegvis säkerhets kopiering stöds för närvarande inte för dessa databaser.

### <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skyddet för en SAP HANA databas

Du kan sluta skydda en SAP HANA-databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhets kopierings jobb och lämna återställnings punkterna intakta.

Om du väljer att lämna återställnings punkter bör du tänka på följande:

* Alla återställnings punkter förblir intakta för alltid och all rensning stoppas vid stopp av skyddet med data kvar.
* Du kommer att debiteras för den skyddade instansen och den förbrukade lagringen. Mer information finns i [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).
* Om du tar bort en data källa utan att stoppa säkerhets kopieringen kommer nya säkerhets kopieringar att Miss lyckas.

Så här stoppar du skydd för en databas:

* På instrument panelen för valvet väljer du **säkerhets kopierings objekt**.
* Under **säkerhets kopierings hanterings typ**väljer du **SAP HANA i virtuell Azure-dator**

  ![Välj SAP HANA i virtuell Azure-dator](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Välj den databas som du vill sluta skydda:

  ![Välj databas för att sluta skydda](./media/sap-hana-db-manage/select-database.png)

* På menyn databas väljer du **stoppa säkerhets kopiering**.

  ![Välj Avbryt säkerhets kopiering](./media/sap-hana-db-manage/stop-backup.png)

* På menyn **stoppa säkerhets kopiering** väljer du om du vill behålla eller ta bort data. Ange en orsak och kommentar om du vill.

  ![Välj Behåll eller ta bort data](./media/sap-hana-db-manage/retain-backup-data.png)

* Välj **stoppa säkerhets kopiering**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Återuppta skyddet för en SAP HANA databas

Om du slutar skydda SAP HANA databasen kan du senare återuppta skyddet om du väljer alternativet **Behåll säkerhets kopierings data** . Om du inte behåller säkerhetskopierade data kommer du inte att kunna återuppta skyddet.

Återuppta skyddet för en SAP HANA databas:

* Öppna säkerhets kopierings objekt och välj **återuppta säkerhets kopiering**.

   ![Välj återuppta säkerhets kopiering](./media/sap-hana-db-manage/resume-backup.png)

* På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Uppgraderar från SAP HANA 1,0 till 2,0

Lär dig hur du fortsätter att säkerhetskopiera för en SAP HANA-databas [efter uppgraderingen från SAP HANA 1,0 till 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Uppgradera utan en SID-ändring

Lär dig hur du fortsätter säkerhetskopiera en SAP HANA databas vars [sid inte har ändrats efter uppgraderingen](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Avregistrera en SAP HANA databas

Avregistrera en SAP HANA instans när du har inaktiverat skyddet, men innan du tar bort valvet:

* På instrument panelen för valv under **Hantera**väljer du **infrastruktur för säkerhets kopiering**.

   ![Välja infrastruktur för säkerhetskopiering](./media/sap-hana-db-manage/backup-infrastructure.png)

* Välj **hanterings typen säkerhets kopiering** som **arbets belastning i virtuell Azure-dator**

   ![Välj hanterings typen säkerhets kopiering som arbets belastning i virtuell Azure-dator](./media/sap-hana-db-manage/backup-management-type.png)

* I **skyddade servrar**väljer du den instans som ska avregistreras. Om du vill ta bort valvet måste du avregistrera alla servrar/instanser.

* Högerklicka på den skyddade instansen och välj **avregistrera**.

   ![Välj avregistrera](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
