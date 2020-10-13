---
title: Säkerhetskontroller
titleSuffix: Azure Storage
description: Visa check listor för säkerhets kontroll för utvärdering av Azure Storage. Områden som omfattas är data skydd, nätverk, övervakning och loggning, identitet och konfiguration.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715718"
---
# <a name="security-controls-for-azure-storage"></a>Säkerhets kontroller för Azure Storage

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Se [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kryptering på kolumn nivå (Azure Data Services)| E.t. |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet kryptering)| Ja | Stöd för standard-HTTPS/TLS-mekanismer.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Krypterade API-anrop| Ja |  |

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Obs! |
|---|---|--|
| Stöd för tjänst slut punkt| Ja |  |
| Support för service märken| Ja | Se [Översikt över Azure Service Tags](../../virtual-network/service-tags-overview.md) för mer information om service märken som stöds av Azure Storage. |
| Stöd för VNet-injektering| E.t. |  |
| Nätverks isolering och brand Väggs stöd| Ja | |
| Stöd för Tvingad tunnel trafik| E.t. |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Azure Monitor mått|
| Loggning och granskning av kontroll-och hanterings plan | Ja | Azure-aktivitetslogg |
| Loggning och granskning av data planet| Ja | Azure Monitor resurs loggar |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Autentisering| Ja | Azure Active Directory, delad nyckel, delad åtkomsttoken. |
| Auktorisering| Ja | Stöd för auktorisering via Azure RBAC, POSIX ACL: er och SAS-token |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Obs!|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöd för Resource Provider-versioner via Azure Resource Manager API: er |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../../security/fundamentals/security-controls.md).