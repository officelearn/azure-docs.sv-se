---
title: Konfigurera aviseringar och meddelanden med Azure Portal | Microsoft Docs
description: Använd Azure Portal för att skapa SQL Database aviseringar som kan utlösa meddelanden eller automatisering när de villkor du anger är uppfyllda.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: 9468dbd71ee8da88cbabc3ca9f76c77d47adc221
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567922"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Skapa aviseringar för Azure SQL Database och informations lager med Azure Portal

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in Azure SQL Database och informations lager aviseringar med hjälp av Azure Portal. Aviseringar kan skicka ett e-postmeddelande till dig eller anropa en webbhook när något mått (till exempel databas storlek eller CPU-användning) når tröskelvärdet. Den här artikeln innehåller också metod tips för att ställa in aviserings perioder.    

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig ännu i en hanterad instans. Alternativt kan du använda SQL-agenten för att skicka e-postaviseringar för vissa mått baserat på [vyer för dynamisk hantering](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Du kan få en avisering baserat på övervaknings mått för, eller händelser på, dina Azure-tjänster.

* **Mått värden** – aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar i båda riktningarna. Det innebär att den utlöses både när villkoret först uppfylls och sedan efteråt när det villkoret inte längre uppfylls.    
* **Aktivitets logg händelser** – en avisering kan utlösas vid *varje* händelse, eller endast när ett visst antal händelser inträffar.

Du kan konfigurera en avisering för att göra följande när den utlöser:

* Skicka e-postaviseringar till tjänst administratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* anropa en webhook

Du kan konfigurera och hämta information om aviserings regler med hjälp av

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [kommando rads gränssnitt (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en varnings regel för ett mått med Azure Portal
1. I [portalen](https://portal.azure.com/)letar du reda på den resurs som du är intresse rad av övervakning och väljer den.
2. Välj **aviseringar (klassisk)** under avsnittet övervakning. Texten och ikonen kan variera något för olika resurser.  
   
     ![Övervakning](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **ENDAST SQL DW**: Klicka på diagram över **användning av DWU** . Välj **Visa klassiska aviseringar**

3. Välj knappen **Lägg till mått varning (klassisk)** och fyll i fälten.
   
    ![Lägg till avisering](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Namnge** din aviserings regel och välj en **Beskrivning**, som också visas i e-postmeddelanden.
5. Välj det **mått** som du vill övervaka och välj sedan ett **villkor** och ett **tröskel** värde för måttet. Välj också den tids **period** som mått regeln måste uppfylla innan aviseringen utlöses. Om du till exempel använder perioden "PT5M" och din avisering söker efter CPU över 80%, utlöses aviseringen när den **genomsnittliga** CPU: n är över 80% i 5 minuter. När den första utlösaren sker utlöses den igen när den genomsnittliga CPU: n är under 80% över 5 minuter. CPU-måttet sker var 1 minut. I tabellen nedan finns det stöd för tidsfönster som stöds och den agg regerings typ som varje varning använder – inte alla aviseringar använder det genomsnittliga värdet.   
6. Kontrol lera **e-postägare...** om du vill att administratörer och medadministratörer ska skickas via e-post när aviseringen utlöses.
7. Om du vill att ytterligare e-postmeddelanden ska ta emot ett meddelande när aviseringen utlöses, lägger du till dem i fältet **ytterligare administratörs e-post (er)** . Avgränsa flera e-postmeddelanden med semikolon – e- *post\@contoso. com; email2\@contoso.com*
8. Placera i en giltig URI i fältet **webhook** om du vill att det ska anropas när aviseringen utlöses.
9. Välj **OK** när du är färdig för att skapa aviseringen.   

Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="managing-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du välja den och:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **Aktivera** det om du tillfälligt vill stoppa eller återuppta mottagning av aviseringar.


## <a name="sql-database-alert-values"></a>SQL Database aviserings värden

| Resurstyp | Måttnamn | Eget namn | Sammansättningstyp: | Minsta tids period för avisering|
| --- | --- | --- | --- | --- |
| SQL-databas | cpu_percent | Processorprocent | Average | 5 minuter |
| SQL-databas | physical_data_read_percent | Data I/O-procent | Average | 5 minuter |
| SQL-databas | log_write_percent | Logg I/O-procent | Average | 5 minuter |
| SQL-databas | dtu_consumption_percent | DTU-procent | Average | 5 minuter |
| SQL-databas | lagring | Total databas storlek | Maximal | 30 minuter |
| SQL-databas | connection_successful | Lyckade anslutningar | Totalt | 10 minuter |
| SQL-databas | connection_failed | Misslyckade anslutningar | Totalt | 10 minuter |
| SQL-databas | blocked_by_firewall | Blockerad av brand väggen | Totalt | 10 minuter |
| SQL-databas | hamn | Dödlägen | Totalt | 10 minuter |
| SQL-databas | storage_percent | Databasstorlek i procent | Maximal | 30 minuter |
| SQL-databas | xtp_storage_percent | Minnes intern OLTP-lagring i procent (för hands version) | Average | 5 minuter |
| SQL-databas | workers_percent | Arbetarprocent | Average | 5 minuter |
| SQL-databas | sessions_percent | Sessioner i procent | Average | 5 minuter |
| SQL-databas | dtu_limit | DTU-gräns | Average | 5 minuter |
| SQL-databas | dtu_used | Använt DTU | Average | 5 minuter |
||||||
| Elastisk pool | cpu_percent | Processorprocent | Average | 10 minuter |
| Elastisk pool | physical_data_read_percent | Data I/O-procent | Average | 10 minuter |
| Elastisk pool | log_write_percent | Logg I/O-procent | Average | 10 minuter |
| Elastisk pool | dtu_consumption_percent | DTU-procent | Average | 10 minuter |
| Elastisk pool | storage_percent | Lagrings procent | Average | 10 minuter |
| Elastisk pool | workers_percent | Arbetarprocent | Average | 10 minuter |
| Elastisk pool | eDTU_limit | eDTU-gräns | Average | 10 minuter |
| Elastisk pool | storage_limit | Lagrings gräns | Average | 10 minuter |
| Elastisk pool | eDTU_used | eDTU använt | Average | 10 minuter |
| Elastisk pool | storage_used | Använt lagringsutrymme | Average | 10 minuter |
||||||               
| SQL Data Warehouse | cpu_percent | Processorprocent | Average | 10 minuter |
| SQL Data Warehouse | physical_data_read_percent | Data I/O-procent | Average | 10 minuter |
| SQL Data Warehouse | connection_successful | Lyckade anslutningar | Totalt | 10 minuter |
| SQL Data Warehouse | connection_failed | Misslyckade anslutningar | Totalt | 10 minuter |
| SQL Data Warehouse | blocked_by_firewall | Blockerad av brand väggen | Totalt | 10 minuter |
| SQL Data Warehouse | service_level_objective | Tjänst nivå för databasen | Totalt | 10 minuter |
| SQL Data Warehouse | dwu_limit | DWU-gräns | Maximal | 10 minuter |
| SQL Data Warehouse | dwu_consumption_percent | DWU procent | Average | 10 minuter |
| SQL Data Warehouse | dwu_used | DWU som används | Average | 10 minuter |
||||||


## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure-övervakning](../monitoring-and-diagnostics/monitoring-overview.md) , inklusive de typer av information som du kan samla in och övervaka.
* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Få en [Översikt över diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) och samla in detaljerade mått för hög frekvens på din tjänst.
* Få en [Översikt över mått samlingen](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för att se till att tjänsten är tillgänglig och svarar.
