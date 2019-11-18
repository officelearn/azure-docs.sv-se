---
title: Övervaka SQL Data Sync med Azure Monitor loggar
description: Lär dig hur du övervakar Azure-SQL Data Sync med hjälp av Azure Monitor loggar
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114615"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Övervaka SQL Data Sync med Azure Monitor loggar 

Om du vill kontrol lera SQL Data Sync aktivitets logg och identifiera fel och varningar måste du tidigare kontrol lera SQL Data Sync manuellt i Azure Portal eller använda PowerShell eller REST API. Följ stegen i den här artikeln för att konfigurera en anpassad lösning som förbättrar övervaknings upplevelsen för data synkronisering. Du kan anpassa den här lösningen så att den passar ditt scenario.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Övervaknings instrument panel för alla dina Sync-grupper 

Du behöver inte längre Titta igenom loggarna för varje synkroniseringskoppling för att leta efter problem. Du kan övervaka alla dina Sync-grupper från alla dina prenumerationer på en plats med hjälp av en anpassad Azure Monitor vy. Den här vyn visar den information som är viktig för att SQL Data Sync kunder.

![Instrument panel för data synkronisering](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatiserade e-postaviseringar

Du behöver inte längre kontrol lera loggen manuellt i Azure Portal eller via PowerShell eller REST API. Med [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)kan du skapa aviseringar som går direkt till e-postadresserna för de personer som behöver se dem när ett fel uppstår.

![E-postaviseringar för data synkronisering](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hur konfigurerar du dessa övervakningsfunktioner? 

Implementera en anpassad Azure Monitors övervaknings lösning för SQL Data Sync på mindre än en timme genom att göra följande:

Du måste konfigurera tre komponenter:

-   En PowerShell-Runbook för att mata in SQL Data Sync loggdata till Azure Monitor loggar.

-   En Azure Monitor avisering för e-postaviseringar.

-   En Azure Monitor vy för övervakning.

### <a name="samples-to-download"></a>Exempel som ska hämtas

Hämta följande två exempel:

-   [Loggen för data synkronisering PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vyn data synkronisering Azure Monitor](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Krav

Kontrol lera att du har ställt in följande saker:

-   Ett Azure Automation konto

-   Log Analytics arbets yta

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-Runbook för att hämta SQL Data Sync logg 

Använd en PowerShell-Runbook som finns i Azure Automation för att hämta SQL Data Sync loggdata och skicka den till Azure Monitor loggar. Ett exempel skript ingår. Som en förutsättning måste du ha ett Azure Automation-konto. Sedan måste du skapa en Runbook och schemalägga den så att den körs. 

### <a name="create-a-runbook"></a>Skapa en runbook

Mer information om hur du skapar en Runbook finns i [min första PowerShell-Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Under ditt Azure Automation-konto väljer du fliken **Runbooks** under process automatisering.

2.  Välj **Lägg till en Runbook** i det övre vänstra hörnet på sidan Runbooks.

3.  Välj **Importera en befintlig Runbook**.

4.  Använd den aktuella `DataSyncLogPowerShellRunbook` filen under **Runbook-fil**. Ange **Runbook-typen** som `PowerShell`. Ge runbooken ett namn.

5.  Välj **Skapa**. Nu har du en Runbook.

6.  Under ditt Azure Automation-konto väljer du fliken **variabler** under delade resurser.

7.  Välj **Lägg till en variabel** på sidan variabler. Skapa en variabel för att lagra den senaste körnings tiden för runbooken. Om du har flera Runbooks behöver du en variabel för varje Runbook.

8.  Ange variabel namnet som `DataSyncLogLastUpdatedTime` och ange dess typ som DateTime.

9.  Välj Runbook och klicka på knappen Redigera överst på sidan.

10. Gör de ändringar som krävs för ditt konto och din SQL Data Sync konfiguration. (Mer detaljerad information finns i exempel skriptet.)

    1.  Azure-information.

    2.  Synkronisera grupp information.

    3.  Azure Monitor loggar information. Hitta den här informationen i Azure Portal | Inställningar | Anslutna källor. Mer information om hur du skickar data till Azure Monitor loggar finns i [skicka data till Azure Monitor loggar med HTTP-API för data insamling (för hands version)](../azure-monitor/platform/data-collector-api.md).

11. Kör runbooken i test fönstret. Kontrol lera att det lyckades.

    Om du har fel kontrollerar du att du har installerat den senaste versionen av PowerShell-modulen. Du kan installera den senaste PowerShell-modulen i **modulens Galleri** i ditt Automation-konto.

12. Klicka på **publicera**

### <a name="schedule-the-runbook"></a>Schemalägg Runbook

Så här schemalägger du en Runbook:

1.  Under runbooken väljer du fliken **scheman** under resurser.

2.  Välj **Lägg till ett schema** på sidan scheman.

3.  Välj **Länka ett schema till din Runbook**.

4.  Välj **skapa ett nytt schema.**

5.  Ställ in **upprepning** för återkommande och ange det intervall som du vill använda. Använd samma intervall här, i skriptet och i Azure Monitor loggar.

6.  Välj **Skapa**.

### <a name="check-the-automation"></a>Kontrol lera Automation

Om du vill övervaka om automatiseringen körs som förväntat går du till **Översikt** för ditt Automation-konto och letar upp vyn **jobb statistik** under **övervakning**. Fäst den här vyn på instrument panelen för enkel visning. Lyckade körningar av runbooken visas som slutförda och misslyckade körningar visas som "misslyckades".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Skapa en Azure Monitor läsar avisering för e-postmeddelanden

Om du vill skapa en avisering som använder Azure Monitor loggar gör du följande saker. Som en förutsättning måste du ha Azure Monitor loggar som är länkade till en Log Analytics arbets yta.

1.  I Azure Portal väljer du **loggs ökning**.

2.  Skapa en fråga för att välja fel och varningar efter synkroniseringsresursen inom det intervall som du har valt. Exempel:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  När du har kört frågan väljer du den klocka som står i **aviseringen**.

4.  Under **skapa avisering baserat på**väljer du **mått mått**.

    1.  Ange det sammanlagda värdet till **större än**.

    2.  Efter **större än**anger du tröskelvärdet innan du får meddelanden. Tillfälliga fel förväntas i datasynkronisering. Om du vill minska bruset ställer du in tröskelvärdet på 5.

5.  Under **åtgärder**anger du **e-postavisering** till "Ja". Ange önskade e-postmottagare.

6.  Klicka på **Save** (Spara). De angivna mottagarna får nu e-postaviseringar när fel inträffar.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Skapa en Azure Monitor vy för övervakning

Det här steget skapar en Azure Monitor vy för att visuellt övervaka alla angivna Sync-grupper. Vyn innehåller flera komponenter:

-   En översikts panel som visar hur många fel, lyckade och varningar som alla Sync-grupper har.

-   En panel för alla Sync-grupper som visar antalet fel och varningar per Sync-grupp. Grupper utan problem visas inte på den här panelen.

-   En panel för varje synkroniseringskoppling som visar antalet fel, lyckade och varningar och de senaste fel meddelandena.

Om du vill konfigurera vyn Azure Monitor gör du följande:

1.  På Start sidan för Log Analytics arbets ytan markerar du plus tecknet till vänster för att öppna **vyn designer**.

2.  Välj **Importera** i det övre fältet i vyns designer. Välj sedan exempel filen "DataSyncLogOMSView".

3.  Exemplet visar hur du hanterar två Sync-grupper. Redigera den här vyn så att den passar ditt scenario. Klicka på **Redigera** och gör följande ändringar:

    1.  Skapa nya "Ring & List"-objekt från galleriet efter behov.

    2.  Uppdatera frågorna med din information på varje panel.

        1.  På varje panel ändrar du TimeStamp_t intervall efter behov.

        2.  Uppdatera Sync-gruppnamnen på panelerna för varje Sync-grupp.

    3.  Uppdatera rubriken efter behov på varje panel.

4.  Klicka på **Spara** och vyn är klar.

## <a name="cost-of-this-solution"></a>Kostnad för den här lösningen

I de flesta fall är den här lösningen kostnads fri.

**Azure Automation:** Det kan uppstå en kostnad med Azure Automation kontot, beroende på din användning. De första 500 minuterna jobb körnings tiden per månad är kostnads fria. I de flesta fall förväntas den här lösningen använda mindre än 500 minuter per månad. Du kan undvika avgifter genom att schemalägga runbooken så att den körs vid ett intervall på två timmar eller mer. Mer information finns i avsnittet om [Automation-priser](https://azure.microsoft.com/pricing/details/automation/).

**Azure Monitor loggar:** Det kan finnas en kostnad som är kopplad till Azure Monitor loggar beroende på din användning. Den kostnads fria nivån omfattar 500 MB inmatade data per dag. I de flesta fall förväntas den här lösningen att mata in mindre än 500 MB per dag. Om du vill minska användningen använder du filtreringen endast vid filtrering som ingår i runbooken. Om du använder mer än 500 MB per dag uppgraderar du till den betalda nivån för att undvika risken för att analyser ska stoppas när begränsningen uppnås. Mer information finns i [priser för Azure Monitor loggar](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kodexempel

Hämta kod exemplen som beskrivs i den här artikeln från följande platser:

-   [Loggen för data synkronisering PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vyn data synkronisering Azure Monitor](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
