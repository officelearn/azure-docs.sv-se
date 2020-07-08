---
title: Konfigurera Advanced Threat Protection
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321554"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurera Avancerat skydd för Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Avancerat skydd](threat-detection-overview.md) för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka**SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](threat-detection-overview.md#alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](threat-detection-overview.md#explore-detection-of-a-suspicious-event) eller [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Avancerat skydd](threat-detection-overview.md) är en del av det [avancerade data säkerhets](advanced-data-security.md) erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala SQL Advanced Data Security-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera Avancerat skydd i Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till konfigurations sidan för den server som du vill skydda. I säkerhets inställningarna väljer du **Avancerad data säkerhet**.
3. På sidan **Avancerad data säkerhets** konfiguration:

   - Aktivera avancerad data säkerhet på servern.
   - I **Inställningar för avancerat skydd**i rutan **skicka aviseringar till** text anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
  
   ![Konfigurera Avancerat skydd](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurera Advanced Threat Protection med PowerShell

Ett skript exempel finns i [Konfigurera granskning och Avancerat skydd med PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](threat-detection-overview.md).
- Läs mer om [Avancerat skydd i SQL-hanterad instans](../managed-instance/threat-detection-configure.md).  
- Läs mer om [Avancerad data säkerhet](advanced-data-security.md).
- Läs mer om [granskning](../../azure-sql/database/auditing-overview.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
