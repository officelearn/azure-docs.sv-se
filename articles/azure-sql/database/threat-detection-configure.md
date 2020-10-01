---
title: Konfigurera Advanced Threat Protection
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot till databasen i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: 678de4354012dcea5b954980109fd71c1004aca4
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619295"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurera Avancerat skydd för Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Avancerat skydd](threat-detection-overview.md) för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Avancerat skydd kan identifiera **potentiell SQL-inmatning**, **komma åt från ovanliga platser eller data Center**, **komma åt från ett välkänt huvud konto eller potentiellt skadligt program och orsaka**SQL-autentiseringsuppgifter i **brutet** läge. mer information finns i [varningarna om avancerade hot skydd](threat-detection-overview.md#alerts).

Du kan få meddelanden om identifierade hot via [e-postmeddelanden](threat-detection-overview.md#explore-detection-of-a-suspicious-event) eller [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Avancerat skydd](threat-detection-overview.md) är en del av [Azure Defender för SQL](azure-defender-for-sql.md) -erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via den centrala Azure Defender för SQL-portalen.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Konfigurera Avancerat skydd i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till konfigurations sidan för den server som du vill skydda. I säkerhets inställningarna väljer du **Security Center**.
3. På konfigurations sidan för **Azure Defender** :

   - Aktivera Azure Defender på servern.
   - I **Inställningar för avancerat skydd**i rutan **skicka aviseringar till** text anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurera Advanced Threat Protection med PowerShell

Ett skript exempel finns i [Konfigurera granskning och Avancerat skydd med PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd](threat-detection-overview.md).
- Läs mer om [Avancerat skydd i SQL-hanterad instans](../managed-instance/threat-detection-configure.md).  
- Läs mer om [Azure Defender för SQL](azure-defender-for-sql.md).
- Läs mer om [granskning](../../azure-sql/database/auditing-overview.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
