---
title: Hotidentifiering – Azure SQL Database Managed Instance | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: vanto
ms.openlocfilehash: 76033438ad785412aa97358d80b5e4fdbf4c2bfb
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717003"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database Managed Instance Hotidentifiering

SQL Threat Detection identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser i en Azure SQL Database Managed Instance (förhandsversion).

## <a name="overview"></a>Översikt

Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot till Managed Instance. Hotidentifiering är nu i förhandsversion för hanterad instans.

Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande databasaktiviteter. Hotidentifiering gör det enkelt att hantera potentiella hot till Managed Instance utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem. För en fullständig undersökning rekommenderar vi att du aktiverar Azure hanterade instans granskning som skriver databashändelser till en granskningslogg logga i Azure storage-kontot. 

SQL Threat Detection integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), och varje skyddad hanterad instans debiteras till samma pris som Azure Security Center Standard-nivån på $15/nod/månad, där varje skyddad hanterad instans räknas som en nod.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Konfigurera identifiering av hot för din hanterade instans i Azure portal
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationssidan för den hanterade instansen som du vill skydda. I den **inställningar** väljer **Hotidentifiering**. 
3. Konfigurationssida för identifiering av hot 
   - Aktivera **på** Hotidentifiering.
   - Konfigurera den **lista över e-postmeddelanden** att få säkerhetsaviseringar när avvikande databasaktiviteter.
   - Välj den **Azure storage-konto** där avvikande threat granskningsposter sparas. 
4.  Klicka på **spara** att spara principen för nya eller uppdaterade hot.

   ![Identifiering av hot](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande aktiviteter för hanterad instans när en misstänkt händelse

1. Du får ett e-postavisering när avvikande databasaktiviteter. 

   E-postmeddelandet innehåller information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn och tidpunkt för händelsen. Dessutom innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet till den hanterade instansen.

   ![Threat identifiering av e-post](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Klicka på den **visar nya SQL-varningar** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som innehåller en översikt över den aktiva SQL hot har identifierats på den hanterade instansen databas.

   ![aktiva hot](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   Till exempel är SQL-inmatning en av de vanliga säkerhetsproblemen för webbprogram på Internet. SQL-inmatning används för att attackera datadrivna program. Angripare utnyttja sårbarheter i program för att mata in skadliga SQL-instruktioner i programmets inmatningsfält tränga in eller ändra data i databasen. För SQL-inmatning aviseringar innehåller den aviseringsinformation sårbara SQL-instruktionen som har utnyttjat.

   ![SQL-inmatning](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Den hanterade instansen Hotidentifiering aviseringar 

Hotidentifiering för hanterad instans identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser och det kan utlösa följande aviseringar:
- **Sårbarhet mot SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Det här kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:
 - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
 - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
- **Åtkomst från ovanlig plats**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till en hanterad instans, där någon har loggat in på den hanterade instansen från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller utvecklare underhållsåtgärden). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare och så vidare).
- **Åtkomst från ovanligt Azure-Datacenter**: den här aviseringen utlöses när åtkomstmönstret till hanterad instans, där någon har loggat in på den hanterade instansen från ett Azure-datacenter som inte visades åtkomst till den här hanterade ändras Instans under den senaste perioden. I vissa fall identifierar aviseringen en giltig åtgärd (det nya programmet i Azure, Power BI, Azure SQL-frågeredigeraren och så vidare). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okänd huvudnamn**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till hanterad instans-server, där någon har loggat in på den hanterade instansen med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt programutvecklare underhållsåtgärden). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hanterad instans, se [vad är en hanterad instans](sql-database-managed-instance.md)
- Läs mer om [hanterad instans granskning](https://go.microsoft.com/fwlink/?linkid=869430) 
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
