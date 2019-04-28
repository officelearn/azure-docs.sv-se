---
title: Vanliga säkerhetsattribut för Azure Service Fabric
description: En lista med vanliga security attribut för att utvärdera Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: b25400f21e24b18a0a21b9d849bdd505f6d47aac
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765752"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Vanliga säkerhetsattribut för Azure Service Fabric

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kunden äger klustret och skalningsuppsättning för virtuell dator i klustret har skapats. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja |  |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunden äger klustret och skalningsuppsättning för virtuell dator i klustret har skapats. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Service Fabric-API-anrop görs via Azure Resource Manager. Det krävs en giltig JSON webbtoken (JWT). |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| vNET-stöd för inmatning| Ja |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG). |
| Tvingad tunneltrafik support| Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Med hjälp av Azure-övervakning stöd och stöd från tredje part. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för anrop via SFRP. Anrop direkt till slutpunkten för klustret har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till någon av rollerna. |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla kontrollplanåtgärder kör via processer för att granska och godkännanden. |
| Data plan loggning och granskning| Gäller inte | Kunden äger klustret.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | |