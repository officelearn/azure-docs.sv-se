---
title: Konfigurera hot identifiering – Azure SQL Database Hanterad instans | Microsoft Docs
description: Hot identifiering identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i en hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567293"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurera hot identifiering (för hands version) i Azure SQL Database Hanterad instans

[Hot identifiering](sql-database-threat-detection-overview.md) för en [hanterad instans](sql-database-managed-instance-index.yml) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Hot identifiering kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från okända huvud konton eller potentiellt skadliga program och orsaka**SQL-autentiseringsuppgifter i **bruten** läge – se mer information i [aviseringar om hot identifiering](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) eller [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Hot identifiering](sql-database-threat-detection-overview.md) är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Hot identifiering kan nås och hanteras via den centrala SQL ADS-portalen. Hot identifierings tjänsten debiteras 15 $/månad per hanterad instans, med de första 30 dagarna utan kostnad.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Konfigurera hot identifiering för din hanterade instans i Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Gå till konfigurations sidan för den hanterade instans som du vill skydda. På sidan **Inställningar** väljer du **hot identifiering**.
3. På sidan konfiguration av hot identifiering
   - Aktivera **hot** identifiering.
   - Konfigurera **listan med e-postmeddelanden** som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
   - Välj det **Azure Storage-konto** där avvikande hot gransknings poster sparas.
4. Klicka på **Spara** för att spara den nya eller uppdaterade principen för hot identifiering.

   ![Hot identifiering](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [hot identifiering](sql-database-threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [Vad är en hanterad instans](sql-database-managed-instance.md).
- Läs mer om [hot identifiering för en enskild databas](sql-database-threat-detection.md).
- Läs mer om [granskning av hanterade instanser](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
