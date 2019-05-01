---
title: Vanliga säkerhetsattribut för Azure API Management
description: En lista med vanliga security attribut för utvärdering av API Management
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508144"
---
# <a name="common-security-attributes-for-api-management"></a>Vanliga säkerhetsattribut för API Management

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i API Management.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja (endast tjänstsidan kryptering) | Känsliga data, till exempel certifikat, nycklar och värden för krypterad hemlighet är krypterade med tjänsthanterad per instans för för tjänsten. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | [Express Route](../expressroute/index.yml) och tillhandahålls krypteringen av virtuellt nätverk av [Azure-nätverk](../virtual-network/index.yml). |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej | Alla krypteringsnycklar är per instans och tjänsthanterade. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Hantering av plan anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. Det krävs en giltig JSON webbtoken (JWT).  Plan dataöverföringar kan skyddas med TLS och en av autentiseringsmekanismer som stöds (t.ex. klientcertifikatet eller JWT).
 |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej | |
| VNet-stöd för inmatning| Ja | |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG) och Azure Application Gateway (eller andra programinstallation) respektive. |
| Tvingad tunneltrafik support| Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | |
| Auktorisering| Ja | |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | [Azure Monitor-aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md) |
| Data plan loggning och granskning| Ja | [Azure Monitor-diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) och (frivilligt) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Med hjälp av den [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Säkerhetsproblem söker igenom falska positiva identifieringar

Det här avsnittet beskrivs vanliga säkerhetsproblem som inte påverkar Azure API Management.

| Sårbarhet               | Beskrivning                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed är säkerhetsproblem i implementeringen av TLS SessionTicket tillägget finns i vissa F5-produkter. Det gör läckage (”genomsläpplighet”) av upp till 31 byte av data från oinitierad minne. Detta orsakas av TLS-stack utfyllnad ett sessions-ID som skickades från klienten, med data så att de blir 32 bitar långt. |