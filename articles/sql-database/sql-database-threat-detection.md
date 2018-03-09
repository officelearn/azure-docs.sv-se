---
title: Hotidentifiering - Azure SQL Database | Microsoft Docs
description: "Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: shaik
editor: v-romcal
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 06/19/2017
ms.author: ronmat
ms.openlocfilehash: a01721b10305ac45082943fbe4fb7fb199a0e49f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sql-database-threat-detection"></a>Hotidentifiering för SQL-databas

SQL-Hotidentifiering identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

## <a name="overview"></a>Översikt

SQL-Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter.  Användarna får en avisering vid misstänkt databasaktiviteter, potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster. SQL-Hotidentifiering aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärd att undersöka och minska risken. Användare kan utforska misstänkta händelser med hjälp av [SQL Database Auditing](sql-database-auditing.md) att avgöra om de härrör från ett försök att komma åt, bryta mot eller utnyttja data i databasen. Hotidentifiering gör det enkelt att adressen potentiella hot mot databasen utan att behöva vara en expert säkerhet eller hantera avancerade säkerhetsövervakning system.

Till exempel är SQL injection en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen.

SQL-Hotidentifiering integreras med [Azure Security Center](https://azure.microsoft.com/services/security-center/), och varje skyddade SQL Database-server faktureras till samma pris som Azure Security Center Standard nivå på $15/nod/månad, där var och en skyddad SQL-databas Server räknas som en nod.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Ställ in hotidentifiering för din databas i Azure-portalen
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Gå till sidan för konfiguration av SQL-databas som du vill övervaka. På sidan Välj **granskning och Hotidentifiering**. 
    ![Navigeringsfönstret][1]
3. I den **granskning och Hotidentifiering** konfigurationssidan, aktivera **ON** granskning som visar inställningarna för identifiering av hot.
  
    ![Navigeringsfönstret][2]
4. Aktivera **på** Hotidentifiering.
5. Konfigurera en lista över e-postmeddelanden för att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
6. Klicka på **spara** i den **Auditing & Threat detection** sidan om du vill spara den nya eller uppdaterade gransknings- och hot identifieringsinställningar.
       
    ![Navigeringsfönstret][3]

## <a name="set-up-threat-detection-using-powershell"></a>Ställ in hotidentifiering med hjälp av PowerShell

Ett exempel på skript finns [konfigurera granskning och hotidentifiering identifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt aktivitet
1. Du får ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. <br/>
   E-postmeddelandet innehåller information om händelsen misstänkta säkerhet inklusive uppbyggnad avvikande aktiviteter, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom e-postmeddelandet innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska den potentiella risken till databasen.<br/>
     
    ![Navigeringsfönstret][4]
2. E-postavisering innehåller en direktlänk till SQL granskningsloggen. När du klickar på den här länken startar Azure portal och öppnar SQL granskningsposter vid ungefär samma tidpunkt misstänkta händelsen. Klicka på en granskningspost om du vill visa mer information om misstänkt databas verksamhet, vilket gör det lättare att hitta SQL-instruktioner som utfördes (som nås vad tyckte och när) och ta reda på om händelsen var ogiltigt eller skadliga (t.ex. ett program säkerhetsproblem för SQL injection var de utnyttjas, någon utsatts för intrång känsliga data, osv.).<br/>
   ![Navigeringsfönstret][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Utforska hotidentifieringsaviseringar för din databas i Azure-portalen

SQL-databasen Hotidentifiering integrerar dess aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). En levande panel SQL i databas på Azure-portalen spårar status för aktiva hot. 

   ![Navigeringsfönstret][6]
   
1. Klicka på SQL säkerhetsrutan startar sidan Azure Security Center-aviseringar och ger en översikt över aktiva SQL hot upptäckts på databasen. 

  ![Navigeringsfönstret][7]

2. Klicka på en specifik avisering innehåller ytterligare information och åtgärder för att undersöka det här hotet och uppdateringen framtida problem.

  ![Navigeringsfönstret][8]


## <a name="next-steps"></a>Nästa steg

* Läs mer om Hotidentifiering den [Azure blogg](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Lär dig mer om [Azure SQL Database Auditing](sql-database-auditing.md)
* Lär dig mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i [prissättning för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)  
* Exempel en PowerShell-skript finns [konfigurera granskning och hotidentifiering identifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


