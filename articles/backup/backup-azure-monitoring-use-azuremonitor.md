---
title: Övervaka Azure Backup med Azure Monitor
description: Övervaka Azure Backup arbets belastningar och skapa anpassade aviseringar med Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1fb739c8d517654c7258fd3a58c93ab29602f228
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894070"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Övervaka i skala med hjälp av Azure Monitor

Azure Backup tillhandahåller [inbyggda övervaknings-och aviserings funktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan någon ytterligare hanterings infrastruktur. Men den här inbyggda tjänsten är begränsad i följande scenarier:

- Om du övervakar data från flera Recovery Services-valv över prenumerationer
- Om den önskade aviserings kanalen *inte* är e-post
- Om användarna vill ha aviseringar för fler scenarier
- Om du vill visa information från en lokal komponent, till exempel System Center Data Protection Manager i Azure, som portalen inte visas i [**säkerhets kopierings jobb**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) eller [**säkerhets kopierings aviseringar**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Använda Log Analytics arbets yta

> [!NOTE]
> Data från virtuella Azure-säkerhetskopieringar, Azure Backup-agenten, System Center Data Protection Manager, SQL-säkerhetskopieringar i virtuella Azure-datorer och Azure Files dela säkerhets kopiorna pumpas till Log Analytics arbets ytan via diagnostikinställningar. Stöd för Microsoft Azure Backup Server (MABS) kommer snart att läggas till

För att övervaka/rapportera i skala behöver du funktionerna i två Azure-tjänster. *Diagnostikinställningar* skickar data från flera Azure Resource Manager resurser till en annan resurs. *Log Analytics* skapar anpassade aviseringar där du kan använda åtgärds grupper för att definiera andra meddelande kanaler.

Följande avsnitt innehåller information om hur du använder Log Analytics för att övervaka Azure Backup i skala.

### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Azure Resource Manager resurser, till exempel Recovery Services-valvet, registrera information om schemalagda åtgärder och användar utlöst åtgärder som diagnostikdata.

I avsnittet övervakning väljer du **diagnostikinställningar** och anger målet för Recovery Services valvets diagnostikdata.

![Den Recovery Services valvets diagnostiska inställning, mål Log Analytics](media/backup-azure-monitoring-laworkspace/diagnostic-setting-new.png)

Du kan rikta en Log Analytics arbets yta från en annan prenumeration. Om du vill övervaka valv mellan prenumerationer på en enda plats väljer du samma Log Analytics arbets yta för flera Recovery Services-valv. Om du vill kanal all information som är relaterad till Azure Backup till arbets ytan Log Analytics väljer du **Resurstilldelning** i växlingen som visas och väljer följande händelser- **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**, **AddonAzureBackupProtectedInstance**. Läs [den här artikeln](backup-azure-diagnostic-events.md) om du vill ha mer information om hur du konfigurerar inställningar för La-diagnostik.

> [!IMPORTANT]
> När du har slutfört konfigurationen bör du vänta 24 timmar innan den första data-push-installationen har slutförts. Efter den första data sändningen skickas alla händelser som beskrivs längre fram i den här artikeln i [avsnittet frekvens](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Distribuera en lösning till arbets ytan Log Analytics

> [!IMPORTANT]
> Vi har släppt en uppdaterad [mall](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) för flera vyer för La-baserad övervakning och rapportering i Azure Backup. Observera att användare som använde den [tidigare lösningen](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) kommer att fortsätta att se den i sina arbets ytor även efter att ha distribuerat den nya lösningen. Den gamla lösningen kan dock ge felaktiga resultat på grund av vissa mindre schema ändringar. Användarna måste därför distribuera den nya mallen.

När data finns i arbets ytan Log Analytics distribuerar du [en GitHub-mall](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) för att Log Analytics för att visualisera data. För att identifiera arbets ytan korrekt, se till att ge den samma resurs grupp, namn på arbets yta och plats för arbets ytan. Installera sedan mallen på arbets ytan.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Visa Azure Backup data med Log Analytics

> [!IMPORTANT]
> Mallen för LA rapportering stöder för närvarande data från den äldre Event AzureBackupReport i AzureDiagnostics-läge. Om du vill använda den här mallen måste du [Konfigurera inställningarna för valv diagnostik i Azure-diagnostik läge](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event). 

- **Azure Monitor**: i avsnittet **insikter** väljer du **mer** och väljer sedan den relevanta arbets ytan.
- **Log Analytics arbets ytor**: Välj relevant arbets yta och under **Allmänt**väljer du **arbets ytans Sammanfattning**.

![Panelerna Log Analytics övervakning och rapportering](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

När du väljer någon av översikts panelerna kan du Visa mer information. Här följer några av de rapporter som visas:

- Säkerhets kopierings jobb som inte är logga

   ![Log Analytics diagram för säkerhets kopierings jobb](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Aviseringar från säkerhets kopiering av Azure-resurser

   ![Log Analytics diagram för återställnings jobb](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

På samma sätt kan du, genom att klicka på de andra panelerna, se rapporter om återställnings jobb, moln lagring, säkerhets kopierings objekt, varningar från säkerhets kopiering av lokala resurser och logg säkerhets kopierings jobb.

Dessa grafer ingår i mallen. Du kan redigera graferna eller lägga till fler grafer om du behöver.

### <a name="create-alerts-by-using-log-analytics"></a>Skapa aviseringar med hjälp av Log Analytics

I Azure Monitor kan du skapa egna aviseringar i en Log Analytics arbets yta. I arbets ytan använder du *Azures åtgärds grupper* för att välja önskad meddelande funktion.

> [!IMPORTANT]
> Information om kostnaden för att skapa den här frågan finns [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

Välj något av diagrammen för att öppna avsnittet **loggar** i arbets ytan Log Analytics. I avsnittet **loggar** redigerar du frågorna och skapar aviseringar på dem.

![Skapa en avisering i en Log Analytics arbets yta](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

När du väljer **ny varnings regel**öppnas sidan Azure Monitor aviserings skapande som visas i följande bild. Här är resursen redan markerad som Log Analytics arbets yta och integration av åtgärds gruppen.

![Sidan Log Analytics aviserings skapande](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Aviserings villkor

Den definiering av egenskaperna för en avisering är dess Utlös ande villkor. Välj **villkor** om du vill läsa in Kusto-frågan automatiskt på sidan **loggar** som du ser i följande bild. Här kan du redigera villkoret så att det passar dina behov. Mer information finns i [exempel på Kusto-frågor](#sample-kusto-queries).

![Konfigurera ett varnings villkor](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Om det behövs kan du redigera frågan Kusto. Välj ett tröskelvärde, en period och en frekvens. Tröskelvärdet avgör när aviseringen ska höjas. Perioden är tiden då frågan körs. Om tröskelvärdet till exempel är större än 0, är perioden 5 minuter och frekvensen är 5 minuter, kör regeln frågan var 5: e minut, och granska de senaste 5 minuterna. Om antalet resultat är större än 0 får du ett meddelande via den valda åtgärds gruppen.

#### <a name="alert-action-groups"></a>Aviserings åtgärds grupper

Använd en åtgärds grupp för att ange en meddelande kanal. Om du vill se tillgängliga aviserings mekanismer väljer du **Skapa nytt**under **Åtgärds grupper**.

![Tillgängliga aviserings mekanismer i fönstret Lägg till åtgärds grupp](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Du kan uppfylla alla varnings-och övervaknings krav från Log Analytics ensamma, eller så kan du använda Log Analytics för att komplettera inbyggda meddelanden.

Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [skapa och hantera åtgärds grupper i Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Standard diagrammen ger dig Kusto frågor om grundläggande scenarier som du kan använda för att skapa aviseringar. Du kan också ändra frågorna för att hämta de data som du vill bli aviserad om. Klistra in följande exempel på Kusto frågor på sidan **loggar** och skapa sedan aviseringar för frågorna:

- Alla slutförda säkerhets kopierings jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Alla misslyckade säkerhets kopierings jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Alla lyckade säkerhets kopierings jobb för virtuella Azure-datorer

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade säkerhets kopierings jobb för SQL-logg

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade Azure Backup Agent jobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

### <a name="diagnostic-data-update-frequency"></a>Uppdaterings frekvens för diagnostikdata

Diagnostikdata från valvet pumpas till Log Analytics arbets ytan med en fördröjning. Varje händelse anländer till Log Analytics arbets ytan *20 till 30 minuter* efter att den har överförts från Recovery Services-valvet. Här är mer information om fördröjningen:

- I alla lösningar skickas säkerhets kopierings tjänstens inbyggda aviseringar så snart de har skapats. De visas vanligt vis i arbets ytan Log Analytics efter 20 till 30 minuter.
- I alla lösningar skickas säkerhets kopierings jobb på begäran och återställnings jobb så fort de är *klara*.
- För alla lösningar utom SQL-säkerhetskopiering skickas schemalagda säkerhets kopierings jobb så snart de är *klara*.
- För SQL-säkerhetskopiering, eftersom logg säkerhets kopieringar kan ske var 15: e minut, är information för alla schemalagda säkerhets kopierings jobb, inklusive loggar, batch och push-överförd var 6: e timme.
- Alla lösningar, annan information som säkerhets kopierings objekt, princip, återställnings punkter, lagring och så vidare, överförs minst *en gång per dag.*
- En ändring i konfigurationen för säkerhets kopiering (till exempel att ändra princip eller redigera princip) utlöser en push av all relaterad säkerhets kopierings information.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Använda aktivitets loggarna i Recovery Services Vault

> [!CAUTION]
> Följande steg gäller endast för *säkerhets kopiering av virtuella Azure-datorer.* Du kan inte använda de här stegen för lösningar som Azure Backup Agent, SQL-säkerhetskopieringar i Azure eller Azure Files.

Du kan också använda aktivitets loggar för att få meddelanden om händelser som till exempel säkerhets kopieringen lyckades. Börja med att följa dessa steg:

1. Logga in på Azure Portal.
1. Öppna det relevanta Recovery Services-valvet.
1. I valvets egenskaper öppnar du avsnittet **aktivitets logg** .

Identifiera lämplig logg och skapa en avisering:

1. Kontrol lera att du har fått aktivitets loggar för lyckade säkerhets kopieringar genom att använda de filter som visas i följande bild. Ändra **TimeSpan** -värdet vid behov för att visa poster.

   ![Filtrera för att hitta aktivitets loggar för virtuella Azure-säkerhetskopieringar](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Välj ett åtgärds namn för att se relevant information.
1. Välj **ny varnings regel** för att öppna sidan **Skapa regel** .
1. Skapa en avisering genom att följa stegen i [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Ny varningsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Här är resursen själva Recovery Services själva valvet. Upprepa samma steg för alla valv där du vill bli meddelad via aktivitets loggarna. Villkoret har ingen tröskel, period eller frekvens eftersom den här aviseringen baseras på händelser. Så snart den relevanta aktivitets loggen har genererats, utlöses aviseringen.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Använda Log Analytics för att övervaka i skala

Du kan visa alla aviseringar som skapats från aktivitets loggar och Log Analytics arbets ytor i Azure Monitor. Öppna bara **aviserings** rutan till vänster.

Även om du kan få aviseringar via aktivitets loggar rekommenderar vi starkt att du använder Log Analytics snarare än aktivitets loggar för övervakning i stor skala. Detta beror på följande:

- **Begränsade scenarier**: meddelanden via aktivitets loggar gäller endast för virtuella Azure-säkerhetskopieringar. Meddelandena måste konfigureras för varje Recovery Services valv.
- **Definitions passning**: den schemalagda säkerhets kopierings aktiviteten passar inte med den senaste definitionen av aktivitets loggarna. I stället justeras det med [resurs loggar](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace). Den här justeringen orsakar oväntade effekter när de data som flödar genom aktivitets loggs kanalen ändras.
- **Problem med aktivitets logg kanalen**: i Recovery Services valv följer aktivitets loggar som pumpas från Azure Backup en ny modell. Den här ändringen påverkar tyvärr genereringen av aktivitets loggar i Azure Government, Azure Germany och Azure Kina 21Vianet. Om användarna av dessa moln tjänster skapar eller konfigurerar aviseringar från aktivitets loggar i Azure Monitor utlöses inte aviseringarna. I alla offentliga Azure-regioner visas även dessa loggar om en användare [samlar in Recovery Services aktivitets loggar till en Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs).

Använd en Log Analytics arbets yta för övervakning och avisering i skala för alla arbets belastningar som skyddas av Azure Backup.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar anpassade frågor finns i [Log Analytics data modell](backup-azure-log-analytics-data-model.md).
