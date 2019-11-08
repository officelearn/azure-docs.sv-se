---
title: Säkerhets kontroller för Azure Backup
description: Lär dig mer om de säkerhets kontroller som används i Azure Backups tjänsten. Dessa kontroller hjälper tjänsten att förhindra, identifiera och reagera på säkerhets risker.
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: 9d7bb2e9ea2d2cf9cce6c4f6d3b4f3346e7ce363
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747160"
---
# <a name="security-controls-for-azure-backup"></a>Säkerhets kontroller för Azure Backup

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Stöd för tjänst slut punkt| Nej |  |  |
| Stöd för VNet-injektering| Nej |  |  |
| Stöd för nätverks isolering och brand vägg| Ja | Tvingad tunnel trafik stöds för VM-säkerhetskopiering. Tvingad tunnel trafik stöds inte för arbets belastningar som körs i virtuella datorer. |  |
| Stöd för Tvingad tunnel trafik| Nej |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Log Analytics stöds via resurs loggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |  |
| Loggning och granskning av data planet| Nej | Det går inte att nå Azure Backup data planet direkt.  |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |  |
| Auktorisering| Ja | Kunden skapade och inbyggda RBAC-roller används. Mer information finns i [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | | Dokumentation
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Använda kryptering av lagrings tjänst för lagrings konton. |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Nej |  |  |
| Kryptering på kolumn nivå (Azure Data Services)| Nej |  |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Nej | Använda HTTPS. |  |
| Krypterade API-anrop| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja|  |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
