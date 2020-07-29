---
title: Säkerhets kontroller för Azure Service Fabric
description: Lär dig mer om säkerhets kontroller för Azure Service Fabric. Innehåller en check lista med inbyggda säkerhets kontroller.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645437"
---
# <a name="security-controls-for-azure-service-fabric"></a>Säkerhets kontroller för Azure Service Fabric

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Azure Monitoring support och stöd från tredje part. |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kontroll Plans åtgärder körs genom processer för granskning och godkännanden. |
| Loggning och granskning av data planet| E.t. | Kunden äger klustret.  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Identitets-och åtkomst hantering (IAM) för anrop via SFRP. Anrop direkt till kluster slut punkten har stöd för två roller: användare och administratör. Kunden kan mappa API: erna till vilken roll som möjligt. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på kolumn nivå (Azure Data Services)| E.t. |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja |  |
| Krypterade API-anrop| Ja | Service Fabric API-anrop görs via Azure Resource Manager. En giltig JSON Web token (JWT) krävs. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).