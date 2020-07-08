---
title: Använd avancerat skydd – Azure Database for PostgreSQL-enskild server
description: Hot skyddet identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74776151"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Avancerat skydd för Azure Database for PostgreSQL-enskild server

Advanced Threat Protection för Azure Database for PostgreSQL identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina databaser.

Avancerat skydd är en del av det avancerade data säkerhets erbjudandet, som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via [Azure Portal](https://portal.azure.com) och är för närvarande en för hands version.

> [!NOTE]
> Funktionen för avancerat skydd är **inte** tillgänglig i följande Azure-myndigheter och suveräna moln regioner: US Gov, Texas, US Gov, Arizona, US gov, IOWA, US, Gov Virginia, US DoD, östra, US DoD, centrala, Tyskland Central, Tyskland, norra, Kina, östra, Kina, östra 2. Besök [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) om du vill ha allmän produkt tillgänglighet.
>

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner i Azure där Azure Database for PostgreSQL distribueras för Generell användning och minnesoptimerade servrar.

## <a name="set-up-threat-detection"></a>Konfigurera hot identifiering
1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com) .
2. Gå till konfigurations sidan för den Azure Database for PostgreSQL-server som du vill skydda. I säkerhets inställningarna väljer du **Avancerat skydd (för hands version)**.
3. På konfigurations sidan för **Advanced Threat Protection (för hands version)** :

   - Aktivera avancerat skydd på servern.
   - I **Inställningar för avancerat skydd**i rutan **skicka aviseringar till** text anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
  
   ![Konfigurera hot identifiering](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Utforska avvikande databas aktiviteter

Du får ett e-postmeddelande när du har identifierat avvikande databas aktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhets händelsen, inklusive typen av avvikande aktiviteter, databasens namn, Server namn, program namn och händelse tiden. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.
    
1. Klicka på länken **Visa nya aviseringar** i e-postmeddelandet för att starta Azure Portal och visa sidan för Azure Security Center aviseringar, som ger en översikt över aktiva hot som har identifierats i SQL-databasen.
    
    ![Avvikande aktivitets rapport](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Visa aktiva hot:

    ![Aktiva hot](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klicka på en avisering om du vill ha mer information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.
    
    ![Speciell avisering](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Utforska aviseringar om hot identifiering

Avancerat skydd integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Klicka på **säkerhets aviseringar** under **hot skydd** för att starta sidan Azure Security Center aviseringar och få en översikt över aktiva SQL-hot som har identifierats i databasen.

  ![Hot skydd ASC](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Mer information om priser finns på sidan med [Azure Database for PostgreSQL priser](https://azure.microsoft.com/pricing/details/postgresql/)  
