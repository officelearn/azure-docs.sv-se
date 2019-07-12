---
title: Security attribut för Azure Load Balancer
description: En checklista med säkerhetsattribut för att utvärdera belastningsutjämnare
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800086"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Security attribut för Azure Load Balancer

Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Gäller inte | |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Gäller inte | |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Via den [med Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Gäller inte | |
| VNet-stöd för inmatning| Gäller inte | . |
| Stöd för isolering av nätverk och Firewalling| Gäller inte |  |
| Tvingad tunneltrafik support| Gäller inte | |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Se [Azure Monitor-loggarna för offentlig grundläggande belastningsutjämnare](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Gäller inte |  |
| Authorization| Gäller inte |  |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Se [Azure Monitor-loggarna för offentlig grundläggande belastningsutjämnare](load-balancer-monitor-log.md). |
| Data plan loggning och granskning | Gäller inte |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Gäller inte |  | 
