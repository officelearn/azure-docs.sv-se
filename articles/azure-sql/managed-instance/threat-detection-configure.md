---
title: Konfigurera Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: a60156762a4d8dfb6b11ae70e608fb26b07e5764
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045762"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Konfigurera Avancerat skydd i Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Avancerat skydd](../database/threat-detection-overview.md) för en [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka**SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](../database/threat-detection-overview.md#alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) eller [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-azure-portal)

[Avancerat skydd](../database/threat-detection-overview.md) är en del av det [avancerade data säkerhets](../database/advanced-data-security.md) erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

##  <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Gå till sidan konfiguration för den SQL-hanterade instans som du vill skydda. På sidan **Inställningar** väljer du **Avancerad data säkerhet**.
3. På sidan Avancerad data säkerhets konfiguration
   - Aktivera **Avancerad** data säkerhet.
   - Konfigurera **listan med e-postmeddelanden** som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
   - Välj det **Azure Storage-konto** där avvikande hot gransknings poster sparas.
   - Välj de **typer av avancerade hot skydd** som du vill konfigurera. Läs mer om [aviseringar om Avancerat skydd](../database/threat-detection-overview.md).
4. Klicka på **Spara** för att spara den nya eller uppdaterade avancerade data säkerhets principen.

   ![Advanced Threat Protection](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](../database/threat-detection-overview.md).
- Lär dig mer om hanterade instanser, se [Vad är en Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md).
- Läs mer om [Avancerat skydd för Azure SQL Database](../database/threat-detection-configure.md).
- Läs mer om [granskning av SQL-hanterad instans](https://go.microsoft.com/fwlink/?linkid=869430).
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
