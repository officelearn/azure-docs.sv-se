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
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680697"
---
# <a name="common-security-attributes-for-azure-storage"></a>Vanliga säkerhetsattribut för Azure Storage

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöd för standardmetoder HTTPS/TLS.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Se [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja |  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja |  |
| vNET-stöd för inmatning| Gäller inte |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | |
| Stöd för Tvingad tunneltrafik | Gäller inte |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Azure Monitor Metrics som är tillgängliga nu loggar från förhandsversion |

## <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Azure Active Directory delad nyckel, delad åtkomst-token. |
| Åtkomsthantering - auktorisering| Ja | Stöd för auktorisering via RBAC, POSIX-ACL: er och SAS-token |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning | Ja | Azure Resource Manager-aktivitetslogg |
| Data-dataplaner loggning och granskning| Ja | Diagnostikloggar för tjänsten och Azure Monitor-loggning från förhandsversion  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Stöd för versionshantering Resource Provider via Azure Resource Manager API: er |