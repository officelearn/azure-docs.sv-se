---
title: Hotidentifiering – Azure SQL Database | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen i en enkel databas eller elastisk pool.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.subservice: advanced-threat-protection
ms.custom: security
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: ronmat
ms.reviewer: vanto
ms.openlocfilehash: ab554707d432f862f9fb9c075eea6cb12419eacd
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497744"
---
# <a name="azure-sql-database-threat-detection"></a>Hotidentifiering för Azure SQL Database

Azure SQL Database Threat Detection identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Hotidentifiering är en del av den [SQL Avancerat skydd](sql-advanced-threat-protection.md) (ATP) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. Hotidentifiering kan nås och hanteras via den centrala SQL ATP-portalen.

## <a name="what-is-threat-detection"></a>Vad är identifiering av hot?

SQL-Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får en avisering när misstänkta databasaktiviteter, potentiella svagheter, och SQL-inmatning attacker, samt avvikande databasåtkomst och frågar mönster. SQL Threat Detection integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot. SQL-Hotidentifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera övervakningssystem för avancerad säkerhet. 

För en fullständig undersökning, rekommenderar vi att du aktiverar [SQL Database Auditing](sql-database-auditing.md), vilka skriver databashändelser till en granskningslogg logga i Azure storage-kontot.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurera hotidentifiering för databasen i Azure portal
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationssidan för Azure SQL Database-server som du vill skydda. Säkerhetsinställningar, Välj **Advanced Threat Protection**.
3. På den **Advanced Threat Protection** konfigurationssidan:

   - Aktivera Avancerat skydd på servern.
   - I **inställningarna för Hotidentifiering**i den **Skicka aviseringar till** text anger listan över e-postmeddelande till få säkerhetsaviseringar när avvikande databasaktiviteter.
  
   ![Konfigurera identifiering av hot](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurera hotidentifiering med hjälp av PowerShell

Ett skript-exempel finns i [konfigurera granskning och hotidentifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt aktivitet

Du får ett e-postavisering när avvikande databasaktiviteter. E-postmeddelandet innehåller information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom kan e-postmeddelandet innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.

![Avvikande Aktivitetsrapport](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Klicka på den **visar nya SQL-varningar** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som innehåller en översikt över aktiva hot har identifierats på SQL-databasen.

   ![Activty hot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   Till exempel är SQL-inmatning en av de vanligaste säkerhetsproblemen för webbprogram på Internet som används för att attackera datadrivna program. Angripare utnyttja sårbarheter i program för att mata in skadliga SQL-instruktioner i programmets inmatningsfält tränga in eller ändra data i databasen. För SQL-inmatning aviseringar innehåller den aviseringsinformation sårbara SQL-instruktionen som har utnyttjat.

   ![Specifika aviseringen](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Utforska hotidentifieringsaviseringar för din databas i Azure portal

Hotidentifieringen i SQL Database integrerar dess aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). En SQL threat identifiering realtidspaneler i databasen och SQL ATP-blad i Azure-portalen spårar status för aktiva hot.

Klicka på **Threat varning** för att starta Azure Security Center-aviseringar sidan och få en översikt över aktiva SQL-hot som upptäckts på databasen.

   ![Varning för hot](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alert2 för identifiering av hot](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure SQL Database Threat Detection-aviseringar 
Hotidentifiering för Azure SQL Database identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser och det kan utlösa följande aviseringar:
- **Sårbarhet mot SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Det här kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:
   - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
   - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
- **Access from unusual location** (Åtkomst från ovanlig plats): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from unusual Azure data center** (Åtkomst från ovanligt Azure-datacenter): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från ett ovanligt Azure-datacenter som nyligen setts på den här servern. I vissa fall identifierar aviseringen en giltig åtgärd (som ditt nya program i Azure, Power BI eller Azure SQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [SQL Advanced Threat Protection](sql-advanced-threat-protection.md). 
* Läs mer om [Azure SQL Database Auditing](sql-database-auditing.md)
* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i den [sidan med priser för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)  
