---
title: Konfigurera identifiering av hot – Azure SQL Database managed instance | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen i en hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61313909"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurera hotidentifiering (förhandsversion) i Azure SQL Database-hanterad instans

[Hotidentifiering](sql-database-threat-detection-overview.md) för en [hanterad instans](sql-database-managed-instance-index.yml) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser. Hotidentifiering kan identifiera **potentiell SQL-inmatning**, **åtkomst från ovanlig plats eller data center**, **åtkomst från okänd huvudnamn eller potentiellt skadliga program**, och **Brute force SQL autentiseringsuppgifter** -finns mer information finns i [hot aviseringar för identifiering av](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan ta emot aviseringar om de identifierade hoten via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure-portalen](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Hotidentifiering](sql-database-threat-detection-overview.md) är en del av den [avancerade datasäkerhet](sql-database-advanced-data-security.md) (ADS) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. Hotidentifiering kan nås och hanteras via den centrala SQL ADS-portalen. Threat detection-tjänsten debiteras 15$ / månad per hanterade instansen med de första 30 dagarna kostnadsfritt.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Konfigurera identifiering av hot för din hanterade instans i Azure portal

1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationssidan för den hanterade instansen som du vill skydda. I den **inställningar** väljer **Hotidentifiering**.
3. Konfigurationssida för identifiering av hot
   - Aktivera **på** Hotidentifiering.
   - Konfigurera den **lista över e-postmeddelanden** att få säkerhetsaviseringar när avvikande databasaktiviteter.
   - Välj den **Azure storage-konto** där avvikande threat granskningsposter sparas.
4. Klicka på **spara** att spara principen för nya eller uppdaterade hot.

   ![Identifiering av hot](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [hotidentifiering](sql-database-threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [hotidentifiering för enkel databas](sql-database-threat-detection.md).
- Läs mer om [hanterad instans granskning](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
