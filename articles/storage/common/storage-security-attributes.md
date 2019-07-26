---
title: Säkerhetsattribut för Azure Storage
description: En check lista över säkerhetsattribut för utvärdering av Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 16ec2757955b53a8bfa73ba724100f7fa61d2867
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444307"
---
# <a name="security-attributes-for-azure-storage"></a>Säkerhetsattribut för Azure Storage

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Stöd för standard-HTTPS/TLS-mekanismer.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Se [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja |  |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Gäller inte |  |
| Stöd för nätverks isolering och brand vägg| Ja | |
| Stöd för Tvingad tunnel trafik| Gäller inte |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Azure Monitor tillgängliga mått nu, loggar startar för hands version |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory, delad nyckel, delad åtkomsttoken. |
| Authorization| Ja | Stöd för auktorisering via RBAC, POSIX ACL: er och SAS-token |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan | Ja | Azure Resource Manager aktivitets logg |
| Loggning och granskning av data planet| Ja | Loggar för tjänsten Diagnostic och Azure Monitor loggning starta för hands version  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöd för Resource Provider-versioner via Azure Resource Manager API: er |