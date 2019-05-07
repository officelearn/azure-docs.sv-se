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
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204242"
---
# <a name="security-attributes-for-azure-sql-database"></a>Security attribut för Azure SQL Database

Den här artikeln beskrivs vanliga säkerhetsattribut i Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL Database innehåller både [enkel databas](sql-database-single-index.yml) och [hanterad instans](sql-database-managed-instance.md). Posterna nedan gäller för båda erbjudanden, utom där annat anges.

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kallas ”kryptering vid användning”, enligt beskrivningen i artikeln [Always Encrypted](sql-database-always-encrypted.md). Tjänstsidan kryptering använder [transparent datakryptering](transparent-data-encryption-azure-sql.md) (TDE).|
| Kryptering under överföring:<ul><li>ExpressRoute-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Med hjälp av HTTPS. |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Både tjänsthanterad och kundhanterad nyckel hantering erbjuds (det senare via [Azure Key Vault](../key-vault/index.yml). |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Ja | Via [Always Encrypted](sql-database-always-encrypted.md). |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS/SSL. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja | Gäller för [enkel databas](sql-database-single-index.yml) endast. |
| vNET-stöd för inmatning| Ja | Gäller för [hanterad instans](sql-database-managed-instance.md) endast. |
| Nätverksisolering / brandväggsfunktioner support| Ja | Brandväggen på båda databas - och server-nivå. nätverksisolering för [hanterad instans](sql-database-managed-instance.md) endast |
| Stöd för Tvingad tunneltrafik | Ja | [hanterad instans](sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | SIEM-lösning från tredje part från Imperva (SecureSphere) är också stöds via [Azure Event Hubs](../event-hubs/index.yml) integrering via [SQL audit](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja |  |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Ja för endast vissa händelser. |
| Data-dataplaner loggning och granskning | Ja | Via [SQL audit](sql-database-auditing.md). |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Nej  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Attribut för ytterligare säkerhet för SQL-databas

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Förebyggande: utvärdering av säkerhetsrisker | Ja | Se [Sårbarhetsbedömning för SQL service hjälper dig att identifiera databasen sårbarheter](sql-vulnerability-assessment.md). |
| Förebyggande: dataidentifiering och klassificering  | Ja | Se [Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md). |
| Identifiering: hotidentifiering | Ja | Se [Avancerat skydd för Azure SQL Database](sql-database-threat-detection-overview.md). |
