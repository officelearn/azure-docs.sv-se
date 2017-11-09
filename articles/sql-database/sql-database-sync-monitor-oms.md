---
title: "Övervaka Azure SQL datasynkronisering med OMS Log Analytics | Microsoft Docs"
description: "Lär dig hur du övervakar Azure SQL Data Sync med hjälp av OMS logganalys"
services: sql-database
ms.date: 11/7/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: e1099d2cd7eeccbe76d762028a0c5d5f95f53026
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-azure-sql-data-sync-preview-with-oms-log-analytics"></a>Övervaka Azure SQL-datasynkronisering (förhandsversion) med OMS logganalys 

I aktivitetsloggen SQL datasynkronisering och identifiera fel och varningar genom tvungen du tidigare att kontrollera SQL datasynkronisering manuellt i Azure-portalen eller Använd PowerShell eller REST API. Följ stegen i den här artikeln för att konfigurera en anpassad lösning som förbättrar datasynkronisering övervakning upplevelse. Du kan anpassa den här lösningen så att den passar din situation.

En översikt över SQL datasynkronisering finns [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Instrumentpanelen för övervakning för alla synkroniseringsgrupper 

Du behöver inte längre att söka igenom loggarna för varje synkronisering grupp individuellt för att leta efter problem. Du kan övervaka alla synkroniseringsgrupper från någon av dina prenumerationer på en plats med hjälp av en anpassad vy i OMS (Operations Management Suite). Den här vyn hämtar information som är viktiga för datasynkronisering för SQL-kunder.

![Data instrumentpanelen för övervakning av synkronisering](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.jpg)

## <a name="automated-email-notifications"></a>Automatisk e-postaviseringar

Du behöver inte längre att kontrollera loggen manuellt i Azure-portalen eller via PowerShell eller REST API. Genom att utnyttja [OMS logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), du kan skapa varningar som gå direkt till de e-postadresserna för de som måste finns i händelse av ett fel.

![Data synkronisera e-postaviseringar](media/sql-database-sync-monitor-oms/sync-email-notifications.jpg)

## <a name="how-do-you-set-this-up"></a>Hur du konfigurerar detta? 

Implementera en anpassad OMS övervakningslösning för synkronisering för SQL-Data på mindre än en timme genom att göra följande saker.

Du behöver konfigurera 3 komponenter:

-   En PowerShell-runbook att mata in SQL datasynkronisering loggdata till OMS.

-   En OMS logganalys avisering för e-postmeddelanden.

-   En OMS-vy för övervakning.

### <a name="samples-to-download"></a>Exempel för att hämta

Hämta följande två exemplen:

-   [Data synkroniseras loggen PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Synkronisera loggen OMS datavy](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Krav

Kontrollera att du har ställt in följande saker:

-   Ett Azure Automation-konto

-   Logganalys kopplad till OMS-arbetsyta

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell-Runbook att hämta SQL Data Sync-logg 

Använd en PowerShell-runbook finns i Azure Automation för att dra loggdata datasynkronisering SQL och skicka den till OMS. Ett exempelskript ingår. Du måste ha en Azure Automation-konto som ett krav. Du måste skapa en runbook och schemalägga den körs. 

### <a name="create-a-runbook"></a>Skapa en runbook

Mer information om hur du skapar en runbook finns [min första PowerShell-runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Under Azure Automation-konto markerar du den **Runbooks** Processautomatisering på fliken.

2.  Välj **lägga till en Runbooks** i det övre vänstra hörnet på sidan Runbooks.

3.  Välj **importera en befintlig Runbook**.

4.  Under **Runbook-filen**, använder den angivna `DataSyncLogPowerShellRunbook` fil. Ange den **runbooktyp** som `PowerShell`. Namnge runbook.

5.  Välj **Skapa**. Nu har du en runbook.

6.  Under Azure Automation-kontot väljer du den **variabler** fliken under delade resurser.

7.  Välj **lägga till en variabel** på sidan variabler. Vi behöver skapa en variabel för att lagra den senaste körningstiden för runbook. Om du har flera runbooks, behöver du en variabel för varje runbook.

8.  Ange variabelnamnet som `DataSyncLogLastUpdatedTime` och ange typen DateTime.

9.  Välj runbook och klicka på redigeringsknappen överst på sidan.

10. Gör de ändringar som krävs för ditt konto och konfigurationen av SQL-datasynkronisering. (Se exempelskript mer detaljerad information.)

    1.  Azure information.

    2.  Information om synkronisering av grupp.

    3.  OMS-information. Hitta den här informationen på OMS-portalen | Inställningar | Anslutna källor. Mer information om hur du skickar data till logganalys finns [skicka data till logganalys med HTTP-Data Collector API (förhandsversion)](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).

11. Köra runbook i rutan. Kontrollera att det lyckades.

    Om du har fel, kontrollera att du har den senaste PowerShell module installerad. Du kan göra detta den **moduler galleriet** i Automation-kontot.

12. Klicka på **publicera**

### <a name="schedule-the-runbook"></a>Schemat för runbook

Så här schemalägger runbook:

1.  Under runbook, Välj den **scheman** på fliken resurser.

2.  Välj **lägga till ett schema** på sidan scheman.

3.  Välj **länka ett schema till din runbook**.

4.  Välj **skapa ett nytt schema.**

5.  Ange **återkommande** återkommande och ange intervallet ska. Använd samma intervall här, i skriptet och i OMS.

6.  Välj **Skapa**.

### <a name="check-the-automation"></a>Kontrollera automatisering

Övervaka om ditt automation körs som förväntat, under **översikt** ditt automation-konto kan hitta den **jobbet statistik** visa under **övervakning**. Fäst det på instrumentpanelen för enkel visning. Lyckad körningar av runbook visas som ”slutförd” och misslyckades körs visas som ”misslyckades”.

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Skapa en OMS Log Reader avisering för e-postaviseringar

Om du vill skapa en avisering som använder OMS logganalys, göra följande. En förutsättning, som du behöver ha logganalys kopplad till en OMS-arbetsyta.

1.  Välj i OMS-portalen **loggen Sök**.

2.  Skapa en fråga för att välja fel och varningar av sync grupp inom det intervall som du har valt. Exempel:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  När du har kört frågan Välj bell som säger **avisering**.

4.  Under **generera avisering baserat på**väljer **mått mätning**.

    1.  Anger det sammanlagda värdet **större än**.

    2.  Efter **större än**, ange tröskelvärdet för att vara inaktiv innan du får meddelanden. Tillfälliga fel förväntas i datasynkronisering. Vi rekommenderar att du anger tröskelvärdet till 5 för att minska bruset.

5.  Under **åtgärder**, ange **e-postavisering** ”yes”. Ange de önskade e-postmottagare.

6.  Klicka på **Spara**. De angivna mottagarna ta emot e-postaviseringar när fel uppstår.

## <a name="create-an-oms-view-for-monitoring"></a>Skapa en OMS-vy för övervakning

Det här steget skapar en OMS-vy för att övervaka alla angivna synkroniseringsgrupper visuellt. Vyn innehåller flera komponenter:

-   En översiktsikon som visar hur många fel, lyckade och varningar grupperna för synkronisering har.

-   En panel för alla synkroniseringsgrupper som visar antalet fel och varningar per sync-grupp. Grupper utan problem visas inte på den här panelen.

-   En panel för varje Sync-grupp som visar antalet fel, har slutförts, och varningar och de senaste felmeddelandena.

Om du vill konfigurera OMS-vy, kan du göra följande:

1.  På startsidan OMS väljer plus till vänster för att öppna den **Vydesigner**.

2.  Välj **importera** i det översta fältet i view Designer. Välj sedan exempelfilen ”DataSyncLogOMSView”.

3.  Exempelvyn är för att hantera två synkroniseringsgrupper. Redigera den här vyn så att de passar din situation. Klicka på **redigera** och gör följande ändringar:

    1.  Skapa nya ”Ring & lista” objekt från galleriet efter behov.

    2.  Uppdatera frågorna med din information i varje bricka.

        1.  Ändra TimeStamp_t intervall enligt önskemål på varje bricka.

        2.  Uppdatera Sync gruppnamn på panelerna för varje grupp med synkronisering.

    3.  Onn varje panelen, uppdatera titeln efter behov.

4.  Klicka på **spara** och vyn är klar.

## <a name="cost-of-this-solution"></a>Kostnaden för den här lösningen

Den här lösningen är gratis i de flesta fall.

**Azure Automation:** det kan finnas en kostnaden med Azure Automation-konto, beroende på din användning. Första 500 minut jobbkörningstid per månad är gratis. I de flesta fall förväntas den här lösningen använder mindre än 500 minuter per månad. För att undvika kostnader, schemalägga en runbook körs vid ett intervall på två timmar eller mer. Mer information finns i [Automation priser](https://azure.microsoft.com/pricing/details/automation/).

**OMS logganalys:** kanske kostnader som är kopplade till OMS beroende på din användning. Den kostnadsfria nivån innehåller 500 MB infogade data per dag. Den här lösningen förväntas i de flesta fall att mata in mindre än 500 MB per dag. Använd endast fel filtreringen ingår i runbook för att minska användningen. Om du använder mer än 500 MB per dag, uppgradera till betald nivån så att risken för analytics stoppas när begränsningen har uppnåtts. Mer information finns i [logganalys priser](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kodexempel

Hämta kodexempel som beskrivs i den här artikeln från följande platser:

-   [Data synkroniseras loggen PowerShell-Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Synkronisera loggen OMS datavy](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Nästa steg
eller mer information om SQL datasynkronisering finns:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering](sql-database-sync-data.md)
-   [Kom igång med Azure SQL datasynkronisering](sql-database-get-started-sql-data-sync.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering](sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
