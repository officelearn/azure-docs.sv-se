---
title: Konfigurera aviseringar och meddelanden med hjälp av Azure portal | Microsoft Docs
description: Använd Azure-portalen för att skapa SQL Database-aviseringar som kan utlösa aviseringar eller automation när angivna villkor är uppfyllda.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 64e743010b3ae615817639d9b2531b2f99979498
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156910"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Skapa aviseringar för Azure SQL Database och Data Warehouse med Azure portal

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in Azure SQL Database och Data Warehouse aviseringar med Azure portal. Aviseringar kan skicka ett e-postmeddelande eller anropa en webhook när vissa mått (till exempel databasens storlek eller CPU-användning) når tröskelvärdet. Den här artikeln innehåller också Metodtips för att ställa in aviseringar perioder.    

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i hanterade instanser ännu. Alternativt kan du använda SQL Agent för att skicka e-postaviseringar för vissa mått baserat på [Dynamic Management Views](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Du kan få en avisering baserat på övervakning mått för eller händelser på dina Azure-tjänster.

* **Måttvärden** -aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan efteråt när villkoret som inte längre är uppfyllt.    
* **Händelser i aktivitetsloggen** – en avisering kan utlösas vid *varje* händelse eller bara när ett visst antal händelser inträffar.

Du kan konfigurera en avisering om du vill göra följande när den utlöses:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webhook

Du kan konfigurera och få information om Varningsregler med hjälp av

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Kommandoradsgränssnittet (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en aviseringsregel på ett mått med Azure portal
1. I den [portal](https://portal.azure.com/), letar du upp den resurs som du är intresserad av övervakning och markera den.
2. Välj **aviseringar (klassisk)** under avsnittet övervakning. Text och ikonen kan variera något mellan olika resurser.  
   
     ![Övervakning](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **SQL DW ENDAST**: Klicka på den **DWU-användning** graph. Välj **Visa klassiska aviseringar**

3. Välj den **Lägg till måttavisering (klassisk)** knappen och Fyll i fälten.
   
    ![Lägg till avisering](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Namnet** aviseringen regel och väljer en **beskrivning**, som visar även i e-postaviseringar.
5. Välj den **mått** du vill övervaka och väljer sedan en **villkor** och **tröskelvärdet** värdet för måttet. Också välja den **Period** tid som måttregel måste vara uppfyllda innan aviseringen utlösare. Till exempel om du använder perioden ”PT5M” och aviseringen söker efter CPU högre än 80%, en avisering utlöses när den **genomsnittlig** CPU har varit över 80% i 5 minuter. När den första utlösaren inträffar, utlöser igen när den genomsnittliga CPU som är mindre än 80% under 5 minuter. CPU-mätning sker varje minut. Med hjälp av tabellen nedan för stöds tidsfönster och aggregeringen Skriv som var Avisera använder inte alla aviseringar använder medelvärdet.   
6. Kontrollera **e-ägare...**  om du vill att administratörer och medadministratörer för att få e-postaviseringar när aviseringen utlöses.
7. Om du vill att ytterligare e-postmeddelanden tar emot ett meddelande när aviseringen utlöses, lägga till dem i den **administratören email(s)** fält. Avgränsa flera e-postmeddelanden med semikolon -  *email@contoso.com;email2@contoso.com*
8. Placera i en giltig URI i den **Webhook** fältet om du vill att den anropas när aviseringen utlöses.
9. Välj **OK** när du är klar för att skapa aviseringen.   

Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

## <a name="managing-your-alerts"></a>Hantera dina aviseringar
När du har skapat en avisering, kan du välja den och:

* Visa ett diagram som visar tröskelvärde för mått och de faktiska värdena från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **aktivera** det om du vill att tillfälligt stoppa eller återuppta ta emot meddelanden för den här aviseringen.


## <a name="sql-database-alert-values"></a>Aviseringen värden för SQL-databas

| Resurstyp | Måttnamn | Eget namn | Sammansättningstyp: | Minsta avisering tidsfönstret|
| --- | --- | --- | --- | --- |
| SQL-databas | cpu_percent | CPU-procent | Medel | 5 minuter |
| SQL-databas | physical_data_read_percent | Data IO-procent | Medel | 5 minuter |
| SQL-databas | log_write_percent | Logg-IO-procent | Medel | 5 minuter |
| SQL-databas | dtu_consumption_percent | DTU-procent | Medel | 5 minuter |
| SQL-databas | lagring | Totala databasstorleken | Maximal | 30 minuter |
| SQL-databas | connection_successful | Anslutningarna lyckades | Totalt | 10 minuter |
| SQL-databas | connection_failed | Misslyckade anslutningar | Totalt | 10 minuter |
| SQL-databas | blocked_by_firewall | Blockeras av brandvägg | Totalt | 10 minuter |
| SQL-databas | Deadlock | Dödlägen | Totalt | 10 minuter |
| SQL-databas | storage_percent | Databasstorlek i procent | Maximal | 30 minuter |
| SQL-databas | xtp_storage_percent | Percent(Preview) för in-Memory OLTP-lagring | Medel | 5 minuter |
| SQL-databas | workers_percent | Arbetare procent | Medel | 5 minuter |
| SQL-databas | sessions_percent | Sessioner procent | Medel | 5 minuter |
| SQL-databas | dtu_limit | DTU-gräns | Medel | 5 minuter |
| SQL-databas | dtu_used | DTU används | Medel | 5 minuter |
||||||
| Elastisk pool | cpu_percent | CPU-procent | Medel | 10 minuter |
| Elastisk pool | physical_data_read_percent | Data IO-procent | Medel | 10 minuter |
| Elastisk pool | log_write_percent | Logg-IO-procent | Medel | 10 minuter |
| Elastisk pool | dtu_consumption_percent | DTU-procent | Medel | 10 minuter |
| Elastisk pool | storage_percent | Lagringsprocent | Medel | 10 minuter |
| Elastisk pool | workers_percent | Arbetare procent | Medel | 10 minuter |
| Elastisk pool | eDTU_limit | eDTU-gränsen | Medel | 10 minuter |
| Elastisk pool | storage_limit | Gränsen för lagring | Medel | 10 minuter |
| Elastisk pool | eDTU_used | edtu: er används | Medel | 10 minuter |
| Elastisk pool | storage_used | Använt lagringsutrymme | Medel | 10 minuter |
||||||               
| SQL Data Warehouse | cpu_percent | CPU-procent | Medel | 10 minuter |
| SQL Data Warehouse | physical_data_read_percent | Data IO-procent | Medel | 10 minuter |
| SQL Data Warehouse | lagring | Totala databasstorleken | Maximal | 10 minuter |
| SQL Data Warehouse | connection_successful | Anslutningarna lyckades | Totalt | 10 minuter |
| SQL Data Warehouse | connection_failed | Misslyckade anslutningar | Totalt | 10 minuter |
| SQL Data Warehouse | blocked_by_firewall | Blockeras av brandvägg | Totalt | 10 minuter |
| SQL Data Warehouse | service_level_objective | Tjänstenivå för databasen | Totalt | 10 minuter |
| SQL Data Warehouse | dwu_limit | dwu-gräns | Maximal | 10 minuter |
| SQL Data Warehouse | dwu_consumption_percent | DWU-procent | Medel | 10 minuter |
| SQL Data Warehouse | dwu_used | Använda DWU | Medel | 10 minuter |
||||||


## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure-övervakning](../monitoring-and-diagnostics/monitoring-overview.md) , inklusive typerna av information som du kan samla in och övervaka.
* Läs mer om [konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Hämta en [översikt över diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) och samla in detaljerade mätvärden för hög frekvens på din tjänst.
* Hämta en [översikt över mått samling](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarar.
