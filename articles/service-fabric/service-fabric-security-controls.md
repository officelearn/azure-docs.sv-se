---
title: Säkerhetskontroller för Azure Service Fabric
description: Läs mer om säkerhetskontroller för Azure Service Fabric. Innehåller en checklista med inbyggda säkerhetskontroller.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645437"
---
# <a name="security-controls-for-azure-service-fabric"></a>Säkerhetskontroller för Azure Service Fabric

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ja |  |
| Stöd för VNet-injektion| Ja |  |
| Stöd för nätverksisolering och brandväggar| Ja | Använda säkerhetsgrupper för nätverk (NSG). |
| Stöd för påtvingad tunnelning| Ja | Azure-nätverk ger påtvingad tunnel. |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Använda Azure-övervakningsstöd och support från tredje part. |
| Kontroll- och hanteringsplan loggning och revision| Ja | Alla styrplansoperationer går igenom processer för granskning och godkännanden. |
| Loggning och granskning av dataplan| Ej tillämpligt | Kunden äger klustret.  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering sker via Azure Active Directory. |
| Auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för samtal via SFRP. Anrop direkt till klusterslutpunkten stöder två roller: Användare och Administratör. Kunden kan mappa API:erna till båda rollerna. |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Kunden äger klustret och den virtuella datorn skala uppsättning som klustret är byggt. Azure-diskkryptering kan aktiveras på den virtuella datorns skalningsuppsättning. |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Kunden äger klustret och den virtuella datorn skala uppsättning som klustret är byggt. Azure-diskkryptering kan aktiveras på den virtuella datorns skalningsuppsättning. |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja |  |
| API-anrop krypterade| Ja | Api-anrop för tjänstinfrastruktur görs via Azure Resource Manager. En giltig JSON-webbtoken (JWT) krävs. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).