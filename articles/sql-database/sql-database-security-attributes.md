---
title: Security attribut för Azure SQL Database
description: En checklista med säkerhetsattribut för att utvärdera Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798692"
---
# <a name="security-attributes-for-azure-sql-database"></a>Security attribut för Azure SQL Database

Den här artikeln beskrivs vanliga security-attribut som är inbyggda i Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database innehåller både [enkel databas](sql-database-single-index.yml) och [hanterad instans](sql-database-managed-instance.md). Följande gäller för båda erbjudanden utom där annat anges.

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner, till exempel klientsidan eller Always Encrypted</ul>| Ja | Kallas ”kryptering vid användning”, enligt beskrivningen i artikeln [Always Encrypted](sql-database-always-encrypted.md). Kryptering på serversidan använder [transparent datakryptering](transparent-data-encryption-azure-sql.md).|
| Kryptering under överföring:<ul><li>Azure ExpressRoute-kryptering</li><li>Kryptering i ett virtuellt nätverk</li><li>Kryptering mellan virtuella nätverk</ul>| Ja | Med hjälp av HTTPS. |
| Krypteringsnyckeln hantering, till exempel CMK eller BYOK| Ja | Både tjänsthanterad och kundhanterad nyckel hantering erbjuds. Erbjuds via [Azure Key Vault](../key-vault/index.yml). |
| På kolumnnivå kryptering som tillhandahålls av Azure-datatjänster| Ja | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Krypterade API-anrop| Ja | Med hjälp av HTTPS/SSL. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | Gäller för [enkel databas](sql-database-single-index.yml) endast. |
| Stöd för Azure Virtual Network-inmatning| Ja | Gäller för [hanterad instans](sql-database-managed-instance.md) endast. |
| Isolering av nätverk och brandvägg| Ja | Brandväggen på databasnivå såväl på servernivå. Isolering av nätverk är för [hanterad instans](sql-database-managed-instance.md) endast. |
| Tvingad tunneltrafik support| Ja | [Hanterad instans](sql-database-managed-instance.md) via en [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support, till exempel Log Analytics eller Application Insights för Azure| Ja | SecureSphere SIEM-lösning från Imperva, också stöds via [Azure Event Hubs](../event-hubs/index.yml) integrering via [SQL-granskning](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory (Azure AD) |
| Authorization| Ja | Ingen |

## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontrollplanet och Hanteringsplanet loggning och granskning| Ja | Ja för endast vissa händelser |
| Dataplanet loggning och granskning | Ja | Via [SQL-granskning](sql-database-auditing.md) |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurationshantering support, till exempel versionshantering av konfigurationen| Nej  | Ingen |

## <a name="additional-security-attributes-for-sql-database"></a>Attribut för ytterligare säkerhet för SQL-databas

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: utvärdering av säkerhetsrisker | Ja | Se [Sårbarhetsbedömning för SQL service hjälper dig att identifiera databasen sårbarheter](sql-vulnerability-assessment.md). |
| Förebyggande: dataidentifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md). |
| Identifiering: hotidentifiering | Ja | Se [Avancerat skydd för Azure SQL Database](sql-database-threat-detection-overview.md). |
