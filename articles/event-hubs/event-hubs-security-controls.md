---
title: Säkerhets kontroller för Azure Event Hubs
description: Den här artikeln innehåller en check lista över säkerhets kontroller för utvärdering av Azure-Event Hubs (nätverk, identitet, data skydd osv.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309514"
---
# <a name="security-controls-for-azure-event-hubs"></a>Säkerhets kontroller för Azure Event Hubs

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Ja |  |  |
| Stöd för VNet-injektering| Nej | |  |
| Stöd för nätverks isolering och brand vägg| Ja |  |  |
| Stöd för Tvingad tunnel trafik| Nej |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |  |
| Loggning och granskning av data planet| Ja |   |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | | [Ge åtkomst till Azure Event Hubs](authorize-access-event-hubs.md), [bevilja åtkomst till Event Hubs resurser med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md), auktorisera [åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst](authorize-access-shared-access-signature.md) |
| Auktorisering|  Ja | | [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md), [autentisera ett program med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-application.md), [autentisera åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ja | |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja. Tillgängligt för dedikerade kluster. | En kundhanterad nyckel i Azure-valv kan användas för att kryptera data i en händelse hubb i vila. | [Konfigurera Kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure Portal](configure-customer-managed-key.md) |
| Kryptering på kolumn nivå (Azure Data Services)| Ej tillämpligt | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | |  |
| Krypterade API-anrop| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
