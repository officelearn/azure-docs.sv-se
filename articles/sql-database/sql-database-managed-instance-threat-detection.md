---
title: Hotidentifiering - Azure SQL Database hanteras instans | Microsoft Docs
description: "Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen."
services: sql-database
author: rmatchoro
manager: cguyer
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: f8c08bc16d622516958b8bd182179d07edfa4891
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database hanteras instans Hotidentifiering

SQL-Hotidentifiering identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försöker komma åt eller utnyttja databaser i en Azure SQL Database hanteras instans (förhandsversion).

## <a name="overview"></a>Översikt

Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot hanteras instans. Hotidentifiering är nu i förhandsvisning för hanterade instans.

Hotidentifiering innehåller ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande databasaktiviteter. Hotidentifiering gör det enkelt att adressen potentiella hot mot hanteras instans utan att behöva en expert säkerhet eller hantera avancerade säkerhetsövervakning system. För en fullständig undersökning upplevelse rekommenderas att aktivera Azure hanteras instans granskning, som skriver databasen till en granskningslogg händelselogg i Azure storage-konto. 

SQL-Hotidentifiering integreras med [Azure Security Center](https://azure.microsoft.com/services/security-center/), och varje skyddade hanteras instans faktureras till samma pris som Azure Security Center Standard nivå på $15/nod/månad, där varje skydd hanteras instans räknas som en nod.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Ställ in Hotidentifiering för din hanterade instans i Azure-portalen
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Gå till sidan för konfiguration av hanterade-instansen som du vill skydda. I den **inställningar** väljer **Hotidentifiering**. 
3. På sidan Hotidentifiering konfiguration 
   - Aktivera **på** Hotidentifiering.
   - Konfigurera den **lista över e-postmeddelanden** att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
   - Välj den **Azure storage-konto** där avvikande hot granskningsposter sparas. 
4.  Klicka på **spara** spara nya eller uppdaterade threat detection principen.

   ![Hotidentifiering](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande hanteras instans aktiviteter vid identifiering av en misstänkt aktivitet

1. Du får ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. 

   E-postmeddelandet innehåller information om händelsen misstänkta säkerhet inklusive uppbyggnad avvikande aktiviteter, databasens namn, servernamn och tidpunkt för händelsen. Dessutom innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska den potentiella risken att instansen hanteras.

   ![Threat detection e-post](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Klicka på den **visar nya varningar för SQL** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center aviseringar, som ger en översikt över aktiva SQL hot upptäckts på instansen för hanterad databas.

   ![aktiva hot](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och uppdateringen framtida problem.

   Till exempel är SQL injection en av säkerhetsproblem för vanliga Web program på Internet. SQL injection används till att attackera datadrivna program. Angripare dra nytta av programmet säkerhetsproblem att mata in skadlig SQL-instruktioner i programmet fält, brott mot eller ändra data i databasen. För SQL Injection aviseringar innehåller den aviseringsinformation sårbara SQL-instruktionen som har de utnyttjas.

   ![SQL injection](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Hanterade instans Hotidentifiering aviseringar 

Hotidentifiering för hanterade instansen identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och den kan du aktivera följande aviseringar:
- **Sårbarhet mot SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Det här kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:
 - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
 - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angripare som försöker att mata in skadlig SQL-instruktioner med sårbara programkod eller lagrade procedurer.
- **Åtkomst från ovanliga plats**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till en hanterad instans där någon har loggat in till den instans som hanteras från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en legitima åtgärd (ett nytt program eller utvecklare underhåll). I annat fall identifierar aviseringen en skadlig åtgärd (tidigare anställd extern angripare och så vidare).
- **Åtkomst från ovanliga Azure-Datacenter**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till hanterade instans, där någon har loggat in till den instans som hanteras från en Azure-datacenter som inte visades åtkomst till den här hanterade Instans under den senaste perioden. I vissa fall identifierar aviseringen en giltig åtgärd (ditt nya program i Azure, Power BI, Azure SQL Query Editor och så vidare). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okända principal**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till hanterade instans-servern där någon har loggat in till instansen hanteras med hjälp av en ovanlig huvudnamn (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nya programutvecklaren underhåll). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I annat fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hanterad instans, se [vad är en hanterad instans](sql-database-managed-instance.md)
- Lär dig mer om [hanteras instans granskning](https://go.microsoft.com/fwlink/?linkid=869430) 
- Lär dig mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
