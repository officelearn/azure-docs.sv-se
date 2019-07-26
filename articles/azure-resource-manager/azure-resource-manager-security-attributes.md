---
title: Säkerhetsattribut för Azure Resource Manager
description: En check lista över säkerhetsattribut för utvärdering av Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444153"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Säkerhetsattribut för Azure Resource Manager

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | HTTPS/TLS. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Gäller inte | Azure Resource Manager lagrar inget kund innehåll, endast kontroll data. |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | |
| Krypterade API-anrop| Ja | |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Nej | |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](/azure/active-directory) baserad.|
| Authorization| Ja | |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Aktivitets loggar visar alla Skriv åtgärder (placering, POST, DELETE) som utförs på resurserna. Se [Visa aktivitets loggar för att granska åtgärder på resurser](resource-group-audit.md). |
| Loggning och granskning av data planet| Gäller inte | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  |
