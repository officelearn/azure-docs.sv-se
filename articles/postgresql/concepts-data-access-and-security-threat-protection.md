---
title: Avancerat skydd – Azure Database för PostgreSQL | Microsoft Docs
description: Avancerat skydd identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
services: postgresql
author: bolzmj
manager: kfile
ms.service: postgresql
ms.topic: article
ms.date: 09/20/2018
ms.author: mbolz
ms.openlocfilehash: 392947919813753dc57422957029e961a037a616
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49066946"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>Azure Database för PostgreSQL Avancerat skydd

Advanced Threat Protection för Azure Database for PostgreSQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina databaser.

Skydd mot hot är en del av erbjudandet avancerade Threat Protection (ATP), vilket är en enhetlig paketet för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den [Azure-portalen](https://portal.azure.com) och förhandsvisas just nu.

> [!NOTE]
> Avancerat skydd-funktionen är **inte** finns i följande Azure government och nationellt molnregioner: USA Gov Texas, USA-förvaltad region Arizona USA Gov Iowa, USA, förvaltad region Virginia, USA DoD, östra, USA DoD centrala, Tyskland centrala, Tyskland Norra Kina, östra, Kina, östra 2. Besök [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän tillgänglighet.
>

## <a name="what-is-advanced-threat-protection"></a>Vad är Advanced Threat Protection?

Avancerat skydd för Azure Database for PostgreSQL ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användarna får en avisering när misstänkta databasaktiviteter, potentiella sårbarheter, samt och avvikande mönster för åtkomst och frågor. Avancerat skydd för Azure Database för PostgreSQL integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot. Avancerat skydd för Azure Database for PostgreSQL gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera övervakningssystem för avancerad säkerhet. 

![Avancerat skydd-koncept](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avancerade Threat Protection-aviseringar 
Avancerat skydd för Azure Database for PostgreSQL identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser och det kan utlösa följande aviseringar:
- **Åtkomst från ovanlig plats**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for PostgreSQL-server, där någon har loggat in på Azure Database for PostgreSQL-server från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ovanligt Azure-Datacenter**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for PostgreSQL-server, där någon har loggat in på servern från en ovanligt Azure-datacenter som nyligen setts på den här -servern under den senaste perioden. I vissa fall identifierar aviseringen en giltig åtgärd (det nya programmet i Azure, Power BI, Azure Database för PostgreSQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okänd huvudnamn**: den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for PostgreSQL-server, där någon har loggat in på servern med ett ovanligt huvudkonto (Azure Database for PostgreSQL användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force Azure Database för PostgreSQL autentiseringsuppgifter**: den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i den [Azure Database for PostgreSQL prissättning](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurera [Azure Database for PostgreSQL Advanced Threat Protection](howto-database-threat-protection-portal.md) med Azure portal  
