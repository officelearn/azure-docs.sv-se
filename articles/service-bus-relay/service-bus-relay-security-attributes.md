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
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000156"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Security attribut för Azure Service Bus Relay

Den här artikeln dokumenteras security-attribut som är inbyggda i Azure Service Bus Relay.

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
