---
title: Konfigurera avancerat skydd mot hot – hanterad instans
description: Avancerat skydd mot hot identifierar avvikande databasaktiviteter som anger potentiella säkerhetshot mot databasen i en hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822556"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Konfigurera avancerat skydd mot hot i hanterad instans i Azure SQL Database

[Avancerat skydd mot hot](sql-database-threat-detection-overview.md) för en [hanterad instans](sql-database-managed-instance-index.yml) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd mot hot kan identifiera **potentiell SQL-injektion**, **Åtkomst från ovanlig plats eller datacenter**, Åtkomst från okända **huvud- eller potentiellt skadliga program**och Brute force **SQL-autentiseringsuppgifter** - se mer information i avancerade [hotskyddsvarningar](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om upptäckta hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure-portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) är en del av det [avancerade datasäkerhetserbjudandet](sql-database-advanced-data-security.md) (ADS), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera avancerat skydd mot hot i Azure-portalen

1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till konfigurationssidan för den hanterade instans som du vill skydda. Välj **Avancerad datasäkerhet**på sidan **Inställningar** .
3. På sidan Avancerad datasäkerhetskonfiguration
   - Aktivera **ON** avancerad datasäkerhet.
   - Konfigurera **listan över e-postmeddelanden** för att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
   - Välj **azure-lagringskontot** där avvikande hotgranskningsposter sparas.
   - Välj de **avancerade skyddsskyddstyper** som du vill ha konfigurerade. Läs mer om [varningar om avancerat skydd mot hot](sql-database-threat-detection-overview.md).
4. Klicka på **Spara** om du vill spara den nya eller uppdaterade principen för avancerad datasäkerhet.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Priserna på skärmbilder återspeglar inte alltid det aktuella priset och är ett exempel.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [avancerat skydd mot hot](sql-database-threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [Vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [avancerat skydd mot hot för en enskild databas](sql-database-threat-detection.md).
- Läs mer om [granskning av hanterade instanser](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
