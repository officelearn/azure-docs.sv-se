---
title: Vanliga säkerhetsattribut för Azure ExpressRoute
description: En lista med vanliga security attribut för att utvärdera Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083280"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Vanliga säkerhetsattribut för Azure ExpressRoute

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>|  Gäller inte | ExpressRoute lagrar inte kunddata. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Nej | |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Gäller inte |  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml) och HTTPS. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Gäller inte |  |
| vNET-stöd för inmatning| Gäller inte | |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Varje kund finns i en egen routningsdomän och dirigering till ett eget virtuellt nätverk |
| Tvingad tunneltrafik support| Gäller inte | Via BGP (Border Gateway Protocol). |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Se [ExpressRoute-övervakning, mått och aviseringar](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Tjänstkontot för Gateway för Microsoft (GWM) (controller); Just-in-Time JIT-åtkomst för utveckling och OP. |
| Auktorisering|  Ja |Tjänstkontot för Gateway för Microsoft (GWM) (controller); Just-in-Time JIT-åtkomst för utveckling och OP. |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar| 
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja |  |
| Data plan loggning och granskning| Nej |   |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Via Nätverksresursprovidern (NRP). |
