---
title: Säkerhetskontroller
description: Lär dig mer om de säkerhetskontroller som används i Azure Backup-tjänsten. Dessa kontroller hjälper tjänsten att förebygga, identifiera och svara på säkerhetsproblem.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172145"
---
# <a name="security-controls-for-azure-backup"></a>Säkerhetskontroller för Azure Backup

Den här artikeln dokumenterar säkerhetskontrollerna som är inbyggda i Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation
|---|---|--|
| Support för tjänstens slutpunkt| Inga |  |  |
| Stöd för VNet-injektion| Inga |  |  |
| Stöd för nätverksisolering och brandväggar| Ja | Tvingad tunnelning stöds för säkerhetskopiering av virtuella datorer. Tvingad tunnelning stöds inte för arbetsbelastningar som körs inuti virtuella datorer. |  |
| Stöd för påtvingad tunnelning| Inga |  |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Log Analytics stöds via resursloggar. Mer information finns i [Övervaka Azure Backup-skyddade arbetsbelastningar med Hjälp av Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Kontroll- och hanteringsplan loggning och revision| Ja | Alla kundutlösta åtgärder från Azure-portalen loggas till aktivitetsloggar. |  |
| Loggning och granskning av dataplan| Inga | Azure Backup-dataplan kan inte nås direkt.  |  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Autentisering| Ja | Autentisering sker via Azure Active Directory. |  |
| Auktorisering| Ja | Kundskapade och inbyggda RBAC-roller används. Mer information finns i [Använda rollbaserad åtkomstkontroll för att hantera återställningspunkter för Azure Backup](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | | Dokumentation
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Använda kryptering av lagringstjänster för lagringskonton. |  |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Inga |  |  |
| Kryptering på kolumnnivå (Azure Data Services)| Inga |  |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Inga | Använda HTTPS. |  |
| API-anrop krypterade| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar| | Dokumentation
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja|  |  |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
