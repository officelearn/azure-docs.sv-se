---
title: Hantera säkerhetskopierade SAP HANA-databaser på virtuella Azure-datorer
description: I den här artikeln kan du lära dig vanliga uppgifter för att hantera och övervaka SAP HANA-databaser som körs på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480070"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Hantera och övervaka säkerhetskopierade SAP HANA-databaser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka SAP HANA-databaser som körs på en virtuell Azure-dator (VM) och som säkerhetskopieras till ett Azure Backup Recovery Services-valv av [Azure Backup-tjänsten.](https://docs.microsoft.com/azure/backup/backup-overview) Du får lära dig hur du övervakar jobb och aviseringar, utlöser en säkerhetskopiering på begäran, redigerar principer, stoppar och återupptar databasskydd och avregistrerar en virtuell dator från säkerhetskopior.

Om du inte har konfigurerat säkerhetskopior ännu för DINA SAP HANA-databaser läser du [Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuella säkerhetskopieringsjobb i portalen

Azure Backup visar alla manuellt utlösta jobb i avsnittet **Säkerhetskopieringsjobb** på Azure-portalen.

![Avsnittet Säkerhetskopieringsjobb](./media/sap-hana-db-manage/backup-jobs.png)

Jobben som visas i den här portalen inkluderar databasidentifiering och registrering samt säkerhetskopierings- och återställningsåtgärder. Schemalagda jobb, inklusive loggsäkerhetskopior visas inte i det här avsnittet. Manuellt utlösta säkerhetskopior från SAP HANA-klienterna (Studio/ Cockpit/ DBA Cockpit) visas inte heller här.

![Lista över säkerhetskopieringsjobb](./media/sap-hana-db-manage/backup-jobs-list.png)

Mer information om övervakning finns [i Övervakning i Azure-portalen](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) och [övervakning med Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Aviseringar är ett enkelt sätt att övervaka säkerhetskopior av SAP HANA-databaser. Aviseringar hjälper dig att fokusera på de händelser du bryr dig mest om utan att gå vilse i de många händelser som en säkerhetskopia genererar. Med Azure Backup kan du ställa in aviseringar och de kan övervakas på följande sätt:

* Logga in på [Azure-portalen](https://portal.azure.com/).
* På instrumentpanelen för valvet väljer du **Säkerhetskopior**.

  ![Säkerhetskopior på instrumentpanelen för valvet](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Du kommer att kunna se aviseringarna:

  ![Lista över aviseringar om säkerhetskopiering](./media/sap-hana-db-manage/backup-alerts-list.png)

* Klicka på aviseringarna för att se mer information:

  ![Aviseringsinformation](./media/sap-hana-db-manage/alert-details.png)

Idag tillåter Azure Backup att skicka aviseringar via e-post. Dessa varningar är:

* Utlöses för alla säkerhetskopieringsfel.
* Konsoliderad på databasnivå efter felkod.
* Skickas endast för en databass första säkerhetskopieringsfel.

Om du vill veta mer om övervakning går du till [Övervakning i Azure-portalen](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) och [övervakning med Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Hanteringsåtgärder

Azure Backup gör det enkelt att hantera en säkerhetskopierad SAP HANA-databas med ett överflöd av hanteringsåtgärder som den stöder. Dessa åtgärder diskuteras mer i detalj i följande avsnitt.

### <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

Säkerhetskopior körs i enlighet med principschemat. Du kan köra en säkerhetskopia på begäran enligt följande:

1. Klicka på **Säkerhetsobjekt**på arkivmenyn.
2. I **Säkerhetskopieringsobjekt**väljer du den virtuella datorn som kör SAP HANA-databasen och klickar sedan på **Säkerhetskopiering nu**.
3. I **Säkerhetskopiering nu**använder du kalenderkontrollen för att välja den sista dagen då återställningspunkten ska behållas. Klicka sedan på **OK**.
4. Övervaka portalmeddelandena. Du kan övervaka jobbframsteget i instrumentpanelen för valvet >**pågående** **säkerhetskopieringsjobb** > . Beroende på databasens storlek kan det ta en stund att skapa den första säkerhetskopian.

### <a name="hana-native-client-integration"></a>INTEGRERING AV HANA-klient

Nu på begäran full säkerhetskopior utlöses från någon av DE HANA infödda klienter kommer att visas som en fullständig säkerhetskopia på sidan **Säkerhetskopieringsobjekt.**

![Senaste säkerhetskopieringar körs](./media/sap-hana-db-manage/last-backups.png)

Dessa ad hoc-fullständiga säkerhetskopior visas också i listan över återställningspunkter för återställning.

![Lista över återställningspunkter](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Kör SAP HANA-säkerhetskopiering av inbyggd klient i en databas med Azure-säkerhetskopiering aktiverad

Om du vill göra en lokal säkerhetskopia (med HANA Studio / Cockpit) i en databas som säkerhetskopieras med Azure Backup gör du följande:

1. Vänta tills alla fullständiga säkerhetskopior eller loggsäkerhetskopior för att databasen ska slutföras. Kontrollera statusen i SAP HANA Studio/Cockpit.
2. Inaktivera loggsäkerhetskopior och ställ in säkerhetskopieringskatalogen till filsystemet för relevant databas.
3. Det gör du genom att dubbelklicka på Databasfiltret för**Configuration** > **konfigurationsval** > av **systemdb** > **(logg).**
4. Ange **enable_auto_log_backup** till **Nej**.
5. Ställ in **log_backup_using_backint** på **False**.
6. Gör en fullständig säkerhetskopiering av databasen på begäran.
7. Vänta tills säkerhetskopieringen och katalogsäkerhetskopiorna är klara.
8. Återställ de tidigare inställningarna till de för Azure:
   * Ställ in **enable_auto_log_backup** på **Ja**.
   * Ställ **in log_backup_using_backint** på **True**.

### <a name="change-policy"></a>Ändra policy

Du kan ändra den underliggande principen för ett SAP HANA-säkerhetsobjekt.

* Gå till **Säkerhetskopieringsobjekt**i instrumentpanelen för valvet:

  ![Välj säkerhetskopior](./media/sap-hana-db-manage/backup-items.png)

* Välj **SAP HANA i Azure VM**

  ![Välj SAP HANA i Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Välj det säkerhetskopieringsobjekt vars underliggande princip du vill ändra
* Klicka på den befintliga säkerhetskopieringsprincipen

  ![Välj befintlig princip för säkerhetskopiering](./media/sap-hana-db-manage/existing-backup-policy.png)

* Ändra principen genom att välja från listan. [Skapa en ny princip](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) för säkerhetskopiering om det behövs.

  ![Välj princip från listrutan](./media/sap-hana-db-manage/choose-backup-policy.png)

* Spara ändringarna

  ![Spara ändringarna](./media/sap-hana-db-manage/save-changes.png)

* Principändring påverkar alla associerade säkerhetskopieringsobjekt och utlöser motsvarande **konfigurera skyddsjobb.**

>[!NOTE]
> Alla ändringar i lagringsperioden kommer att tillämpas retroaktivt på alla äldre återställningspunkter förutom de nya.
>
> Inkrementella principer för säkerhetskopiering kan inte användas för SAP HANA-databaser. Inkrementell säkerhetskopiering stöds för närvarande inte för dessa databaser.

### <a name="modify-policy"></a>Ändra princip

Ändra principen för att ändra säkerhetskopieringstyper, frekvenser och kvarhållningsintervall.

>[!NOTE]
>Alla ändringar i lagringsperioden kommer att tillämpas retroaktivt på alla äldre återställningspunkter, utöver de nya.

1. Gå till **Hantera > principer för säkerhetskopiering i** valvet och välj den princip som du vill redigera.

   ![Välj den princip som ska redigeras](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Välj **Ändra**.

   ![Välj Ändra](./media/sap-hana-db-manage/modify-policy.png)

1. Välj frekvens för säkerhetskopieringstyperna.

   ![Välj säkerhetskopieringsfrekvens](./media/sap-hana-db-manage/choose-frequency.png)

Principändring påverkar alla associerade säkerhetskopieringsobjekt och utlöser motsvarande **konfigurera skyddsjobb.**

### <a name="inconsistent-policy"></a>Inkonsekvent policy

Ibland kan en ändringsprincipåtgärd leda till en **inkonsekvent** principversion för vissa säkerhetskopieringsobjekt. Detta händer när motsvarande **konfigurationsskyddsjobb** misslyckas för säkerhetskopieringsobjektet när en ändringsprincipåtgärd har utlösts. Det visas på följande sätt i vyn för säkerhetskopieringsobjekt:

![Inkonsekvent policy](./media/sap-hana-db-manage/inconsistent-policy.png)

Du kan åtgärda principversionen för alla påverkade objekt med ett klick:

![Åtgärda principversion](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skydd för en SAP HANA-databas

Du kan sluta skydda en SAP HANA-databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb och lämna återställningspunkterna intakta.

Om du väljer att lämna återställningspunkter bör du tänka på följande:

* Alla återställningspunkter förblir intakta för alltid, all beskärning ska stanna vid stoppskyddet med lagringsdata.
* Du debiteras för den skyddade instansen och förbrukad lagring. Mer information finns i [Azure Backup-priser](https://azure.microsoft.com/pricing/details/backup/).
* Om du tar bort en datakälla utan att stoppa säkerhetskopior misslyckas nya säkerhetskopior.

Så här stoppar du skydd för en databas:

* På instrumentpanelen för valvet väljer du **Säkerhetskopieringsobjekt**.
* Under **Hanteringstyp för säkerhetskopiering**väljer du **SAP HANA i Azure VM**

  ![Välj SAP HANA i Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Välj den databas som du vill stoppa skyddet för:

  ![Välj databas för att stoppa skyddet](./media/sap-hana-db-manage/select-database.png)

* På databasmenyn väljer du **Stoppa säkerhetskopiering**.

  ![Välj stoppa säkerhetskopiering](./media/sap-hana-db-manage/stop-backup.png)

* På menyn **Stoppa säkerhetskopiering** väljer du om data ska behållas eller tas bort. Om du vill kan du ange en orsak och kommentar.

  ![Markera behåll eller ta bort data](./media/sap-hana-db-manage/retain-backup-data.png)

* Välj **Stoppa säkerhetskopiering**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Återuppta skyddet för en SAP HANA-databas

När du stoppar skyddet för SAP HANA-databasen kan du senare återuppta skyddet om du väljer alternativet **Behåll säkerhetskopieringsdata.** Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Så här återupptar du skyddet för en SAP HANA-databas:

* Öppna säkerhetskopian och välj **Återuppta säkerhetskopiering**.

   ![Välj återuppta säkerhetskopiering](./media/sap-hana-db-manage/resume-backup.png)

* På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Uppgradering från SAP HANA 1.0 till 2.0

Lär dig hur du fortsätter säkerhetskopieringen för en SAP HANA-databas [efter uppgradering från SAP HANA 1.0 till 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Uppgradera utan SID-ändring

Lär dig hur du fortsätter säkerhetskopieringen av en SAP HANA-databas vars [SID inte har ändrats efter uppgraderingen](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-instance"></a>Avregistrera en SAP HANA-instans

Avregistrera en SAP HANA-instans när du har inaktiverat skyddet men innan du tar bort valvet:

* Välj **Säkerhetskopieringsinfrastruktur**under **Hantera**på instrumentpanelen för valvet .

   ![Välja infrastruktur för säkerhetskopiering](./media/sap-hana-db-manage/backup-infrastructure.png)

* Välj **typ av säkerhetskopieringshantering** som **arbetsbelastning i Azure VM**

   ![Välj typ av säkerhetskopieringshantering som arbetsbelastning i Azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* I **Skyddade servrar**väljer du den instans som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar/instanser.

* Högerklicka på den skyddade instansen och välj **Avregistrera**.

   ![Välj avregistrera](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Registrera tillägg för återregistrering på DEN VIRTUELLA DATORN FÖR SAP HANA-servern

Ibland kan arbetsbelastningstillägget på den virtuella datorn påverkas av en eller annan anledning. I sådana fall kommer alla åtgärder som utlöses på den virtuella datorn att börja misslyckas. Du kan då behöva registrera tillägget på den virtuella datorn igen. Omregistrera åtgärden installerar om tillägget för säkerhetskopiering av arbetsbelastningen på den virtuella datorn för att åtgärderna ska kunna fortsätta.

Använd det här alternativet med försiktighet: när du utlöses på en virtuell dator med ett redan felfritt tillägg kommer den här åtgärden att orsaka att tillägget startas om. Detta kan leda till att alla pågående jobb misslyckas. Kontrollera om det finns ett eller flera av [symptomen](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) innan du utlöser omregistreringen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA-databaser.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
