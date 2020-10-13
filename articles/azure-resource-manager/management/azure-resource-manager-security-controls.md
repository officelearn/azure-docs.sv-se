---
title: Säkerhetskontroller
description: En check lista över inbyggda säkerhets kontroller för utvärdering av Azure Resource Managers tjänsten.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054465"
---
# <a name="security-controls-for-azure-resource-manager"></a>Säkerhets kontroller för Azure Resource Manager

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Ja | HTTPS/TLS. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | E.t. | Azure Resource Manager lagrar inget kund innehåll, endast kontroll data. |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | |
| Krypterade API-anrop| Ja | |

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| Inga | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Inga |  |
| Stöd för Tvingad tunnel trafik| Inga |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Inga | |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Aktivitets loggar visar alla Skriv åtgärder (placering, POST, DELETE) som utförs på resurserna. Se [Visa aktivitets loggar för att granska åtgärder på resurser](view-activity-logs.md). |
| Loggning och granskning av data planet| E.t. | |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](../../active-directory/index.yml) baserad.|
| Auktorisering| Ja | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).
