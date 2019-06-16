---
title: Security attribut för Azure Cosmos DB
description: En checklista med säkerhetsattribut för att utvärdera Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480460"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Security attribut för Azure Cosmos DB

Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/Nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Ja | Alla Cosmos DB-databaser och säkerhetskopieringar krypteras som standard. Se [datakryptering i Azure Cosmos DB](database-encryption-at-rest.md). Kryptering på serversidan med Kundhanterade nycklar stöds inte. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Ja | Alla Azure Cosmos DB-data krypteras vid överföring. |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Nej |  |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Ja | Endast i tabeller API Premium. Inte alla API: er stöder den här funktionen. Se [introduktion till Azure Cosmos DB: Tabell-API](table-introduction.md). |
| API-anrop som är krypterad| Ja | Alla anslutningar till Azure Cosmos DB stöder HTTPS. Azure Cosmos DB stöder TLS 1.2-anslutningar, men detta är inte ännu tvingande. Kunder har stängt av lägre nivå TLS på sina slutet, de kan garantera för att ansluta till Cosmos DB.  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/Nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja |  |
| vNET-stöd för inmatning| Ja | Du kan konfigurera ett Azure Cosmos DB-konto för att tillåta åtkomst från ett specifikt undernät i ett virtuellt nätverk (VNet) med VNet-tjänstslutpunkt. Du kan också kombinera VNet-åtkomst med brandväggsregler.  Se [åtkomst till Azure Cosmos DB från virtuella nätverk](vnet-service-endpoint.md). |
| Stöd för isolering av nätverk och Firewalling| Ja | Med brandväggen kan konfigurera du ditt Azure Cosmos-konto för att tillåta åtkomst från en godkänd uppsättning IP-adresser, ett intervall med IP-adresser och/eller molntjänster. Se [konfigurera IP-brandvägg i Azure Cosmos DB](how-to-configure-firewall.md).|
| Stöd för Tvingad tunneltrafik | Ja | Kan konfigureras på klientsidan på det virtuella nätverket där de virtuella datorerna finns.   |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/Nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Alla förfrågningar som skickas till Azure Cosmos DB loggas. [Azure Monitoring](../azure-monitor/overview.md), Azure-mått, Azure-granskningsloggning stöds.  Du kan logga information som motsvarar begäranden av plan, runtime frågestatistik, frågetexten MongoDB-begäranden. Du kan också konfigurera aviseringar. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/Nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Ja på kontonivå databasen; på nivån data dataplanet använder Cosmos DB resurstokens och nyckelåtkomst. |
| Auktorisering| Ja | Stöds på Azure Cosmos-konto med huvudnycklar (primära och sekundära) och resurstokens. Du kan få full eller skrivskyddad åtkomst till data med huvudnycklar. Resurstokens Tillåt tidsbegränsat åtkomst till resurser, till exempel dokument och behållare. |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/Nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Azure-aktivitetslogg för kontot på åtgärder som till exempel brandväggar, virtuella nätverk, åtkomst till nycklar och IAM. |
| Data-dataplaner loggning och granskning | Ja | Diagnostik övervakning loggning för behållare på åtgärder som skapar behållare, etablera dataflöde, indexering av principer och CRUD-åtgärder på dokument. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Nej  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Attribut för ytterligare säkerhet för Cosmos DB

| Säkerhetsattributet | Ja/Nej | Anteckningar|
|---|---|--|
| Cross Origin Resource Sharing (CORS) | Ja | Se [konfigurera Cross-Origin Resource Sharing (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
