---
title: Advanced Threat Protection
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 0b231adafabff8414b1cf742e1441e938a3fa212
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848202"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection för Azure SQL Database

Avancerat skydd för [Azure SQL Database](sql-database-technical-overview.md) och [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här avsnittet gäller för Azure SQL Server och för både SQL Database och Azure-Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure-Synapse.

## <a name="what-is-advanced-threat-protection"></a>Vad är Avancerat skydd

 Avancerat skydd ger ett nytt säkerhets lager som gör det möjligt för kunder att upptäcka och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter. Användare får en avisering om misstänkt databas aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst och frågor. Avancerat skydd integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet. Avancerat skydd gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhets expert eller hantera avancerade säkerhets övervaknings system.

För en fullständig utrednings erfarenhet rekommenderar vi att du aktiverar [SQL Database granskning](sql-database-auditing.md)som skriver databas händelser till en Gransknings logg i ditt Azure Storage-konto.  

## <a name="advanced-threat-protection-alerts"></a>Aviseringar för avancerat skydd

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. En lista över aviseringar för Azure SQL Database finns i avsnittet [varningar för SQL Database och SQL Data Warehouse i Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databas aktiviteter vid identifiering av misstänkt händelse

Du får ett e-postmeddelande när du har identifierat avvikande databas aktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhets händelsen, inklusive typen av avvikande aktiviteter, databasens namn, Server namn, program namn och händelse tiden. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.

![Avvikande aktivitets rapport](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klicka på länken **Visa senaste SQL-aviseringar** i e-postmeddelandet för att starta Azure Portal och visa sidan för Azure Security Center aviseringar, som ger en översikt över aktiva hot som har identifierats i SQL-databasen.

   ![Aktivitets hot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en avisering om du vill ha mer information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   SQL-inmatning är till exempel ett av de vanligaste säkerhets problemen för webb program på Internet som används för att angripa data drivna program. Angripare drar nytta av program sårbarheter för att mata in skadliga SQL-uttryck i program post fält, bryta eller ändra data i databasen. För SQL-inmatnings aviseringar innehåller aviseringens information den sårbara SQL-instruktionen som utnyttjades.

   ![Speciell avisering](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Utforska aviseringar för avancerat skydd för din databas i Azure Portal

Avancerat skydd integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-paneler i bladet databas och SQL-annonser i Azure Portal spåra statusen för aktiva hot.

Klicka på **Avancerat skydds varning** för att starta sidan Azure Security Center aviseringar och få en översikt över aktiva SQL-hot som har identifierats i databasen.

   ![Avisering om Avancerat skydd](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert2 för avancerat skydd](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd i enkla databaser och databaser i pooler](sql-database-threat-detection.md).
- Läs mer om [Avancerat skydd i hanterade instanser](sql-database-managed-instance-threat-detection.md).
- Läs mer om [Avancerad data säkerhet](sql-database-advanced-data-security.md).
- Läs mer om [Azure SQL Database granskning](sql-database-auditing.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
