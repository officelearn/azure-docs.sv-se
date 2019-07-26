---
title: Säkerhetsattribut för Azure-ExpressRoute
description: En check lista över säkerhetsattribut för utvärdering av Azure-ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442190"
---
# <a name="security-attributes-for-azure-expressroute"></a>Säkerhetsattribut för Azure-ExpressRoute

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)|  Gäller inte | ExpressRoute lagrar inte kund information. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Nej | |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Gäller inte |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Gäller inte |  |
| Stöd för VNet-injektering| Gäller inte | |
| Stöd för nätverks isolering och brand vägg| Ja | Varje kund ingår i en egen routningsdomän och dirigeras sedan till sitt eget VNet |
| Stöd för Tvingad tunnel trafik| Gäller inte | Via Border Gateway Protocol (BGP). |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |
| Authorization|  Ja |Tjänst konto för gateway för Microsoft (GWM) (kontrollant); JIT-åtkomst (just in Time) för utveckling och OP. |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar| 
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet| Nej |   |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via nätverks resurs leverantören (NRP). |
