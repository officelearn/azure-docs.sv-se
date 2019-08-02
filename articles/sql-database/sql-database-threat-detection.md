---
title: Hot identifiering – Azure SQL Database | Microsoft Docs
description: Hot identifiering identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i en enskild databas eller elastisk pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 5549d016978e8bf9491c3745e335e3c4c793212c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566332"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Azure SQL Database hot identifiering för en eller flera databaser i en pool

[Hot identifiering](sql-database-threat-detection-overview.md) för enkla databaser och databaser i pooler identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Hot identifiering kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från okända huvud konton eller potentiellt skadliga program och orsaka**SQL-autentiseringsuppgifter i **bruten** läge – se mer information i [aviseringar om hot identifiering](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Hot identifiering](sql-database-threat-detection-overview.md) är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Hot identifiering kan nås och hanteras via den centrala SQL ADS-portalen. Det avancerade data säkerhets paketet debiteras 15 $/månad per logisk server med de första 30 dagarna kostnads fritt.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurera hot identifiering för databasen i Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Gå till konfigurations sidan för den Azure SQL Database-Server som du vill skydda. I säkerhets inställningarna väljer du **Avancerad data säkerhet**.
3. På sidan **Avancerad data säkerhets** konfiguration:

   - Aktivera avancerad data säkerhet på servern.
   - I **Inställningar för hot identifiering**i text rutan **skicka aviseringar till** anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
  
   ![Konfigurera hot identifiering](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurera hot identifiering med PowerShell

Ett skript exempel finns i [Konfigurera granskning och hot identifiering med PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [hot identifiering](sql-database-threat-detection-overview.md).
- Lär dig mer om [hot identifiering i hanterade instanser](sql-database-managed-instance-threat-detection.md).  
- Läs mer om [Avancerad data säkerhet](sql-database-advanced-data-security.md).
- Läs mer om [granskning](sql-database-auditing.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
