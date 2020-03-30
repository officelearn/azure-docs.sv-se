---
title: Konfigurera Advanced Threat Protection
description: Avancerat skydd mot hot identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen i en enda databas eller elastisk pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822508"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Avancerat skydd mot azure SQL-databas för enstaka eller poolade databaser

[Avancerat skydd mot hot](sql-database-threat-detection-overview.md) för enstaka och poolade databaser identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd mot hot kan identifiera **potentiell SQL-injektion**, **Åtkomst från ovanlig plats eller datacenter**, Åtkomst från okända **huvud- eller potentiellt skadliga program**och Brute force **SQL-autentiseringsuppgifter** - se mer information i avancerade [hotskyddsvarningar](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om upptäckta hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure-portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) är en del av det [avancerade datasäkerhetserbjudandet](sql-database-advanced-data-security.md) (ADS), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera avancerat skydd mot hot i Azure-portalen

1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till konfigurationssidan för den Azure SQL Database-server som du vill skydda. I säkerhetsinställningarna väljer du **Avancerad datasäkerhet**.
3. På sidan **Avancerad datasäkerhetskonfiguration:**

   - Aktivera avancerad datasäkerhet på servern.
   - I **Avancerade inställningar för skydd av hot**anger du i rutan Skicka aviseringar **till** text en lista över e-postmeddelanden för att ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.
  
   ![Konfigurera avancerat hotskydd](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Priserna på skärmbilder återspeglar inte alltid det aktuella priset och är ett exempel.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurera Advanced Threat Protection med PowerShell

Ett skriptexempel finns i [Konfigurera granskning och avancerat skydd mot hot med PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [avancerat skydd mot hot](sql-database-threat-detection-overview.md).
- Läs mer om [avancerat skydd mot hot i hanterad instans](sql-database-managed-instance-threat-detection.md).  
- Läs mer om [avancerad datasäkerhet](sql-database-advanced-data-security.md).
- Läs mer om [granskning](sql-database-auditing.md)
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om prissättning finns på [prissidan för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
