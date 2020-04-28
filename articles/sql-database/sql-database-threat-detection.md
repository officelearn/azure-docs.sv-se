---
title: Konfigurera Advanced Threat Protection
description: Avancerad hot skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i en enskild databas eller elastisk pool.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822508"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database Avancerat skydd för databaser med enkel eller pool

[Avancerat skydd](sql-database-threat-detection-overview.md) för enkla databaser och databaser i pooler identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka**SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Avancerat skydd](sql-database-threat-detection-overview.md) är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera Avancerat skydd i Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Gå till konfigurations sidan för den Azure SQL Database-Server som du vill skydda. I säkerhets inställningarna väljer du **Avancerad data säkerhet**.
3. På sidan **Avancerad data säkerhets** konfiguration:

   - Aktivera avancerad data säkerhet på servern.
   - I **Inställningar för avancerat skydd**i rutan **skicka aviseringar till** text anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
  
   ![Konfigurera Avancerat skydd](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Priserna i skärm bilderna återspeglar inte alltid det aktuella priset och är ett exempel.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurera Advanced Threat Protection med PowerShell

Ett skript exempel finns i [Konfigurera granskning och Avancerat skydd med PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](sql-database-threat-detection-overview.md).
- Läs mer om [Avancerat skydd i hanterade instanser](sql-database-managed-instance-threat-detection.md).  
- Läs mer om [Avancerad data säkerhet](sql-database-advanced-data-security.md).
- Läs mer om [granskning](sql-database-auditing.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
