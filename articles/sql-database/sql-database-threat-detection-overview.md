---
title: Avancerat skydd – Azure SQL Database | Microsoft Docs
description: Avancerat skydd identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797072"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Avancerat skydd för Azure SQL Database

Avancerat skydd för [Azure SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av den [avancerade datasäkerhet](sql-database-advanced-data-security.md) (ADS) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. Avancerat skydd kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>Vad är Advanced Threat Protection

 Avancerat skydd ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får en avisering när misstänkta databasaktiviteter, potentiella svagheter, och SQL-inmatning attacker, samt avvikande databasåtkomst och frågar mönster. Avancerat skydd integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot. Avancerat skydd gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera övervakningssystem för avancerad säkerhet.

För en fullständig undersökning, rekommenderar vi att du aktiverar [SQL Database Auditing](sql-database-auditing.md), vilka skriver databashändelser till en granskningslogg logga i Azure storage-kontot.  

## <a name="advanced-threat-protection-alerts"></a>Avancerat skydd-aviseringar

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser och det kan utlösa följande aviseringar:

- **Sårbarhet för SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:

  - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
  - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potentiell SQL-inmatning**: Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart sårbarhet för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
- **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ovanligt Azure-Datacenter**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer från en ovanligt Azure-datacenter som nyligen setts på den här servern under den senaste perioden. I vissa fall identifierar aviseringen en giltig åtgärd (som ditt nya program i Azure, Power BI eller Azure SQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okänd huvudnamn**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ett potentiellt skadligt program**: Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL autentiseringsuppgifter**: Den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt aktivitet

Du får ett e-postavisering när avvikande databasaktiviteter. E-postmeddelandet innehåller information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom kan e-postmeddelandet innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.

![Avvikande Aktivitetsrapport](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klicka på den **visar nya SQL-varningar** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som innehåller en översikt över aktiva hot har identifierats på SQL-databasen.

   ![Aktivitet hot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   Till exempel är SQL-inmatning en av de vanligaste säkerhetsproblemen för webbprogram på Internet som används för att attackera datadrivna program. Angripare utnyttja sårbarheter i program för att mata in skadliga SQL-instruktioner i programmets inmatningsfält tränga in eller ändra data i databasen. För SQL-inmatning aviseringar innehåller den aviseringsinformation sårbara SQL-instruktionen som har utnyttjat.

   ![Specifika aviseringen](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Utforska Advanced Threat Protection-aviseringar för din databas i Azure portal

Avancerat skydd integrerar dess aviseringar med [Azure security center](https://azure.microsoft.com/services/security-center/). Realtidspaneler i databasen och SQL-ANNONSER blad i Azure portal SQL Advanced Threat Protection spåra status för aktiva hot.

Klicka på **Advanced Threat Protection avisering** för att starta Azure Security Center-aviseringar sidan och få en översikt över aktiva SQL hot som upptäckts på databasen eller datalagret.

   ![Avancerat skydd-avisering](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd i enkel och delade databaser](sql-database-threat-detection.md).
- Läs mer om [Avancerat skydd i hanterade instansen](sql-database-managed-instance-threat-detection.md).
- Läs mer om [avancerade datasäkerhet](sql-database-advanced-data-security.md).
- Läs mer om [Azure SQL Database-granskning](sql-database-auditing.md)
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns i den [sidan med priser för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)  
