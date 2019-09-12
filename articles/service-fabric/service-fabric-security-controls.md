---
title: Säkerhets kontroller för Azure Service Fabric
description: En check lista över säkerhets kontroller för utvärdering av Azure-Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886393"
---
# <a name="security-controls-for-azure-service-fabric"></a>Säkerhets kontroller för Azure Service Fabric

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Azure Monitoring support och stöd från tredje part. |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kontroll Plans åtgärder körs genom processer för granskning och godkännanden. |
| Loggning och granskning av data planet| Gäller inte | Kunden äger klustret.  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Identitets-och åtkomst hantering (IAM) för anrop via SFRP. Anrop direkt till kluster slut punkten har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till vilken roll som möjligt. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja |  |
| Krypterade API-anrop| Ja | Service Fabric API-anrop görs via Azure Resource Manager. En giltig JSON Web token (JWT) krävs. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).