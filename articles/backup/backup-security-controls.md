---
title: Säkerhetskontroller
description: Lär dig mer om de säkerhets kontroller som används i Azure Backups tjänsten. Dessa kontroller hjälper tjänsten att förhindra, identifiera och reagera på säkerhets risker.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 96899c0669f3063232c36ad3ae1fec76a90e0a5c
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090870"
---
# <a name="security-controls-for-azure-backup"></a>Säkerhets kontroller för Azure Backup

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation
|---|---|--|--|
| Stöd för tjänst slut punkt| Nej |  |  |
| Stöd för VNet-injektering| Nej |  |  |
| Stöd för nätverks isolering och brand vägg| Ja | |  |
| Stöd för Tvingad tunnel trafik för virtuella Azure-datorer | Ja  |  |  |
| Stöd för Tvingad tunnel trafik för program som körs i virtuella Azure-datorer| Nej  |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Stöd för Azure-övervakning (till exempel Log Analytics, App Insights)| Ja | Log Analytics stöds via resurs loggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med Log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |  |
| Loggning och granskning av data planet| Nej | Det går inte att nå Azure Backup data planet direkt.  |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |  |
| Auktorisering| Ja | Kunden skapade och de inbyggda Azure-rollerna används. Mer information finns i [använda rollbaserad åtkomst kontroll i Azure för att hantera Azure Backup återställnings punkter](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Använda kryptering av lagrings tjänst för lagrings konton. |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Nej |  |  |
| Kryptering på kolumn nivå (Azure Data Services)| Nej |  |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Nej | Använda HTTPS. |  |
| Krypterade API-anrop| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Kommentarer| Dokumentation
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version och så vidare)| Ja|  |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
