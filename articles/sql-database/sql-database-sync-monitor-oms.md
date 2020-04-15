---
title: Övervaka SQL Data Sync med Azure Monitor-loggar
description: Lär dig hur du övervakar Azure SQL Data Sync med hjälp av Azure Monitor-loggar
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 5f5980f74b24cd972d43e9b05d4a5d623e6e3d2f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383693"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Övervaka SQL Data Sync med Azure Monitor-loggar 

För att kontrollera SQL Data Sync-aktivitetsloggen och identifiera fel och varningar var du tidigare tvungen att kontrollera SQL Data Sync manuellt i Azure-portalen eller använda PowerShell eller REST API. Följ stegen i den här artikeln för att konfigurera en anpassad lösning som förbättrar övervakningsupplevelsen för datasynkronisering. Du kan anpassa den här lösningen så att den passar ditt scenario.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Övervakningsinstrumentpanel för alla dina synkroniseringsgrupper 

Du behöver inte längre titta igenom loggarna för varje synkroniseringsgrupp individuellt för att leta efter problem. Du kan övervaka alla dina synkroniseringsgrupper från någon av dina prenumerationer på ett ställe med hjälp av en anpassad Azure Monitor-vy. I den här vyn visas den information som är viktig för SQL Data Sync-kunder.

