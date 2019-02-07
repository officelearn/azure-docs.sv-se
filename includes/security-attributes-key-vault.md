---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: aba09012bf2e9d2741f598280add8b599a6f6d1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807228"
---
## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Alla objekt som är krypterade. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | All kommunikation är via krypterade API-anrop |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När modul (HSM) som backas upp av maskinvara säkerhetsnycklar är specifiecd, skyddar en FIPS nivå 2 HSM nyckeln, certifikat eller hemligheten. |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja | Med tjänstslutpunkter i virtuella nätverk (Vnet). |
| vNET-stöd för inmatning| Nej |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Med hjälp av brandväggsregler för virtuellt nätverk. |
| Stöd för Tvingad tunneltrafik | Nej |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv)| Ja | Använda Log Analytics. |

## <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Med hjälp av Nyckelvalvets åtkomstprincip. |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplaner loggning och granskning| Ja | Använda Log Analytics. |
| Data-dataplaner loggning och granskning| Ja | Använda Log Analytics. |

## <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplanets åtkomstkontroller | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Data dataplanets åtkomstkontroller (på varje servicenivå) | Ja | Nyckelvalvets åtkomstprincip |
