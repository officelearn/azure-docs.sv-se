---
title: Konfigurera Avancerat skydd – hanterad instans
description: Avancerad hot skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i en hanterad instans.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822556"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Konfigurera Avancerat skydd i Azure SQL Database Hanterad instans

[Avancerat skydd](sql-database-threat-detection-overview.md) för en [hanterad instans](sql-database-managed-instance-index.yml) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka**SQL-autentiseringsuppgifter för **Brute Force** -se Mer information finns i [varningar om Avancerat skydd](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Avancerat skydd](sql-database-threat-detection-overview.md) är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala SQL ADS-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera Avancerat skydd i Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Gå till konfigurations sidan för den hanterade instans som du vill skydda. På sidan **Inställningar** väljer du **Avancerad data säkerhet**.
3. På sidan Avancerad data säkerhets konfiguration
   - Aktivera **Avancerad** data säkerhet.
   - Konfigurera **listan med e-postmeddelanden** som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
   - Välj det **Azure Storage-konto** där avvikande hot gransknings poster sparas.
   - Välj de **typer av avancerade hot skydd** som du vill konfigurera. Läs mer om [aviseringar om Avancerat skydd](sql-database-threat-detection-overview.md).
4. Klicka på **Spara** för att spara den nya eller uppdaterade avancerade data säkerhets principen.

   ![Advanced Threat Protection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Priserna i skärm bilderna återspeglar inte alltid det aktuella priset och är ett exempel.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](sql-database-threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [Vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [Avancerat skydd för en enskild databas](sql-database-threat-detection.md).
- Läs mer om [granskning av hanterade instanser](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
