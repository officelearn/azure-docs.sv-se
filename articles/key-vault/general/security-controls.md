---
title: Säkerhetskontroller för Azure Key Vault
description: En checklista med säkerhetskontroller för utvärdering av Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429868"
---
# <a name="security-controls-for-azure-key-vault"></a>Säkerhetskontroller för Azure Key Vault

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ja | Använda VNet-tjänstslutpunkter (Virtual Network). |
| Stöd för VNet-injektion| Inga |  |
| Stöd för nätverksisolering och brandväggar| Ja | Använda VNet-brandväggsregler. |
| Stöd för påtvingad tunnelning| Inga |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Använda Logganalys. |
| Loggning och granskning av kontroll/hanteringsplan| Ja | Använda Logganalys. |
| Loggning och granskning av dataplan| Ja | Använda Logganalys. |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering sker via Azure Active Directory. |
| Auktorisering| Ja | Använda åtkomstprincipen för Nyckelvalv. |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Alla objekt är krypterade. |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Kunden styr alla nycklar i key vault. När HSM-stödda nycklar (Hardware Security Module) har angetts skyddar en FIPS-nivå 2 HSM nyckeln, certifikatet eller hemligheten. |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | All kommunikation sker via krypterade API-anrop |
| API-anrop krypterade| Ja | Använda HTTPS. |

## <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomstkontroller för styr-/hanteringsplan | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Åtkomstkontroller för dataplan (på alla servicenivåer) | Ja | Åtkomstprincip för nyckelvalv |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../../security/fundamentals/security-controls.md).