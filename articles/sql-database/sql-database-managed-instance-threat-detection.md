---
title: Konfigurera identifiering av hot – Azure SQL Database Managed Instance | Microsoft Docs
description: Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen i en hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158677"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurera Hotidentifiering (förhandsversion) i Azure SQL Database Managed Instance

Azure SQL [Hotidentifiering](sql-database-threat-detection-overview.md) för [SQL Database Managed Instance](sql-database-managed-instance-index.yml) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser. Hotidentifiering kan identifiera **potentiell SQL-inmatning**, **åtkomst från ovanlig plats eller data center**, **åtkomst från okänd huvudnamn eller potentiellt skadliga program**, och **Brute force SQL autentiseringsuppgifter** -finns mer information finns i [aviseringar om Hotidentifieringar](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Du kan ta emot aviseringar om de identifierade hoten via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure-portalen](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Hotidentifiering](sql-database-threat-detection-overview.md) är en del av den [SQL Avancerat skydd](sql-advanced-threat-protection.md) (ATP) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. Hotidentifiering kan nås och hanteras via den centrala SQL ATP-portalen. Threat detection-tjänsten debiteras 15$ / månad per Managed Instance med första 30 dagarna kostnadsfritt.

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

- Läs mer om [Hotidentifiering](sql-database-threat-detection-overview.md).
- Lär dig mer om hanterad instans, se [vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [Hotidentifiering för enkel databas](sql-database-threat-detection.md).
- Läs mer om [hanterad instans granskning](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
