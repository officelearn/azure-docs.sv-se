---
title: 'Azure Backup: Övervaka Azure Backup med Azure Monitor'
description: Övervaka Azure Backup-arbetsbelastningar och skapa anpassade varningar genom att använda Azure Monitor.
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; Aviseringar; Diagnostikinställningar; Åtgärdsgrupper
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786315"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Övervaka i skala med hjälp av Azure Monitor

Azure Backup innehåller [inbyggd övervakning och aviseringsfunktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan någon infrastruktur med ytterligare hantering. Men den här inbyggda tjänsten är begränsad i följande scenarier:

- Om du vill övervaka data från flera Recovery Services-valv mellan prenumerationer
- Om den prioriterade meddelandekanalen är *inte* e-post
- Om användarna vill ha aviseringar för fler scenarier
- Om du vill visa information från en lokal komponent, till exempel System Center Data Protection Manager i Azure, som inte visar portalen i [ **säkerhetskopieringsjobb** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) eller [  **Säkerhetskopieringsaviseringar**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Använda Log Analytics-arbetsyta

> [!NOTE]
> Data från Virtuella Azure-säkerhetskopieringar, Azure Backup-agenten, System Center Data Protection Manager, SQL-säkerhetskopior i Azure virtuella datorer och Azure Files säkerhetskopior av filresurser är läggs in till arbetsytan Log Analytics via diagnostikinställningar. 

Om du vill övervaka i skala, behöver du funktionerna i två Azure-tjänster. *Diagnostikinställningar* skicka data från flera Azure Resource Manager-resurser till en annan resurs. *Log Analytics* genererar anpassade aviseringar som där du kan använda åtgärdsgrupper för att definiera andra meddelandekanaler. 

I följande avsnitt förklarar vi hur du använder Log Analytics för att övervaka Azure Backup i skala.

### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Azure Resource Manager-resurser, till exempel Recovery Services-valvet registrera information om schemalagda åtgärder och användare som utlöste åtgärder som diagnostikdata. 

I avsnittet övervakning väljer **diagnostikinställningar** och ange mål för Recovery Services-valvet diagnostikdata.

![Diagnostikinställning för Recovery Services-valvet, riktar in sig på Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Du kan rikta en Log Analytics-arbetsyta från en annan prenumeration. Välj samma Log Analytics-arbetsytan för flera Recovery Services-valv för att övervaka valv mellan prenumerationer på en och samma plats. Om du vill channel all information som är relaterade till Azure Backup till Log Analytics-arbetsytan, Välj **AzureBackupReport** som loggen.

> [!IMPORTANT]
> När du har slutfört konfigurationen ska du vänta 24 timmar för ursprungliga data-push-installation att slutföra. Efter som inledande data-push, alla händelser skickas som beskrivs senare i den här artikeln i den [frekvens avsnittet](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Distribuera en lösning till Log Analytics-arbetsytan

När data som finns i Log Analytics-arbetsytan [distribuera en mall för GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) till Log Analytics för att visualisera data. För att korrekt identifiera arbetsytan, kontrollera att du ger det i samma resursgrupp, namn på arbetsyta och arbetsytan, position. Installera sedan den här mallen på arbetsytan.

> [!NOTE]
> Om du inte har aviseringar, jobb och återställningsjobb i Log Analytics-arbetsytan kan se du en ”BadArgumentError” felkod i portalen. Ignorera felet och fortsätta att använda lösningen. När startar skriver du relevanta data flödar till arbetsytan visualiseringar visas samma och du längre se inte felet.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Visa data i Azure Backup med hjälp av Log Analytics

När mallen distribueras visas lösningen för övervakning av Azure Backup i den sammanfattande arbetsyteregion. Om du vill gå till sammanfattningen, gör du något av dessa sökvägar:

- **Azure Monitor**: I den **Insights** väljer **mer** och sedan väljer du relevant arbetsyta.
- **Log Analytics-arbetsytor**: Väljer du relevant arbetsyta och sedan under **Allmänt**väljer **arbetsytan Sammanfattning**.

![Log Analytics-ikonen för övervakning](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

När du väljer panelen övervakning öppnas designer mallen en serie diagram om grundläggande övervakningsdata från Azure Backup. Här följer några av diagrammen som visas:

* Alla säkerhetskopieringsjobb

   ![Log Analytics-diagram för säkerhetskopieringsjobb](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Återställningsjobb

   ![Log Analytics graph för återställningsjobb](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Inbyggda Azure Backup-aviseringar för Azure-resurser

   ![Log Analytics graph för inbyggda Azure Backup-aviseringar för Azure-resurser](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Inbyggda Azure Backup-aviseringar för lokala resurser

   ![Log Analytics graph för inbyggd Azure Backup-aviseringar för lokala resurser](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Aktiva datakällorna

   ![Log Analytics graph för aktiva säkerhetskopierade entiteter](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Recovery Services-valv molnlagring

   ![Log Analytics-diagram för molnlagring för Recovery Services-valv](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Dessa diagram tillhandahålls med hjälp av mallen. Du kan redigera diagram eller lägga till flera diagram om du behöver.

> [!IMPORTANT]
> När du distribuerar mallen skapar du i stort sett ett skrivskyddat Lås. Du måste ta bort låset för att redigera och spara mallen. Du kan ta bort ett lås i den **inställningar** avsnittet i Log Analytics-arbetsytan, på den **låser** fönstret.

### <a name="create-alerts-by-using-log-analytics"></a>Skapa aviseringar med hjälp av Log Analytics

I Azure Monitor kan skapa du dina egna aviseringar i Log Analytics-arbetsytan. I arbetsytan kan du använda *Azure åtgärdsgrupper* och välj din önskade sätt. 

> [!IMPORTANT]
> Information om kostnaden för att skapa den här frågan finns i [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Välj något av diagrammen för att öppna den **loggar** avsnittet i Log Analytics-arbetsytan. I den **loggar** redigerar frågorna och skapa aviseringar för dem.

![Skapa en avisering i Log Analytics-arbetsytan](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

När du väljer **ny Aviseringsregel**, öppnas sidan Azure Monitor skapande av avisering som visas i följande bild. Här resursen har redan markerats som Log Analytics-arbetsytan och åtgärden gruppen integration har angetts.

![Sidan för Log Analytics-avisering skapas](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Aviseringstillståndet

Definiera egenskap för en avisering är dess utlösande tillståndet. Välj **villkor** att läsa in Kusto-fråga på den **loggar** sidan som visas i följande bild. Här kan du redigera villkoret som passar dina behov. Mer information finns i [exempel Kusto frågor](#sample-kusto-queries).

![Hur du konfigurerar en varningsvillkor](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Om det behövs kan du redigera Kusto-fråga. Välj ett tröskelvärde, punkt och frekvens. Tröskelvärdet avgör när aviseringen aktiveras. Perioden är tidsperiod som frågan körs. Till exempel om tröskelvärdet är större än 0, perioden är 5 minuter och frekvensen är 5 minuter, körs sedan regeln frågan var femte minut, granska de tidigare fem minuterna. Om antalet resultat som är större än 0, meddelas du via den valda åtgärdsgruppen.

#### <a name="alert-action-groups"></a>Aviseringar åtgärdsgrupper

Använd en åtgärdsgrupp för att ange en meddelandekanal. Se tillgängliga notification-mekanismer under **åtgärdsgrupper**väljer **Skapa ny**.

![Tillgängliga meddelande mekanismer i fönstret ”Lägg till åtgärdsgrupp”](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Du kan uppfylla alla aviseringar och Övervakningskrav från Log Analytics ensam eller du kan använda Log Analytics för att komplettera inbyggda aviseringar.

Mer information finns i [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [skapa och hantera åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Exempelfrågor för Kusto

Standarddiagram ger dig Kusto-frågor för grundläggande scenarier där du kan skapa aviseringar. Du kan också ändra frågor för att hämta de data du vill bli aviserad om. Klistra in följande exempel Kusto frågorna i den **loggar** sidan och skapa aviseringar för frågorna som:

* Alla lyckade säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Alla misslyckade säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Alla slutförda jobb som används för att säkerhetskopiera virtuella Azure-datorer

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

* Alla lyckade SQL log säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

* Alla lyckade Azure Backup agent-jobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>Uppdateringsfrekvensen för diagnostikdata

Diagnostikdata från valvet är läggs in till Log Analytics-arbetsytan med vissa fördröjning. Varje händelse anländer till Log Analytics-arbetsytan *20 till 30 minuter* när det skickas från Recovery Services-valvet. Här finns ytterligare information om fördröjningen:

- Tjänsten backup inbyggda aviseringar skickas över alla lösningar när de skapas. Så att de visas vanligtvis i Log Analytics-arbetsytan efter 20 till 30 minuter.
- Över alla lösningar, säkerhetskopiera ad hoc-jobb och återställningsjobb skickas så snart som de *Slutför*.
- För alla lösningar utom SQL-säkerhetskopiering schemalagda säkerhetskopieringsjobb skickas så snart som de *Slutför*.
- För SQL-säkerhetskopiering eftersom loggsäkerhetskopior kan inträffa varje kvart information för alla slutförda schemalagda säkerhetskopieringsjobb, inklusive loggar, batchar och push-överfört var sjätte timme.
- I alla lösningar annan information, till exempel säkerhetskopieringsobjekt, princip, återställningspunkter, lagring och så vidare skickas minst *en gång per dag.*
- En ändring i konfigurationen för säkerhetskopiering (till exempel ändra principen eller redigera principen) utlöser en säkerhetskopierad information för alla relaterade-push.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Med hjälp av aktivitetsloggar för Recovery Services-valvet

> [!CAUTION]
> Följande steg gäller endast för *Virtuella Azure-säkerhetskopieringar.* Du kan inte använda de här stegen för lösningar som Azure Backup-agenten, SQL-säkerhetskopior i Azure eller Azure Files.

Du kan också använda aktivitetsloggar för att få meddelande för händelser, till exempel lyckade. Börja genom att följa dessa steg:

1. Logga in på Azure-portalen.
1. Öppna relevanta Recovery Services-valvet. 
1. Öppna i valvegenskaper, den **aktivitetsloggen** avsnittet.

Identifiera relevant logg och skapa en avisering:

1. Kontrollera att du har fått aktivitetsloggar för säkerhetskopiering genom att tillämpa filter som visas i följande bild. Ändra den **Timespan** värdet som behövs för att visa poster.

   ![Filtrera för att hitta några aktivitetsloggar för Virtuella Azure-säkerhetskopieringar](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Välj Åtgärdsnamnet kan se vilka relevanta.
1. Välj **ny aviseringsregel** att öppna den **skapa regeln** sidan. 
1. Skapa en avisering genom att följa stegen i [skapa, visa och hantera aviseringar för aktivitetsloggar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Ny aviseringsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Här är resursen i Recovery Services-valvet. Du måste upprepa samma steg för alla valv där du vill meddelas via aktivitetsloggar. Villkoret har inte ett tröskelvärde, punkt eller frekvens eftersom den här aviseringen är baserad på händelser. Varningen aktiveras när relevanta aktivitetsloggen genereras.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Använda Log Analytics för att övervaka i skala

Du kan visa alla aviseringar som aktiveras från aktivitetsloggar och Log Analytics-arbetsytor i Azure Monitor. Bara öppna den **aviseringar** fönstret till vänster.

Även om du får meddelanden via aktivitetsloggar måste vi rekommenderar starkt att med hjälp av Log Analytics i stället för aktivitetsloggar för att övervaka i skala. Här är anledningen:

- **Begränsade scenarier**: Meddelanden via aktivitetsloggar gäller endast för Virtuella Azure-säkerhetskopieringar. Meddelanden måste ställas in för varje Recovery Services-valvet.
- **Definition av passar**: Schemalagd säkerhetskopiering aktiviteten inte får plats med de senaste definitionerna av aktivitetsloggar. I stället det överensstämmer med [diagnostikloggar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Den här justering orsakar oväntade effekter när de data som flödar genom aktiviteten logga kanaländringar i.
- **Problem med den aktivitet logg för driftshändelsekanal**: I Recovery Services-valv följer aktivitetsloggar som är läggs in från Azure Backup en ny modell. Den här ändringen påverkar tyvärr generering av aktivitetsloggar i Azure Government, Azure Tyskland och Azure Kina 21Vianet. Om användare av dessa molntjänster skapa eller konfigurera några aviseringar från aktivitetsloggar i Azure Monitor, aktiveras inte aviseringarna som. I alla offentliga Azure-regioner, om en användare [samlar in Recovery Services-aktivitetsloggar till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), visas inte dessa loggar.

Använd Log Analytics-arbetsytan för övervakning och avisering i skala för alla arbetsbelastningar som skyddas av Azure Backup.

## <a name="next-steps"></a>Nästa steg

För att skapa egna frågor, se [Log Analytics-datamodell](backup-azure-log-analytics-data-model.md).
