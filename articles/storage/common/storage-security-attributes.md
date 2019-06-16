---
title: Vanliga säkerhetsattribut för Azure Storage
description: En lista med vanliga security attribut för att utvärdera Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001465"
---
# <a name="security-attributes-for-azure-storage"></a>Security attribut för Azure Storage

Den här artikeln dokumenteras security-attribut som är inbyggda i Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöd för standardmetoder HTTPS/TLS.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Se [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja |  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| VNet-stöd för inmatning| Gäller inte |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | |
| Tvingad tunneltrafik support| Gäller inte |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Azure Monitor Metrics som är tillgängliga nu loggar från förhandsversion |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory delad nyckel, delad åtkomst-token. |
| Auktorisering| Ja | Stöd för auktorisering via RBAC, POSIX-ACL: er och SAS-token |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning | Ja | Azure Resource Manager-aktivitetslogg |
| Data plan loggning och granskning| Ja | Diagnostikloggar för tjänsten och Azure Monitor-loggning från förhandsversion  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Stöd för versionshantering Resource Provider via Azure Resource Manager API: er |