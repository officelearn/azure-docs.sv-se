---
title: Avancerat skydd – Azure Database for MySQL | Microsoft Docs
description: Avancerat skydd identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 10fa2a409437c8cc48bcd1a674cc3832f086dcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525989"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database för MySQL Avancerat skydd

Advanced Threat Protection för Azure Database for MySQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

> [!NOTE]
> Avancerat skydd finns i offentlig förhandsversion.

Avancerat skydd är en del av erbjudandet avancerad säkerhet för Data, vilket är en enhetlig paketet för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den [Azure-portalen](https://portal.azure.com) eller med hjälp av [REST API](/rest/api/mysql/serversecurityalertpolicies). Funktionen är tillgänglig för generell användning och Minnesoptimerad servrar.

> [!NOTE]
> Avancerat skydd-funktionen är **inte** finns i följande Azure government och nationellt molnregioner: US Gov Texas, USA-förvaltad region Arizona USA Gov Iowa, USA, förvaltad Virginia, USA DoD, östra, US DoD, centrala, Tyskland-centrala, Tyskland Nord, Kina, östra, Kina, östra 2. Besök [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän tillgänglighet.


## <a name="what-is-advanced-threat-protection"></a>Vad är Advanced Threat Protection?

Avancerat skydd för Azure Database for MySQL ger ett nytt lager av säkerhet som ger kunder möjlighet att upptäcka och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användarna får en avisering när misstänkta databasaktiviteter, potentiella sårbarheter, samt och avvikande mönster för åtkomst och frågor. Avancerat skydd för Azure Database för MySQL integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot. Avancerat skydd för Azure Database for MySQL gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera övervakningssystem för avancerad säkerhet. 

![Avancerat skydd-koncept](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Avancerat skydd-aviseringar 
Avancerat skydd för Azure Database for MySQL identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser och det kan utlösa följande aviseringar:
- **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for MySQL-server, där någon har loggat in till Azure Database for MySQL-server från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ovanligt Azure-Datacenter**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for MySQL-server, där någon har loggat in på servern från en ovanligt Azure-datacenter som nyligen setts på den här servern under den senaste perioden. I vissa fall identifierar aviseringen en giltig åtgärd (det nya programmet i Azure, Power BI, Azure Database för MySQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okänd huvudnamn**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till Azure Database for MySQL-server, där någon har loggat in på servern med ett ovanligt huvudkonto (Azure Database för MySQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ett potentiellt skadligt program**: Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force Azure Database för MySQL autentiseringsuppgifter**: Den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i den [Azure Database for MySQL-prissättning](https://azure.microsoft.com/pricing/details/mysql/) 
* Konfigurera [Azure Database for MySQL Advanced Threat Protection](howto-database-threat-protection-portal.md) med Azure portal  
