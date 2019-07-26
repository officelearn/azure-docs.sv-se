---
title: Säkerhetsattribut för Azure SQL Database
description: En check lista över säkerhetsattribut för utvärdering av Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444381"
---
# <a name="security-attributes-for-azure-sql-database"></a>Säkerhetsattribut för Azure SQL Database

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database innehåller både [en enkel databas och en](sql-database-single-index.yml) [hanterad instans](sql-database-managed-instance.md). Följande poster gäller för båda erbjudandena utom i de fall där annat anges.

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila:<ul><li>Kryptering på serversidan</li><li>Kryptering på Server sidan med Kundhanterade nycklar</li><li>Andra krypterings funktioner, till exempel klient sidan eller Always Encrypted</ul>| Ja | Kallas "kryptering i användning", enligt beskrivningen i artikeln [Always Encrypted](sql-database-always-encrypted.md). Kryptering på Server sidan använder [transparent data kryptering](transparent-data-encryption-azure-sql.md).|
| Kryptering under överföring:<ul><li>Azure ExpressRoute-kryptering</li><li>Kryptering i ett virtuellt nätverk</li><li>Kryptering mellan virtuella nätverk</ul>| Ja | Använda HTTPS. |
| Kryptering – nyckel hantering, till exempel CMK eller BYOK| Ja | Både hanterad och kundhanterad nyckel hantering erbjuds. Den senare erbjuds via [Azure Key Vault](../key-vault/index.yml). |
| Kryptering på kolumn nivå som tillhandahålls av Azure Data Services| Ja | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Krypterade API-anrop| Ja | Använda HTTPS/SSL. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Gäller endast för [en enskild databas](sql-database-single-index.yml) . |
| Stöd för Azure Virtual Network-injektering| Ja | Gäller endast för [hanterade instanser](sql-database-managed-instance.md) . |
| Nätverks isolering och brand Väggs stöd| Ja | Brand väggen på både databas nivå och server nivå. Nätverks isolering är endast för [hanterad instans](sql-database-managed-instance.md) . |
| Stöd för Tvingad tunnel trafik| Ja | [Hanterad instans](sql-database-managed-instance.md) via en [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för Azure-övervakning, till exempel Log Analytics eller Application Insights| Ja | SecureSphere, SIEM-lösningen från Imperva, stöds också via [Azure Event Hubs](../event-hubs/index.yml) integration via [SQL-granskning](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory (Azure AD) |
| Authorization| Ja | Inga |

## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll – plan och hantering – plan loggning och granskning| Ja | Ja endast för vissa händelser |
| Data Plans loggning och granskning | Ja | Via [SQL audit](sql-database-auditing.md) |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfiguration – hanterings stöd, till exempel konfiguration av versioner| Nej  | Inga |

## <a name="additional-security-attributes-for-sql-database"></a>Ytterligare säkerhetsattribut för SQL Database

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: sårbarhets bedömning | Ja | Se [SQL sårbarhet Assessment service hjälper dig att identifiera databas sårbarheter](sql-vulnerability-assessment.md). |
| Förebyggande: data identifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse data identifiering & klassificering](sql-database-data-discovery-and-classification.md). |
| Identifiering: Hot identifiering | Ja | Se [Avancerat skydd för Azure SQL Database](sql-database-threat-detection-overview.md). |
