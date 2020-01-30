---
title: Säkerhets kontroller för Azure Cosmos DB
description: Få en check lista över säkerhets kontroller som nätverk, övervakning, identitet och data skydd för att utvärdera Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: d06afb3670b1c3af3f1acf0bd690c1e3e266a11c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771794"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Säkerhets kontroller för Azure Cosmos DB

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/Nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja | Med VNet-tjänstens slut punkt kan du konfigurera ett Azure Cosmos DB konto för att tillåta åtkomst endast från ett speciellt undernät i ett virtuellt nätverk (VNet). Du kan också kombinera VNet-åtkomst med brand Väggs regler. Mer information finns i [åtkomst Azure Cosmos dB från virtuella nätverk](VNet-service-endpoint.md). |
| Nätverks isolering och brand Väggs stöd| Ja | Med brand Väggs stöd kan du konfigurera ditt Azure Cosmos-konto så att det bara tillåter åtkomst från en godkänd uppsättning IP-adresser, ett intervall med IP-adresser och/eller moln tjänster. Läs mer i [Konfigurera IP-brandvägg i Azure Cosmos DB](how-to-configure-firewall.md).|
| Stöd för Tvingad tunnel trafik| Ja | Kan konfigureras på klient sidan på det VNet där de virtuella datorerna finns.   |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/Nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Alla begär Anden som skickas till Azure Cosmos DB loggas. [Azure-övervakning](../azure-monitor/overview.md), Azure-mått, Azure gransknings loggning stöds.  Du kan logga information som motsvarar data Plans begär Anden, fråga efter körnings statistik, frågetext, MongoDB-begäranden. Du kan också ställa in aviseringar. |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Azure aktivitets logg för åtgärder på konto nivå, till exempel brand väggar, virtuella nätverk, nycklar åtkomst och IAM. |
| Loggning och granskning av data planet | Ja | Loggning av diagnostik för behållar åtgärder, till exempel skapa behållare, etablera data flöde, indexerings principer och CRUD åtgärder för dokument. |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/Nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Ja på databas konto nivå; på data planet nivå använder Cosmos DB resurspooler och nyckel åtkomst. |
| Autentisering| Ja | Stöds på Azure Cosmos-kontot med huvud nycklar (primära och sekundära) och resurs-token. Du kan hämta Läs-och skriv-eller Läs behörighet till data med huvud nycklar. Med resurs-token får du begränsad åtkomst till resurser som dokument och behållare. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/Nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Alla Azure Cosmos-databaser och säkerhets kopior krypteras som standard. Se [data kryptering i Azure Cosmos DB](database-encryption-at-rest.md). |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Se [Konfigurera Kundhanterade nycklar för ditt Azure Cosmos DB-konto](how-to-setup-cmk.md)  |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | Endast i Tables API Premium. Alla API: er stöder inte den här funktionen. Se [Introduktion till Azure Cosmos DB: tabell-API](table-introduction.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Alla Azure Cosmos DB data krypteras vid överföring. |
| Krypterade API-anrop| Ja | Alla anslutningar till Azure Cosmos DB stöd för HTTPS. Azure Cosmos DB stöder även TLS 1,2.<br>Det är möjligt att framtvinga en lägsta TLS-version på Server sidan. Om du vill göra det kontaktar du [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com). |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Inga  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Ytterligare säkerhets kontroller för Cosmos DB

| Säkerhets kontroll | Ja/Nej | Anteckningar|
|---|---|--|
| Resurs delning mellan ursprung (CORS) | Ja | Se [Konfigurera CORS (Cross-Origin resurs delning)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).