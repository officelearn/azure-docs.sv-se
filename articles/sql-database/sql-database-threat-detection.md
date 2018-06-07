---
title: Hotidentifiering - Azure SQL Database | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 09ba4b3b72d5c82dc42199f2f883cedee6609bd2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649551"
---
# <a name="azure-sql-database-threat-detection"></a>Hotidentifiering för Azure SQL-databas

Azure SQL Database-Hotidentifiering identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Hotidentifiering är en del av den [SQL avancerade Threat Protection](sql-advanced-threat-protection.md) (ATP) erbjudande, vilket är en enhetlig paketet för avancerade SQL-säkerhetsfunktioner. Hotidentifiering kan nås och hanteras via den centrala SQL ATP-portalen.

## <a name="what-is-threat-detection"></a>Vad är Hotidentifiering?

SQL-Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter. Användarna får en avisering vid misstänkt databasaktiviteter, potentiella säkerhetsproblem och SQL injection attacker samt avvikande databasåtkomst och frågar mönster. SQL-Hotidentifiering integreras med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderad åtgärd att undersöka och minska risken. SQL-Hotidentifiering gör det enkelt att adress potentiella hot mot databasen utan att behöva vara en expert säkerhet eller hantera övervakningssystem för avancerad säkerhet. 

För en fullständig undersökning upplevelse, rekommenderas att aktivera [SQL Database Auditing](sql-database-auditing.md), vilka skriver databasen till en granskningslogg händelselogg i Azure storage-konto.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Ställ in hotidentifiering för din databas i Azure-portalen
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till sidan för konfiguration av Azure SQL Database-server som du vill skydda. Välj i säkerhetsinställningar, **Advanced Threat Protection**.
3. På den **Advanced Threat Protection** konfigurationssidan:

   - Aktivera Advanced Threat Protection på servern.
   - I **hot identifieringsinställningar**i den **Skicka aviseringar till** text anger listan över e-postmeddelanden för att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
  
   ![Ställ in hotidentifiering](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Ställ in hotidentifiering med hjälp av PowerShell

Ett exempel på skript finns [konfigurera granskning och hotidentifiering identifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt aktivitet

Du får ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. E-postmeddelandet innehåller information om händelsen misstänkta säkerhet inklusive uppbyggnad avvikande aktiviteter, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom e-postmeddelandet innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska den potentiella risken till databasen.

![Avvikande Aktivitetsrapport](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Klicka på den **visar nya varningar för SQL** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som ger en översikt över aktiva hot som identifieras på SQL-databasen.

   ![Activty hot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och uppdateringen framtida problem.

   Till exempel är SQL injection en av säkerhetsproblem för de vanligaste Web program på Internet som används för att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen. För SQL Injection aviseringar innehåller den aviseringsinformation sårbara SQL-instruktionen som har de utnyttjas.

   ![Specifika aviseringen](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Utforska hotidentifieringsaviseringar för din databas i Azure-portalen

SQL-databasen Hotidentifiering integrerar dess aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Identifiering av paneler för live SQL-hot i databasen och SQL ATP-blad i Azure portal spårar status för aktiva hot.

Klicka på **Threat detection avisering** för att starta Azure Security Center aviseringar sidan och få en översikt över aktiva SQL hot upptäckts på databasen.

   ![Avisering om hot identifiering](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Threat detection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure SQL Database Hotidentifiering aviseringar 
Hotidentifiering för Azure SQL Database identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och den kan du aktivera följande aviseringar:
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

* Lär dig mer om [SQL Advanced Threat Protection](sql-advanced-threat-protection.md). 
* Lär dig mer om [Azure SQL Database Auditing](sql-database-auditing.md)
* Lär dig mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i [prissättning för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)  
