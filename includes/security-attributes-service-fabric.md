---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 41a8d6c2812b0fbd1d7e2fd4fd88a4343b52714f
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007270"
---
## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja |  |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Service Fabric-API-anrop görs via Azure Resource Manager. Det krävs en giltig JSON webbtoken (JWT). |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja |  |
| vNET-stöd för inmatning| Ja |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG). |
| Stöd för Tvingad tunneltrafik | Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Med hjälp av Azure-övervakning stöd och stöd från tredje part. |

## <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för anrop via SFRP. Anrop direkt till slutpunkten för klustret har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till någon av rollerna. |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Alla kontrollplanåtgärder kör via processer för att granska och godkännanden. |
| Data-dataplaner loggning och granskning| Gäller inte | Kunden äger klustret.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Tjänstkonfigurationen är versionsnummer och distribuerade använda Azure distribuerar. Koden (program och runtime) skapas med Azure-versionen.
 |
