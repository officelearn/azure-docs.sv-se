---
title: Hotidentifiering – Azure SQL Database | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen i en enkel databas eller elastisk pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 032146742c1a49fc8cdbda24d5c732add3cc5ea7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465358"
---
# <a name="azure-sql-database-threat-detection-for-standalone-or-pooled-database"></a>Azure SQL Database Threat Detection för fristående eller databas i pool

Azure SQL [Hotidentifiering](sql-database-threat-detection-overview.md) för fristående och databaser i pooler identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser. Hotidentifiering kan identifiera **potentiell SQL-inmatning**, **åtkomst från ovanlig plats eller data center**, **åtkomst från okänd huvudnamn eller potentiellt skadliga program**, och **Brute force SQL autentiseringsuppgifter** -finns mer information finns i [aviseringar om Hotidentifieringar](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Du kan ta emot aviseringar om de identifierade hoten via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure-portalen](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Hotidentifiering](sql-database-threat-detection-overview.md) är en del av den [SQL avancerade datasäkerhet](sql-advanced-threat-protection.md) (ADS) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. Hotidentifiering kan nås och hanteras via den centrala SQL ADS-portalen. Säkerhetspaketet avancerade Data debiteras 15$ / månad per logisk Server med de första 30 dagarna kostnadsfritt.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Konfigurera hotidentifiering för databasen i Azure portal

1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationssidan för Azure SQL Database-server som du vill skydda. Säkerhetsinställningar, Välj **avancerade datasäkerhet**.
3. På den **avancerade datasäkerhet** konfigurationssidan:

   - Aktivera avancerad säkerhet för Data på servern.
   - I **inställningarna för Hotidentifiering**i den **Skicka aviseringar till** text anger listan över e-postmeddelande till få säkerhetsaviseringar när avvikande databasaktiviteter.
  
   ![Konfigurera identifiering av hot](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Konfigurera hotidentifiering med hjälp av PowerShell

Ett skript-exempel finns i [konfigurera granskning och hotidentifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Hotidentifiering](sql-database-threat-detection-overview.md).
- Läs mer om [Hotidentifiering i Managed Instance](sql-database-managed-instance-threat-detection.md).  
- Läs mer om [SQL Advanced datasäkerhet](sql-advanced-threat-protection.md).
- Läs mer om [Azure SQL Database Auditing](sql-database-auditing.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns i den [sidan med priser för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)  
