---
title: Advanced Threat Protection
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 473c58fa5097c4f4e318543c59ad1cf3a3899594
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194095"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection för Azure SQL Database

Avancerat skydd för [Azure SQL Database](sql-database-technical-overview.md) och [Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här avsnittet gäller för Azure SQL Server och för både SQL Database och Azure-Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure-Synapse.

## <a name="what-is-advanced-threat-protection"></a>Vad är Avancerat skydd

 Avancerat skydd ger ett nytt säkerhets lager som gör det möjligt för kunder att upptäcka och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter. Användare får en avisering om misstänkt databas aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst och frågor. Avancerat skydd integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet. Avancerat skydd gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhets expert eller hantera avancerade säkerhets övervaknings system.

För en fullständig utrednings erfarenhet rekommenderar vi att du aktiverar [SQL Database granskning](sql-database-auditing.md)som skriver databas händelser till en Gransknings logg i ditt Azure Storage-konto.  

## <a name="advanced-threat-protection-alerts"></a>Aviseringar för avancerat skydd

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa följande aviseringar:

- **Sårbarhet för SQL-inmatning**: den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:

  - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
  - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
- **Access from unusual location** (Åtkomst från ovanlig plats): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from unusual Azure data center** (Åtkomst från ovanligt Azure-datacenter): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från ett ovanligt Azure-datacenter som nyligen setts på den här servern. I vissa fall identifierar aviseringen en giltig åtgärd (som ditt nya program i Azure, Power BI eller Azure SQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databas aktiviteter vid identifiering av misstänkt händelse

Du får ett e-postmeddelande när du har identifierat avvikande databas aktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhets händelsen, inklusive typen av avvikande aktiviteter, databasens namn, Server namn, program namn och händelse tiden. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.

![Avvikande aktivitets rapport](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klicka på länken **Visa senaste SQL-aviseringar** i e-postmeddelandet för att starta Azure Portal och visa sidan för Azure Security Center aviseringar, som ger en översikt över aktiva hot som har identifierats i SQL-databasen.

   ![Aktivitets hot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en avisering om du vill ha mer information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   SQL-inmatning är till exempel ett av de vanligaste säkerhets problemen för webb program på Internet som används för att angripa data drivna program. Angripare drar nytta av program sårbarheter för att mata in skadliga SQL-uttryck i program post fält, bryta eller ändra data i databasen. För SQL-inmatnings aviseringar innehåller aviseringens information den sårbara SQL-instruktionen som utnyttjades.

   ![Speciell avisering](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Utforska aviseringar för avancerat skydd för din databas i Azure Portal

Avancerat skydd integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-paneler i bladet databas och SQL-annonser i Azure Portal spåra statusen för aktiva hot.

Klicka på **Avancerat skydds varning** för att starta sidan Azure Security Center aviseringar och få en översikt över aktiva SQL-hot som har identifierats i databasen.

   ![Avisering om Avancerat skydd](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert2 för avancerat skydd](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd i enkla databaser och databaser i pooler](sql-database-threat-detection.md).
- Läs mer om [Avancerat skydd i hanterade instanser](sql-database-managed-instance-threat-detection.md).
- Läs mer om [Avancerad data säkerhet](sql-database-advanced-data-security.md).
- Läs mer om [Azure SQL Database granskning](sql-database-auditing.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
