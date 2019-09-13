---
title: Säkerhets kontroller för Azure Resource Manager
description: En check lista över säkerhets kontroller för utvärdering av Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97fd6611d7e2a2787b865365c4c7579f89f17d01
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886716"
---
# <a name="security-controls-for-azure-resource-manager"></a>Säkerhets kontroller för Azure Resource Manager

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | HTTPS/TLS. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Gäller inte | Azure Resource Manager lagrar inget kund innehåll, endast kontroll data. |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | |
| Krypterade API-anrop| Ja | |

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Nej | |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Aktivitets loggar visar alla Skriv åtgärder (placering, POST, DELETE) som utförs på resurserna. Se [Visa aktivitets loggar för att granska åtgärder på resurser](resource-group-audit.md). |
| Loggning och granskning av data planet| Gäller inte | |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | [Azure Active Directory](/azure/active-directory) baserad.|
| Authorization| Ja | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).