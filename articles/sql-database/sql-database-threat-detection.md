---
title: Hotidentifiering - Azure SQL Database | Microsoft Docs
description: "Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/19/2017
ms.author: ronmat; ronitr
ms.openlocfilehash: bd3de9ed0131edc683763b0fe7f4a2ae74533944
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="sql-database-threat-detection"></a>Hotidentifiering för SQL-databas

SQL-Hotidentifiering identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

## <a name="overview"></a>Översikt

SQL-Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter.  Användarna får en avisering vid misstänkt databasaktiviteter, potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasen åtkomstmönster. SQL-Hotidentifiering aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärd att undersöka och minska risken. Användare kan utforska misstänkta händelser med hjälp av [SQL Database Auditing](sql-database-auditing.md) att avgöra om de härrör från ett försök att komma åt, bryta mot eller utnyttja data i databasen. Hotidentifiering gör det enkelt att adressen potentiella hot mot databasen utan att behöva vara en expert säkerhet eller hantera avancerade säkerhetsövervakning system.

Till exempel är SQL injection en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen.

SQL-Hotidentifiering integreras med [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/), och varje skyddade SQL Database-server kommer att debiteras på samma pris som Azure Security Center Standard nivå på $15/nod/månad, där var och en skyddad SQL Databasservern räknas som en nod. Vi erbjuder dig att testa kostnadsfritt i 60 dagar. 

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Ställ in hotidentifiering för din databas i Azure-portalen
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till bladet för konfiguration av SQL-databas som du vill övervaka. I bladet inställningar väljer **granskning och Hotidentifiering**. 
    ![Navigeringsfönstret][1]
3. I den **granskning och Hotidentifiering** configuration bladet Stäng **ON** granskning som visar inställningarna för identifiering av hot.
  
    ![Navigeringsfönstret][2]
4. Aktivera **på** Hotidentifiering.
5. Konfigurera en lista över e-postmeddelanden som ska ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
6. Klicka på **spara** i den **Auditing & Threat detection** bladet för att spara den nya eller uppdaterade gransknings- och hot identifieringsinställningar.
       
    ![Navigeringsfönstret][3]

## <a name="set-up-threat-detection-using-powershell"></a>Ställ in hotidentifiering med hjälp av PowerShell

Ett exempel på skript finns [konfigurera granskning och hotidentifiering identifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt aktivitet
1. Du får ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. <br/>
   E-postmeddelandet ger information om händelsen misstänkta säkerhet inklusive uppbyggnad avvikande aktiviteter, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom ger information om möjliga orsaker e-postmeddelandet och rekommenderade åtgärder för att undersöka och minska den potentiella risken till databasen.<br/>
     
    ![Navigeringsfönstret][4]
2. E-postavisering innehåller en direktlänk till SQL granskningsloggen. När du klickar på den här länken startar Azure portal och öppnar SQL granskningsposter vid ungefär samma tidpunkt misstänkta händelsen. Klicka på en granskningspost om du vill visa mer information om misstänkt databasaktiviteter, vilket gör det lättare att hitta SQL-instruktioner som utfördes (som nås vad tyckte och när) och ta reda på om händelsen var ogiltigt eller skadliga (t.ex. ett program säkerhetsproblem för SQL injection var de utnyttjas, någon utsatts för intrång känsliga data, osv.).<br/>
   ![Navigeringsfönstret][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Utforska hotidentifieringsaviseringar för din databas i Azure-portalen

SQL-databasen Hotidentifiering integrerar dess aviseringar med [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/). En levande panel SQL i databasbladet i Azure portal spårar status för aktiva hot. 

   ![Navigeringsfönstret][6]
   
1. Klicka på SQL säkerhetsrutan startar bladet med säkerhetsaviseringar i Azure Security Center och ger en översikt över aktiva SQL hot upptäckts på databasen. 

  ![Navigeringsfönstret][7]

2. Klicka på en specifik avisering innehåller ytterligare information och åtgärder för att undersöka det här hotet och uppdateringen framtida problem.

  ![Navigeringsfönstret][8]


## <a name="next-steps"></a>Nästa steg

* Läs mer om Hotidentifiering den [Azure blogg](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Lär dig mer om [Azure SQL Database Auditing](sql-database-auditing.md)
* Lär dig mer om [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)
* Mer information om priser finns i [prissättning för SQL-databas](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
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


