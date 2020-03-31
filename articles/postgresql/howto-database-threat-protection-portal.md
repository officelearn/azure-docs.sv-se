---
title: Använd avancerat skydd mot hot – Azure-databas för PostgreSQL - Single Server
description: Threat Protection identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74776151"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Avancerat skydd mot hot för Azure-databas för PostgreSQL - Single Server

Advanced Threat Protection för Azure Database for PostgreSQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina databaser.

Avancerat skydd mot hot är en del av erbjudandet om avancerad datasäkerhet, som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd mot hot kan nås och hanteras via [Azure-portalen](https://portal.azure.com) och är för närvarande i förhandsversion.

> [!NOTE]
> Funktionen Avancerat skydd mot hot är **inte** tillgänglig i följande Azure-myndigheter och suveräna molnregioner: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Besök [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän produkttillgänglighet.
>

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner i Azure där Azure Database for PostgreSQL distribueras för servrar med allmänt syfte och minne.

## <a name="set-up-threat-detection"></a>Konfigurera identifiering av hot
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till konfigurationssidan för Den Azure-databas för PostgreSQL-server som du vill skydda. I säkerhetsinställningarna väljer du **Avancerat skydd mot hot (förhandsgranskning)**.
3. På konfigurationssidan **för avancerat skyddsskydd (förhandsversion):**

   - Aktivera avancerat skydd mot hot på servern.
   - I **Avancerade inställningar för skydd av hot**anger du i rutan Skicka aviseringar **till** text en lista över e-postmeddelanden för att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
  
   ![Konfigurera identifiering av hot](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Utforska avvikande databasaktiviteter

Du får ett e-postmeddelande när du har upptäckt avvikande databasaktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhetshändelsen, inklusive arten av avvikande aktiviteter, databasnamn, servernamn, programnamn och händelsetid. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska det potentiella hotet mot databasen.
    
1. Klicka på länken **Visa senaste aviseringar** i e-postmeddelandet för att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som ger en översikt över aktiva hot som upptäckts i SQL-databasen.
    
    ![Avvikelseaktivitetsrapport](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Visa aktiva hot:

    ![Aktiva hot](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klicka på en specifik avisering för att få ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.
    
    ![Specifik varning](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Utforska varningar om identifiering av hot

Avancerat skydd mot hot integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Klicka på **Säkerhetsaviseringar** under **THREAT PROTECTION** för att starta sidan Azure Security Alerts och få en översikt över aktiva SQL-hot som identifierats i databasen.

  ![Hotskydd asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns på [sidan Azure Database for PostgreSQL Pricing](https://azure.microsoft.com/pricing/details/postgresql/)  
