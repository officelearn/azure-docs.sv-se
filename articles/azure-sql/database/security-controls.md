---
title: Säkerhetskontroller
description: En check lista över säkerhets kontroller för utvärdering av Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84668431"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Säkerhets kontroller för Azure SQL Database-och SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure SQL Database och Azure SQL-hanterad instans.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| Yes | Gäller endast för [SQL Database](../index.yml) . |
| Stöd för Azure Virtual Network-injektering| Yes | Gäller endast för [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Nätverks isolering och brand Väggs stöd| Yes | Brand väggen på både databas nivå och server nivå. Nätverks isolering är endast för [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Stöd för Tvingad tunnel trafik| Yes | [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) via en [ExpressRoute](../expressroute/../index.yml) VPN. |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Stöd för Azure-övervakning, till exempel Log Analytics eller Application Insights| Yes | SecureSphere, SIEM-lösningen från Imperva, stöds också via [Azure Event Hubs](../event-hubs/../index.yml) integration via [SQL-granskning](../../azure-sql/database/auditing-overview.md). |
| Kontroll – plan och hantering – plan loggning och granskning| Yes | Ja endast för vissa händelser |
| Data Plans loggning och granskning | Yes | Via [SQL audit](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Yes | Azure Active Directory (Azure AD) |
| Auktorisering| Yes | Inga |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Yes | Kallas "kryptering i användning", enligt beskrivningen i artikeln [Always Encrypted](always-encrypted-certificate-store-configure.md). Kryptering på Server sidan använder [transparent data kryptering](transparent-data-encryption-tde-overview.md).|
| Kryptering under överföring:<ul><li>Azure ExpressRoute-kryptering</li><li>Kryptering i ett virtuellt nätverk</li><li>Kryptering mellan virtuella nätverk</ul>| Yes | Använda HTTPS. |
| Kryptering – nyckel hantering, till exempel CMK eller BYOK| Yes | Både hanterad och kundhanterad nyckel hantering erbjuds. Den senare erbjuds via [Azure Key Vault](../key-vault/../index.yml). |
| Kryptering på kolumn nivå som tillhandahålls av Azure Data Services| Yes | Via [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Krypterade API-anrop| Yes | Använda HTTPS/TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfiguration – hanterings stöd, till exempel konfiguration av versioner| No  | Inga |

## <a name="additional-security-controls-for-sql-database"></a>Ytterligare säkerhets kontroller för SQL Database

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Förebyggande: sårbarhets bedömning | Yes | Se [SQL sårbarhet Assessment service hjälper dig att identifiera databas sårbarheter](sql-vulnerability-assessment.md). |
| Förebyggande: data identifiering och klassificering  | Yes | Se [Azure SQL Database och SQL Data Warehouse data identifiering & klassificering](data-discovery-and-classification-overview.md). |
| Identifiering: Hot identifiering | Yes | Se [Avancerat skydd för Azure SQL Database](threat-detection-overview.md). |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).
