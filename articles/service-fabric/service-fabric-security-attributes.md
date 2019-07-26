---
title: Säkerhetsattribut för Azure Service Fabric
description: En check lista över säkerhetsattribut för utvärdering av Azure-Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443861"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Säkerhetsattribut för Azure Service Fabric

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja |  |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kunden äger klustret och den skalnings uppsättning för virtuella datorer som klustret har skapats på. Azure Disk Encryption kan aktive ras på den virtuella datorns skal uppsättning. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja | Service Fabric API-anrop görs via Azure Resource Manager. En giltig JSON Web token (JWT) krävs. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Ja |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Azure Monitoring support och stöd från tredje part. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Identitets-och åtkomst hantering (IAM) för anrop via SFRP. Anrop direkt till kluster slut punkten har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till vilken roll som möjligt. |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kontroll Plans åtgärder körs genom processer för granskning och godkännanden. |
| Loggning och granskning av data planet| Gäller inte | Kunden äger klustret.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |