---
title: Övervaka Azure SQL Data Sync med Log Analytics | Microsoft Docs
description: Lär dig hur du övervakar Azure SQL Data Sync med Log Analytics
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 0a130613d6d2086fda55ca015b1c0ca77c909e3d
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684621"
---
# <a name="monitor-sql-data-sync-with-log-analytics"></a>Övervaka SQL Data Sync med Log Analytics 

För att kontrollera aktivitetsloggen SQL Data Sync och identifiera fel och varningar, var du tidigare tvungen att kontrollera SQL Data Sync manuellt i Azure portal eller Använd PowerShell eller REST API. Följ stegen i den här artikeln för att konfigurera en anpassad lösning som förbättrar datasynkronisering övervakningsupplevelse. Du kan anpassa den här lösningen för att passa ditt scenario.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Övervakningsinstrumentpanel för alla synkroniseringsgrupper 

Du behöver inte längre att söka igenom loggarna för varje Synkroniseringsgrupp individuellt för att leta efter problem. Du kan övervaka alla synkroniseringsgrupper från någon av dina prenumerationer på en plats med hjälp av en anpassad Log Analytics-vy. Den här vyn hämtar information som är viktiga för SQL Data Sync-kunder.

![Instrumentpanelen för data Sync](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatiserade e-postaviseringar

Du behöver inte längre Kontrollera loggen manuellt i Azure portal eller via PowerShell eller REST API. Med [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), du kan skapa aviseringar som går direkt till e-postadresserna för de personer som behöver se dem när ett fel uppstår.

![Data Sync e-postaviseringar](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hur ställer du in dessa övervakningsfunktioner? 

Implementera en anpassad logganalys övervakningslösning för SQL Data Sync på mindre än en timme genom att göra följande:

Du måste konfigurera tre komponenter:

-   En PowerShell-runbook för att mata in loggdata för SQL Data Sync till Log Analytics.

-   En avisering i Log Analytics för e-postmeddelanden.

-   En Log Analytics-vy för övervakning.

### <a name="samples-to-download"></a>Exempel för att ladda ned

Ladda ned följande två exempel:

-   [Data Sync Log PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Data Sync Log Analytics-vy](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har konfigurerat följande saker:

-   Ett Azure Automation-konto

-   Log Analytics Workspace

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-Runbook att hämta SQL Data Sync-logg 

Använd en PowerShell-runbook i Azure Automation för att hämta loggdata för SQL Data Sync och skicka den till Log Analytics. Ett exempelskript ingår. Du måste ha ett Azure Automation-konto som ett krav. Sedan måste du skapa en runbook och schemalägga den så att du kör. 

### <a name="create-a-runbook"></a>Skapa en runbook

Mer information om hur du skapar en runbook finns i [min första PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Under ditt Azure Automation-konto väljer du den **Runbooks** fliken under Processautomatisering.

2.  Välj **Lägg till en Runbook** i det övre vänstra hörnet av sidan för Runbooks.

3.  Välj **importera en befintlig Runbook**.

4.  Under **Runbook-filen**, använda den angivna `DataSyncLogPowerShellRunbook` fil. Ange den **runbooktyp** som `PowerShell`. Namnge runbook.

5.  Välj **Skapa**. Nu har du en runbook.

6.  Under ditt Azure Automation-konto väljer du den **variabler** fliken under delade resurser.

7.  Välj **Lägg till en variabel** på sidan variabler. Skapa en variabel för att lagra senaste körningstid för runbook. Om du har flera runbooks, behöver du en variabel för varje runbook.

8.  Ange variabelnamnet som `DataSyncLogLastUpdatedTime` och ange dess typ som DateTime.

9.  Välj runbook och klicka på Redigera-knappen längst ned på sidan.

10. Göra de ändringar som krävs för ditt konto och din SQL Data Sync-konfiguration. (Mer information finns i exempelskriptet.)

    1.  Information om Azure.

    2.  Synkroniseringsgruppen information.

    3.  Logga Analytics information. Hitta denna information i Azure Portal | Inställningar | Anslutna källor. Mer information om hur du skickar data till Log Analytics finns i [skicka data till Log Analytics med HTTP Data Collector API (förhandsversion)](../log-analytics/log-analytics-data-collector-api.md).

11. Kör en runbook i rutan. Kontrollera att det lyckades.

    Om du har fel kan du kontrollera att du har den senaste PowerShell-modulen installerad. Du kan installera den senaste PowerShell-modulen i den **Modulgalleri** i ditt Automation-konto.

12. Klicka på **publicera**

### <a name="schedule-the-runbook"></a>Schemalägg runbook

Så här schemalägger runbook:

1.  Under runbook, Välj den **scheman** fliken under resurser.

2.  Välj **lägga till ett schema** på sidan scheman.

3.  Välj **länka ett schema till din runbook**.

4.  Välj **skapa ett nytt schema.**

5.  Ange **upprepning** återkommande och ange intervallet du vill. Använd samma intervall här i skriptet och i Log Analytics.

6.  Välj **Skapa**.

### <a name="check-the-automation"></a>Kontrollera automation

Övervaka om ditt automation körs som förväntat under **översikt** för ditt automation-konto, hitta den **Jobbstatistik** visa **övervakning**. Fäst vyn på instrumentpanelen för enkel visning. Lyckade körningar av runbook-Visa som ”slutförd” och misslyckade körningar visas som ”misslyckades”.

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>Skapa en avisering i Log Analytics Reader för e-postaviseringar

Om du vill skapa en avisering som använder Log Analytics, gör du följande. Du måste ha Log Analytics som är kopplad till en Log Analytics-arbetsyta som ett krav.

1.  I Azure-portalen väljer du **Loggsökning**.

2.  Skapa en fråga för att välja fel och varningar av synkroniseringsgruppen inom det intervallet som du har valt. Exempel:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  När du kör frågan väljer du på klockan där det står **avisering**.

4.  Under **generera avisering baserat på**väljer **mått mätning**.

    1.  Ställ in det sammanlagda värdet **är större än**.

    2.  Efter **är större än**, ange tröskelvärdet för att vara inaktiv innan du tar emot meddelanden. Tillfälliga fel förväntas i datasynkronisering. Ange tröskelvärdet till 5 för att minska bruset.

5.  Under **åtgärder**anger **e-postavisering** till ”Yes”. Ange de önskade e-postmottagare.

6.  Klicka på **Spara**. De angivna mottagarna nu ta emot e-postaviseringar när fel uppstår.

## <a name="create-a-log-analytics-view-for-monitoring"></a>Skapa en Log Analytics-vy för övervakning

Det här steget skapar en Log Analytics-vy för att visuellt övervaka alla angivna synkroniseringsgrupper. Vyn innehåller flera komponenter:

-   En översiktsikon som visar hur många fel, lyckade och varningar har alla synkroniseringsgrupper.

-   En panel för alla synkroniseringsgrupper, som visar antalet fel och varningar per synkroniseringsgrupp. Grupper utan problem visas inte på den här panelen.

-   En panel för varje Synkroniseringsgrupp som visar antalet fel, har slutförts, och varningar och de senaste felmeddelandena.

Om du vill konfigurera Log Analytics-vy, gör du följande:

1.  På sidan Log Analytics, väljer du plus till vänster och öppna den **Vydesigner**.

2.  Välj **Import** i det översta fältet i view Designer. Välj sedan exempelfilen ”DataSyncLogOMSView”.

3.  Exempelvy är för att hantera två synkroniseringsgrupper. Redigera den här vyn så att de passar ditt scenario. Klicka på **redigera** och gör följande ändringar:

    1.  Skapa nya ”ringdiagram och lista” objekt från galleriet efter behov.

    2.  Uppdatera frågorna med din information i varje panel.

        1.  Ändra TimeStamp_t intervallet efter behov i varje panel.

        2.  Uppdatera Synkroniseringsgruppen namnen på paneler för varje Synkroniseringsgrupp.

    3.  Uppdatera rubriken i varje panel efter behov.

4.  Klicka på **spara** och vyn är redo.

## <a name="cost-of-this-solution"></a>Kostnaden för den här lösningen

Den här lösningen är kostnadsfri i de flesta fall.

**Azure Automation:** det kan finnas en kostnader som uppstår med Azure Automation-kontot, beroende på din användning. De första 500 minuterna uppgiftskörtid per månad är kostnadsfria. I de flesta fall förväntas i den här lösningen använder mindre än 500 minuter per månad. Om du vill undvika kostnader genom att schemalägga att runbook körs vid ett intervall av två timmar eller mer. Mer information finns i [Automation-prissättning](https://azure.microsoft.com/pricing/details/automation/).

**Logganalys:** det kan finnas en kostnad som hör till Log Analytics beroende på din användning. Den kostnadsfria nivån omfattar 500 MB inmatade data per dag. I de flesta fall förväntas i den här lösningen mata in mindre än 500 MB per dag. Använd endast fel filtrering ingår i runbook för att minska användningen. Om du använder mer än 500 MB per dag, uppgradera till den kostnadsfria nivån för att undvika risken för analytics stoppas när begränsningen har uppnåtts. Mer information finns i [priserna för Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kodexempel

Ladda ned kodexemplen i den här artikeln från följande platser:

-   [Data Sync Log PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Data Sync Log Analytics-vy](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera datasynkronisering
    - I portalen – [självstudie: Ställ in SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data synkroniseras Agent - [Data synkroniseras Agent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Felsök - [felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL - [automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell - [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
