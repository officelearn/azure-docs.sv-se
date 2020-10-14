---
title: Säkerhetskontroller
description: Lär dig mer om de säkerhets kontroller som används i Azure Backups tjänsten. Dessa kontroller hjälper tjänsten att förhindra, identifiera och reagera på säkerhets risker.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 40bd35bdf83d336aebd37cdda0a0b59164ac9f7a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055893"
---
# <a name="security-controls-for-azure-backup"></a>Säkerhets kontroller för Azure Backup

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation
|---|---|--|--|
| Stöd för tjänst slut punkt| No |  |  |
| Stöd för VNet-injektering| No |  |  |
| Stöd för nätverks isolering och brand vägg| Yes | |  |
| Stöd för Tvingad tunnel trafik för virtuella Azure-datorer | Yes  |  |  |
| Stöd för Tvingad tunnel trafik för program som körs i virtuella Azure-datorer| No  |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Stöd för Azure-övervakning (till exempel Log Analytics, App Insights)| Yes | Log Analytics stöds via resurs loggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med Log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Loggning och granskning av kontroll-och hanterings plan| Yes | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |  |
| Loggning och granskning av data planet| No | Det går inte att nå Azure Backup data planet direkt.  |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Autentisering| Yes | Autentisering är via Azure Active Directory. |  |
| Auktorisering| Yes | Kunden skapade och de inbyggda Azure-rollerna används. Mer information finns i [använda Role-Based Access Control för att hantera Azure Backup återställnings punkter](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Yes | Använda kryptering av lagrings tjänst för lagrings konton. |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | No |  |  |
| Kryptering på kolumn nivå (Azure Data Services)| No |  |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| No | Använda HTTPS. |  |
| Krypterade API-anrop| Yes |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version och så vidare)| Ja|  |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
