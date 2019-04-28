---
title: Avancerat skydd – Azure Database for MySQL | Microsoft Docs
description: Skydd mot hot identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 76f6c15fc1e186e254c4edbb53a2a0ccf7050b3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458981"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Avancerat skydd för Azure Database for MySQL

Advanced Threat Protection för Azure Database for MySQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av erbjudandet avancerad säkerhet för Data, vilket är en enhetlig paketet för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den [Azure-portalen](https://portal.azure.com) och förhandsvisas just nu.

> [!NOTE]
> Avancerat skydd-funktionen är **inte** finns i följande Azure government och nationellt molnregioner: US Gov Texas, USA-förvaltad region Arizona USA Gov Iowa, USA, förvaltad Virginia, USA DoD, östra, US DoD, centrala, Tyskland-centrala, Tyskland Nord, Kina, östra, Kina, östra 2. Besök [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) för allmän tillgänglighet.
>

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner där Azure där Azure Database for MySQL distribueras för generell användning och Minnesoptimerad servrar.

## <a name="set-up-threat-detection"></a>Konfigurera identifiering av hot
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationssidan för Azure Database for MySQL-server som du vill skydda. Säkerhetsinställningar, Välj **Advanced Threat Protection (förhandsversion)**.
3. På den **Advanced Threat Protection (förhandsversion)** konfigurationssidan:

   - Aktivera Avancerat skydd på servern.
   - I **Advanced Threat Protection-inställningar**i den **Skicka aviseringar till** text anger listan över e-postmeddelande till få säkerhetsaviseringar när avvikande databasaktiviteter.
  
   ![Konfigurera identifiering av hot](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Utforska avvikande databasaktiviteter

Du får ett e-postavisering när avvikande databasaktiviteter. E-postmeddelandet innehåller information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn, programnamn och tidpunkt för händelsen. Dessutom kan e-postmeddelandet innehåller information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.
 
1. Klicka på den **visar nya varningar** länken i e-postmeddelandet att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som innehåller en översikt över aktiva hot har identifierats på SQL-databasen.
    
    ![Avvikande Aktivitetsrapport](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Visa aktiva hot:

    ![Aktiva hot](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klicka på en specifik avisering om du vill ha ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.
    
    ![Specifika aviseringen](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Utforska hotidentifieringsaviseringar

Hotidentifieringen i SQL Database integrerar dess aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). En SQL threat identifiering realtidspaneler i databasen och SQL ATP-blad i Azure-portalen spårar status för aktiva hot.

Klicka på **Threat varning** för att starta Azure Security Center-aviseringar sidan och få en översikt över aktiva SQL-hot som upptäckts på databasen.

   ![Varning för hot](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns i den [Azure Database for MySQL-prissättning](https://azure.microsoft.com/pricing/details/mysql/)  
