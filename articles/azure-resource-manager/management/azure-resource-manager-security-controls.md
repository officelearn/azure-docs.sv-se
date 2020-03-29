---
title: Säkerhetskontroller
description: En checklista med inbyggda säkerhetskontroller för utvärdering av Azure Resource Manager-tjänsten.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485629"
---
# <a name="security-controls-for-azure-resource-manager"></a>Säkerhetskontroller för Azure Resource Manager

I den här artikeln dokumenteras de säkerhetskontroller som är inbyggda i Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | HTTPS/TLS. |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ej tillämpligt | Azure Resource Manager lagrar inget kundinnehåll, bara kontrollera data. |
| Kryptering på kolumnnivå (Azure Data Services)| Ja | |
| API-anrop krypterade| Ja | |

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Inga | |
| Stöd för VNet-injektion| Ja | |
| Stöd för nätverksisolering och brandväggar| Inga |  |
| Stöd för påtvingad tunnelning| Inga |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Inga | |
| Kontroll- och hanteringsplan loggning och revision| Ja | Aktivitetsloggar exponerar alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser. se [Visa aktivitetsloggar för granskningsåtgärder på resurser](view-activity-logs.md). |
| Loggning och granskning av dataplan| Ej tillämpligt | |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active](/azure/active-directory) Directory-baserad.|
| Auktorisering| Ja | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja |  |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../../security/fundamentals/security-controls.md).