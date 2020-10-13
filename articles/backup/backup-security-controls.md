---
title: Säkerhetskontroller
description: Lär dig mer om de säkerhets kontroller som används i Azure Backups tjänsten. Dessa kontroller hjälper tjänsten att förhindra, identifiera och reagera på säkerhets risker.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7ff3ff5c1b024a228778b0214e67239d3c8ab721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89418763"
---
# <a name="security-controls-for-azure-backup"></a>Säkerhets kontroller för Azure Backup

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|--|
| Stöd för tjänst slut punkt| Inga |  |  |
| Stöd för VNet-injektering| Inga |  |  |
| Stöd för nätverks isolering och brand vägg| Ja | |  |
| Stöd för Tvingad tunnel trafik för virtuella Azure-datorer | Ja  |  |  |
| Stöd för Tvingad tunnel trafik för program som körs i virtuella Azure-datorer| Inga  |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation
|---|---|--|--|
| Stöd för Azure-övervakning (till exempel Log Analytics, App Insights)| Ja | Log Analytics stöds via resurs loggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |  |
| Loggning och granskning av data planet| Inga | Det går inte att nå Azure Backup data planet direkt.  |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation
|---|---|--|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |  |
| Auktorisering| Ja | Kunden skapade och de inbyggda Azure-rollerna används. Mer information finns i [använda Role-Based Access Control för att hantera Azure Backup återställnings punkter](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! | Dokumentation
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Använda kryptering av lagrings tjänst för lagrings konton. |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Inga |  |  |
| Kryptering på kolumn nivå (Azure Data Services)| Inga |  |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Inga | Använda HTTPS. |  |
| Krypterade API-anrop| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!| Dokumentation
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version och så vidare)| Ja|  |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
