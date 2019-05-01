---
title: Vanliga säkerhetsattribut för Azure Service Bus Relay
description: En lista med vanliga security attribut för att utvärdera Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927499"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Vanliga säkerhetsattribut för Azure Service Bus Relay

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>|  Gäller inte | Relay är en web-socket och data sparas inte. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Tjänsten kräver TLS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej | Använder Microsoft TLS-certifikat.  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | HTTPS. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Nej |  |
| Tvingad tunneltrafik support| Gäller inte | Relay är TLS-tunnel  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Via SAS. |
| Auktorisering|  Ja | Via SAS. |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml). |
| Data plan loggning och granskning| Ja | Anslutningen lyckas / fel och fel och loggas.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Via [med Azure Resource Manager](../azure-resource-manager/index.yml).|
