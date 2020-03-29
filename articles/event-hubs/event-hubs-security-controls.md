---
title: Säkerhetskontroller för Azure Event Hubs
description: Den här artikeln innehåller en checklista med säkerhetskontroller för utvärdering av Azure Event Hubs (nätverk, identitet, dataskydd osv.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309514"
---
# <a name="security-controls-for-azure-event-hubs"></a>Säkerhetskontroller för Azure Event Hubs

Den här artikeln dokumenterar säkerhetskontrollerna som är inbyggda i Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Support för tjänstens slutpunkt| Ja |  |  |
| Stöd för VNet-injektion| Inga | |  |
| Stöd för nätverksisolering och brandväggar| Ja |  |  |
| Stöd för påtvingad tunnelning| Inga |  |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | |  |
| Kontroll- och hanteringsplan loggning och revision| Ja |  |  |
| Loggning och granskning av dataplan| Ja |   |  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | | [Auktorisera åtkomst till Azure Event Hubs](authorize-access-event-hubs.md), [auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md), [auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md) |
| Auktorisering|  Ja | | [Autentisera en hanterad identitet med Azure Active Directory för åtkomst till eventhubbarresurser,](authenticate-managed-identity.md) [autentisera ett program med Azure Active Directory för åtkomst till eventhubbar-resurser,](authenticate-application.md) [autentisera åtkomst till eventhubbar-resurser med hjälp av SAS (Shared Access Signatures)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar |  Ja | |  |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja. Tillgänglig för dedikerade kluster. | En kundhanterad nyckel i Azure KeyVault kan användas för att kryptera data på en eventhubb i vila. | [Konfigurera kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure-portalen](configure-customer-managed-key.md) |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | |  |
| API-anrop krypterade| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | |  |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
