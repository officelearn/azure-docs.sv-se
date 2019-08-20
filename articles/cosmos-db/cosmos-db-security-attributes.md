---
title: Säkerhetsattribut för Azure Cosmos DB
description: En check lista över säkerhetsattribut för utvärdering av Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: f1b578ff70e8a2795ba886e6d0c75eff521f77c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615531"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Säkerhetsattribut för Azure Cosmos DB

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/Nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner) | Ja | Alla Cosmos-databaser och säkerhets kopior krypteras som standard. Se [data kryptering i Azure Cosmos DB](database-encryption-at-rest.md). Kryptering på Server sidan med Kundhanterade nycklar stöds inte. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Alla Azure Cosmos DB data krypteras vid överföring. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | Endast i Tables API Premium. Alla API: er stöder inte den här funktionen. Se [introduktion till Azure Cosmos DB: Tabell-API](table-introduction.md). |
| Krypterade API-anrop| Ja | Alla anslutningar till Azure Cosmos DB stöd för HTTPS. Azure Cosmos DB stöder även TLS 1,2-anslutningar, men detta är inte tvingande än. Om kunderna stänger av lågnivå-TLS på deras sida, kan de se till att de ansluter till Cosmos DB.  |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/Nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja | Med VNet-tjänstens slut punkt kan du konfigurera ett Azure Cosmos DB konto för att tillåta åtkomst endast från ett speciellt undernät i ett virtuellt nätverk (VNet). Du kan också kombinera VNet-åtkomst med brand Väggs regler.  Se [åtkomst Azure Cosmos dB från virtuella nätverk](VNet-service-endpoint.md). |
| Stöd för nätverks isolering och brand vägg| Ja | Med brand Väggs stöd kan du konfigurera ditt Azure Cosmos-konto så att det bara tillåter åtkomst från en godkänd uppsättning IP-adresser, ett intervall med IP-adresser och/eller moln tjänster. Se [Konfigurera IP-brandvägg i Azure Cosmos DB](how-to-configure-firewall.md).|
| Stöd för Tvingad tunnel trafik| Ja | Kan konfigureras på klient sidan på det VNet där de virtuella datorerna finns.   |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Alla begär Anden som skickas till Azure Cosmos DB loggas. [Azure-övervakning](../azure-monitor/overview.md), Azure-mått, Azure gransknings loggning stöds.  Du kan logga information som motsvarar data Plans begär Anden, fråga efter körnings statistik, frågetext, MongoDB-begäranden. Du kan också ställa in aviseringar. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Ja på databas konto nivå; på data planet nivå använder Cosmos DB resurspooler och nyckel åtkomst. |
| Authorization| Ja | Stöds på Azure Cosmos-kontot med huvud nycklar (primära och sekundära) och resurs-token. Du kan hämta Läs-och skriv-eller Läs behörighet till data med huvud nycklar. Med resurs-token får du begränsad åtkomst till resurser som dokument och behållare. |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure aktivitets logg för åtgärder på konto nivå, till exempel brand väggar, virtuella nätverk, nycklar åtkomst och IAM. |
| Loggning och granskning av data planet | Ja | Loggning av diagnostik för behållar åtgärder, till exempel skapa behållare, etablera data flöde, indexerings principer och CRUD åtgärder för dokument. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Nej  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Ytterligare säkerhetsattribut för Cosmos DB

| Säkerhetsattribut | Ja/Nej | Anteckningar|
|---|---|--|
| Resurs delning mellan ursprung (CORS) | Ja | Se [Konfigurera CORS (Cross-Origin resurs delning)](how-to-configure-cross-origin-resource-sharing.md). |
