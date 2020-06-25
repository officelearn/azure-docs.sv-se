---
title: Säkerhets kontroller för Azure Event Hubs
description: Den här artikeln innehåller en check lista över säkerhets kontroller för utvärdering av Azure-Event Hubs (nätverk, identitet, data skydd osv.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85315388"
---
# <a name="security-controls-for-azure-event-hubs"></a>Säkerhets kontroller för Azure Event Hubs

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Yes |  |  |
| Stöd för VNet-injektering| No | |  |
| Stöd för nätverks isolering och brand vägg| Yes |  |  |
| Stöd för Tvingad tunnel trafik| No |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Yes | |  |
| Loggning och granskning av kontroll-och hanterings plan| Yes |  |  |
| Loggning och granskning av data planet| Yes |   |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Autentisering| Yes | | [Ge åtkomst till Azure Event Hubs](authorize-access-event-hubs.md), [bevilja åtkomst till Event Hubs resurser med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md), auktorisera [åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst](authorize-access-shared-access-signature.md) |
| Auktorisering|  Yes | | [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md), [autentisera ett program med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-application.md), [autentisera åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Yes | |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja. Tillgängligt för dedikerade kluster. | En kundhanterad nyckel i Azure-valv kan användas för att kryptera data i en händelse hubb i vila. | [Konfigurera Kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure Portal](configure-customer-managed-key.md) |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Yes | |  |
| Krypterade API-anrop| Yes |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
