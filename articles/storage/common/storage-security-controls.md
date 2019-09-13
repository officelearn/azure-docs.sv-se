---
title: Säkerhets kontroller för Azure Storage
description: En check lista över säkerhets kontroller för utvärdering av Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886560"
---
# <a name="security-controls-for-azure-storage"></a>Säkerhets kontroller för Azure Storage

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Ja |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Se [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Stöd för standard-HTTPS/TLS-mekanismer.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Krypterade API-anrop| Ja |  |

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Stöd för VNet-injektering| Gäller inte |  |
| Stöd för nätverks isolering och brand vägg| Ja | |
| Stöd för Tvingad tunnel trafik| Gäller inte |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Azure Monitor tillgängliga mått nu, loggar startar för hands version |
| Loggning och granskning av kontroll-och hanterings plan | Ja | Azure Resource Manager aktivitets logg |
| Loggning och granskning av data planet| Ja | Loggar för tjänsten Diagnostic och Azure Monitor loggning starta för hands version  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Azure Active Directory, delad nyckel, delad åtkomsttoken. |
| Authorization| Ja | Stöd för auktorisering via RBAC, POSIX ACL: er och SAS-token |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöd för Resource Provider-versioner via Azure Resource Manager API: er |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).