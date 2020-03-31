---
title: Avancerat skydd mot hot – Azure-portal – Azure-databas för MariaDB
description: Threat Protection identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: de056f61337311642589ba937c6d9842d8354d36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530146"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Avancerat skydd mot hot för Azure-databas för MariaDB

Avancerat skydd mot azure-databas för MariaDB identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd mot hot är en del av erbjudandet om avancerad datasäkerhet, som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd mot hot kan nås och hanteras via [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> Avancerat skydd mot hot finns i offentlig förhandsversion. Den här funktionen är tillgänglig i alla regioner i Azure där Azure Database för MariaDB distribueras för servrar med allmänt ändamål och minne.

> [!NOTE]
> Funktionen Avancerat skydd mot hot är **inte** tillgänglig i följande Azure-myndigheter och suveräna molnregioner: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Besök [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän produkttillgänglighet.

## <a name="set-up-threat-detection"></a>Konfigurera identifiering av hot
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till konfigurationssidan för den Azure Database för MariaDB-servern som du vill skydda. I säkerhetsinställningarna väljer du **Avancerat skydd mot hot (förhandsgranskning)**.
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

SQL Database Threat Detection integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). En levande SQL-hotidentifieringspaneler i databasen och SQL ATP-bladen i Azure-portalen spårar status för aktiva hot.

Klicka på **Avisering om identifiering av hot** om du vill starta sidan Azure Security Center-aviseringar och få en översikt över aktiva SQL-hot som identifierats i databasen.

   ![Varning för identifiering av hot](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns på [sidan Azure Database for MariaDB Pricing](https://azure.microsoft.com/pricing/details/mariadb/)  
