---
title: Säkerhetskontroller för Azure Storage
description: En checklista över säkerhetskontroller för utvärdering av Azure Storage
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061120"
---
# <a name="security-controls-for-azure-storage"></a>Säkerhetskontroller för Azure Storage

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja |  |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Se [Kryptering av lagringstjänster med kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Stöd för vanliga HTTPS/TLS-mekanismer.  Användare kan också kryptera data innan de överförs till tjänsten. |
| API-anrop krypterade| Ja |  |

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ja |  |
| Stöd för VNet-injektion| Ej tillämpligt |  |
| Stöd för nätverksisolering och brandväggar| Ja | |
| Stöd för påtvingad tunnelning| Ej tillämpligt |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Azure-övervakarmått|
| Kontroll- och hanteringsplan loggning och revision | Ja | Aktivitetslogg för Azure Resource Manager |
| Loggning och granskning av dataplan| Ja | Diagnostikloggar för tjänsten.|

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory, Delad nyckel, Delad åtkomsttoken. |
| Auktorisering| Ja | Supportauktorisering via RBAC-, POSIX-ACL:er och SAS-token |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | Stöd resursproviderversionering via Azure Resource Manager API:er |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../../security/fundamentals/security-controls.md).