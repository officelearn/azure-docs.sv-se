---
title: Säkerhetsattribut för Azure Key Vault
description: En check lista över säkerhetsattribut för utvärdering av Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444007"
---
# <a name="security-attributes-for-azure-key-vault"></a>Säkerhetsattribut för Azure Key Vault

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja | Alla objekt är krypterade. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | All kommunikation sker via krypterade API-anrop |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När HSM-säkerhetskopierade nycklar har angetts skyddar en FIPS-nivå 2 HSM nyckeln, certifikatet eller hemligheten. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte |  |
| Krypterade API-anrop| Ja | Använda HTTPS. |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | Använda tjänst slut punkter för Virtual Network (VNet). |
| Stöd för VNet-injektering| Nej |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använd brand Väggs regler för VNet. |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Använda Log Analytics. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Använda Key Vault åtkomst princip. |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll/hantering plan-loggning och granskning| Ja | Använda Log Analytics. |
| Loggning och granskning av data planet| Ja | Använda Log Analytics. |

## <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomst kontroller för kontroll/hanterings plan | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Åtkomst kontroller för data plan (på varje service nivå) | Ja | Key Vault åtkomst princip |