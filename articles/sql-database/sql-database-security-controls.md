---
title: Säkerhetskontroller
description: En checklista med säkerhetskontroller för utvärdering av Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190693"
---
# <a name="security-controls-for-azure-sql-database"></a>Säkerhetskontroller för Azure SQL Database

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure SQL Database.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database innehåller både [en enda databas](sql-database-single-index.yml) och [hanterad instans](sql-database-managed-instance.md). Följande poster gäller för båda erbjudandena om inte annat anges.

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ja | Gäller endast [för enstaka databaser.](sql-database-single-index.yml) |
| Azure Virtual Network-injektionsstöd| Ja | Gäller endast [hanterad instans.](sql-database-managed-instance.md) |
| Stöd för nätverksisolering och brandvägg| Ja | Brandvägg på både databasnivå och servernivå. Nätverksisolering är endast för [hanterade instanser.](sql-database-managed-instance.md) |
| Stöd för påtvingad tunnelning| Ja | [Hanterad instans](sql-database-managed-instance.md) via en [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure-övervakningsstöd, till exempel Logganalys eller Application Insights| Ja | SecureSphere, SIEM-lösningen från Imperva, stöds också via [Azure Event Hubs-integrering](../event-hubs/index.yml) via [SQL-granskning](sql-database-auditing.md). |
| Loggning och revision av styrplan och ledningsplan| Ja | Ja endast för vissa händelser |
| Loggning och granskning av dataplan | Ja | Via [SQL-granskning](sql-database-auditing.md) |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory (Azure AD) |
| Auktorisering| Ja | Inget |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Kallas "kryptering-i-användning", som beskrivs i artikeln [Alltid krypterad](sql-database-always-encrypted.md). Kryptering på serversidan använder [transparent datakryptering](transparent-data-encryption-azure-sql.md).|
| Kryptering under överföring:<ul><li>Azure ExpressRoute-kryptering</li><li>Kryptering i ett virtuellt nätverk</li><li>Kryptering mellan virtuella nätverk</ul>| Ja | Använda HTTPS. |
| Krypteringsnyckelhantering, till exempel CMK eller BYOK| Ja | Både servicehanterad och kundhanterad nyckelhantering erbjuds. Det senare erbjuds via [Azure Key Vault](../key-vault/index.yml). |
| Kryptering på kolumnnivå som tillhandahålls av Azure-datatjänster| Ja | Genom [alltid krypterad](sql-database-always-encrypted.md). |
| Krypterade API-anrop| Ja | Använda HTTPS/TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering, till exempel versionshantering av konfiguration| Inga  | Inget |

## <a name="additional-security-controls-for-sql-database"></a>Ytterligare säkerhetskontroller för SQL Database

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: sårbarhetsbedömning | Ja | Se [tjänsten SQL Vulnerability Assessment hjälper dig att identifiera databassäkerhetsproblem](sql-vulnerability-assessment.md). |
| Förebyggande: dataidentifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse dataidentifiering & klassificering](sql-database-data-discovery-and-classification.md). |
| Identifiering: hotidentifiering | Ja | Se [Avancerat skydd mot hot för Azure SQL Database](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