![Instrumentpanel för övervakning av datasynkronisering](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatiska e-postmeddelanden

Du behöver inte längre kontrollera loggen manuellt i Azure-portalen eller via PowerShell eller REST API. Med [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)kan du skapa aviseringar som går direkt till e-postadresserna till de personer som behöver se dem när ett fel inträffar.

![E-postmeddelanden om datasynkronisering](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Hur ställer du in dessa övervakningsfunktioner? 

Implementera en anpassad Azure Monitor-loggövervakningslösning för SQL Data Sync på mindre än en timme genom att göra följande saker:

Du måste konfigurera tre komponenter:

-   En PowerShell-runbook för att mata SQL Data Sync-loggdata till Azure Monitor-loggar.

-   En Azure Monitor-avisering för e-postmeddelanden.

-   En Azure Monitor View för övervakning.

### <a name="samples-to-download"></a>Exempel att ladda ner

Ladda ner följande två exempel:

-   [PowerShell-runbook för datasynkroniseringslogg](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure-övervakarvy för datasynkronisering](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Krav

Kontrollera att du har ställt in följande saker:

-   Ett Azure Automation-konto

-   Log Analytics-arbetsyta

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell Runbook för att hämta SQL Data Sync Log 

Använd en PowerShell-runbook som finns i Azure Automation för att hämta SQL Data Sync-loggdata och skicka den till Azure Monitor-loggar. Ett exempelskript ingår. Som en förutsättning måste du ha ett Azure Automation-konto. Då måste du skapa en runbook och schemalägga den så att den körs. 

### <a name="create-a-runbook"></a>Skapa en runbook

Mer information om hur du skapar en runbook finns i [Min första PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Under ditt Azure Automation-konto väljer du fliken **Runbooks** under Process Automation.

2.  Välj **Lägg till en runbook** längst upp till vänster på sidan Runbooks.

3.  Välj **Importera en befintlig Runbook**.

4.  Använd den angivna `DataSyncLogPowerShellRunbook` filen under **Runbook-fil.** Ange **runbook-typen** som `PowerShell`. Ge runbooken ett namn.

5.  Välj **Skapa**. Du har nu en runbook.

6.  Under ditt Azure Automation-konto väljer du fliken **Variabler** under Delade resurser.

7.  Välj **Lägg till en variabel** på sidan Variabler. Skapa en variabel för att lagra den senaste körningstiden för runbooken. Om du har flera runbooks behöver du en variabel för varje runbook.

8.  Ange variabelnamnet `DataSyncLogLastUpdatedTime` som och ange dess typ som DateTime.

9.  Markera runbooken och klicka på redigeringsknappen högst upp på sidan.

10. Gör de ändringar som krävs för ditt konto och din SQL Data Sync-konfiguration. (Mer detaljerad information finns i exempelskriptet.)

    1.  Azure-information.

    2.  Synkronisera gruppinformation.

    3.  Azure Monitor loggar information. Hitta den här informationen i Azure Portal | Inställningar | Anslutna källor. Mer information om hur du skickar data till Azure Monitor-loggar finns i [Skicka data till Azure Monitor-loggar med HTTP Data Collector API (förhandsversion)](../azure-monitor/platform/data-collector-api.md).

11. Kör runbooken i testfönstret. Kontrollera att det lyckades.

    Om du har fel kontrollerar du att du har den senaste PowerShell-modulen installerad. Du kan installera den senaste PowerShell-modulen i **modulens galleri** i ditt Automation-konto.

12. Klicka på **Publicera**

### <a name="schedule-the-runbook"></a>Schemalägg runbooken

Så här schemalägger du runbooken:

1.  Under runbooken väljer du fliken **Scheman** under Resurser.

2.  Välj **Lägg till ett schema** på sidan Scheman.

3.  Välj **Länka ett schema till din runbook**.

4.  Välj **Skapa ett nytt schema.**

5.  Ange **Återkommande** till Återkommande och ange önskat intervall. Använd samma intervall här, i skriptet och i Azure Monitor-loggar.

6.  Välj **Skapa**.

### <a name="check-the-automation"></a>Kontrollera automatiseringen

Om du vill övervaka om automatiseringen körs som förväntat hittar du vyn Jobbstatistik under **Övervakning**under **Översikt** för ditt **automatiseringskonto** . Fäst den här vyn på instrumentpanelen för enkel visning. Lyckade körningar av runbook-showen som "Slutförd" och Misslyckade körningar visas som "Misslyckades".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Skapa en Avisering om Azure Monitor Reader för e-postaviseringar

Om du vill skapa en avisering som använder Azure Monitor-loggar gör du följande saker. Som en förutsättning måste du ha Azure Monitor-loggar kopplade till en Log Analytics-arbetsyta.

1.  Välj **Logga sök**i Azure-portalen .

2.  Skapa en fråga för att välja fel och varningar efter synkroniseringsgrupp inom det intervall du valde. Ett exempel:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  När du har kört frågan väljer du klockan som säger **Alert**.

4.  Under **Generera avisering baserat på**väljer du **Måttmått**.

    1.  Ange det sammanlagda värdet till **Större än**.

    2.  När **du har större än**anger du tröskelvärdet för att förflyta innan du får aviseringar. Tillfälliga fel förväntas i Datasynkronisering. Om du vill minska bullret ställer du in tröskelvärdet till 5.

5.  Under **Åtgärder**anger du **E-postmeddelande** till "Ja". Ange önskade e-postmottagare.

6.  Klicka på **Spara**. De angivna mottagarna får nu e-postmeddelanden när fel uppstår.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Skapa en Azure Monitor-vy för övervakning

I det här steget skapas en Azure Monitor-vy för att visuellt övervaka alla angivna synkroniseringsgrupper. Vyn innehåller flera komponenter:

-   En översiktspanel som visar hur många fel, lyckade och varningar som alla synkroniseringsgrupper har.

-   En panel för alla synkroniseringsgrupper, som visar antalet fel och varningar per synkroniseringsgrupp. Grupper utan problem visas inte på den här panelen.

-   En panel för varje synkroniseringsgrupp, som visar antalet fel, lyckade och varningar samt de senaste felmeddelandena.

Så här konfigurerar du vyn Azure Monitor:

1.  På startsidan för Logganalysarbetsyta väljer du pluset till vänster för att öppna **vydesignern**.

2.  Välj **Importera** i det övre fältet i vydesignern. Välj sedan exempelfilen "DataSyncLogOMSView".

3.  Exempelvyn är till för hantering av två synkroniseringsgrupper. Redigera den här vyn så att den passar ditt scenario. Klicka på **Redigera** och gör följande ändringar:

    1.  Skapa nya "Donut & List" objekt från galleriet efter behov.

    2.  Uppdatera frågorna med informationen på varje panel.

        1.  Ändra TimeStamp_t intervall på varje panel efter behov.

        2.  Uppdatera namn på synkroniseringsgruppen på panelerna för varje synkroniseringsgrupp.

    3.  Uppdatera rubriken efter behov på varje panel.

4.  Klicka på **Spara** så är vyn klar.

## <a name="cost-of-this-solution"></a>Kostnaden för denna lösning

I de flesta fall är denna lösning gratis.

**Azure Automation:** Det kan finnas en kostnad för Azure Automation-kontot, beroende på din användning. De första 500 minuterna av jobbet kör tid per månad är gratis. I de flesta fall förväntas den här lösningen använda mindre än 500 minuter per månad. Om du vill undvika avgifter schemalägger du runbooken så att den körs med två timmar eller mer. Mer information finns i [Priser för Automatisering](https://azure.microsoft.com/pricing/details/automation/).

**Azure Monitor-loggar:** Det kan finnas en kostnad som är associerad med Azure Monitor-loggar beroende på din användning. Den kostnadsfria nivån innehåller 500 MB introllade data per dag. I de flesta fall förväntas den här lösningen inta mindre än 500 MB per dag. Om du vill minska användningen använder du filtreringen med endast fel som ingår i runbooken. Om du använder mer än 500 MB per dag uppgraderar du till den betalda nivån för att undvika risken för att analytics stoppas när begränsningen nås. Mer information finns i [Azure Monitor loggar priser](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kodexempel

Hämta kodexemplen som beskrivs i den här artikeln från följande platser:

-   [PowerShell-runbook för datasynkroniseringslogg](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Azure-övervakarvy för datasynkronisering](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

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
