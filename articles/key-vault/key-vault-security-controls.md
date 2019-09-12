---
title: Säkerhets kontroller för Azure Key Vault
description: En check lista över säkerhets kontroller för utvärdering av Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: d7f92a309a3d0b5d8e85f1e270d5590f46496a77
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886785"
---
# <a name="security-controls-for-azure-key-vault"></a>Säkerhets kontroller för Azure Key Vault

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Key Vault. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Använda tjänst slut punkter för Virtual Network (VNet). |
| Stöd för VNet-injektering| Nej |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använd brand Väggs regler för VNet. |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Log Analytics. |
| Kontroll/hantering plan-loggning och granskning| Ja | Använda Log Analytics. |
| Loggning och granskning av data planet| Ja | Använda Log Analytics. |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Använda Key Vault åtkomst princip. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Ja | Alla objekt är krypterade. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kunden styr alla nycklar i sina Key Vault. När HSM-säkerhetskopierade nycklar har angetts skyddar en FIPS-nivå 2 HSM nyckeln, certifikatet eller hemligheten. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | All kommunikation sker via krypterade API-anrop |
| Krypterade API-anrop| Ja | Använda HTTPS. |

## <a name="access-controls"></a>Åtkomstkontroller

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomst kontroller för kontroll/hanterings plan | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Åtkomst kontroller för data plan (på varje service nivå) | Ja | Key Vault åtkomst princip |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).