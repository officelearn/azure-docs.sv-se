---
title: Avancerat skydd mot hot – Azure-databas för PostgreSQL – en server
description: Lär dig mer om hur du använder avancerat skydd mot hot för att identifiera avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768749"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Avancerat skydd mot hot i Azure-databasen för PostgreSQL - Single Server

Advanced Threat Protection för Azure Database for PostgreSQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina databaser.

> [!NOTE]
> Avancerat skydd mot hot finns i offentlig förhandsversion.

Threat Protection är en del av ATP-erbjudandet (Advanced Threat Protection), som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd mot hot kan nås och hanteras via [Azure-portalen](https://portal.azure.com) eller med [REST API](/rest/api/postgresql/serversecurityalertpolicies). Funktionen är tillgänglig för servrar som är allmänt avsedda och minnesoptimerade.

> [!NOTE]
> Funktionen Avancerat skydd mot hot är **inte** tillgänglig i följande Azure-myndigheter och suveräna molnregioner: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Besök [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän produkttillgänglighet.

## <a name="what-is-advanced-threat-protection"></a>Vad är avancerat skydd mot hot?

Avancerat skydd mot azure-databas för PostgreSQL ger ett nytt säkerhetslager som gör det möjligt för kunder att identifiera och svara på potentiella hot när de uppstår genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får en avisering om misstänkta databasaktiviteter och potentiella sårbarheter, samt avvikande databasåtkomst och frågorsmönster. Avancerat skydd mot azure-databas för PostgreSQL integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för hur du undersöker och minskar hotet. Avancerat skydd mot Azure-databas för PostgreSQL gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem. 

![Avancerat hotskyddskoncept](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Varningar för avancerat skydd mot hot 
Avancerat skydd mot hot för Azure-databas för PostgreSQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa följande aviseringar:
- **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när åtkomstmönstret ändras till Azure Database för PostgreSQL-server, där någon har loggat in på Azure Database för PostgreSQL-server från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ovanliga Azure-datacenter:** Den här aviseringen utlöses när åtkomstmönstret ändras till Azure Database för PostgreSQL-servern, där någon har loggat in på servern från ett ovanligt Azure-datacenter som sågs på den här servern under den senaste perioden. I vissa fall identifierar aviseringen en legitim åtgärd (ditt nya program i Azure, Power BI, Azure Database for PostgreSQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okänt huvudnamn**: Den här aviseringen utlöses när åtkomstmönstret ändras till Azure Database för PostgreSQL-servern, där någon har loggat in på servern med ett ovanligt huvudnamn (Azure-databas för PostgreSQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force Azure Database for PostgreSQL credentials**: This alert is triggered when there is an abnormal high number of failed logins with different credentials. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns på [sidan Azure Database for PostgreSQL Pricing](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurera [Azure-databas för postgresQL avancerat skydd mot hot](howto-database-threat-protection-portal.md) med hjälp av Azure-portalen  
