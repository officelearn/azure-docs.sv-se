---
title: Säkerhets kontroller för Azure API Management
description: En check lista över säkerhets kontroller för utvärdering av API Management
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751136"
---
# <a name="security-controls-for-api-management"></a>Säkerhets kontroller för API Management

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Inga | |  |
| Stöd för VNet-injektering| Ja | |  |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG) och Azure Application Gateway (eller annan program varu installation). |  |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | | |
| Loggning och granskning av kontroll-och hanterings plan| Ja | [Azure Monitor aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md) | |
| Loggning och granskning av data planet| Ja | [Azure Monitor diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md) och (valfritt) [Azure Application insikter](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | |  |
| Autentisering| Ja | |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Känsliga data, till exempel certifikat, nycklar och hemliga värden, krypteras med hanterade tjänst instanser, per tjänst instans nycklar. |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Inga | Alla krypterings nycklar är per tjänst instans och hanteras av tjänsten. |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | [Express Route](../expressroute/index.yml) och VNET-kryptering tillhandahålls av [Azure-nätverk](../virtual-network/index.yml). |  |
| Krypterade API-anrop| Ja | Hanterings Plans anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. En giltig JSON Web token (JWT) krävs.  Data Plans anrop kan skyddas med TLS och en av de autentiseringsmekanismer som stöds (till exempel klient certifikat eller JWT). |   |
 |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Använda [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Sårbarhet söker efter falska positiva identifieringar

I det här avsnittet dokumenteras vanliga sårbarheter som inte påverkar Azure API Management.

| Säkerhetsrisk               | Beskrivning                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed är ett säkerhets problem i implementeringen av TLS SessionTicket-tillägget som finns i vissa F5-produkter. Det tillåter läckage ("avblödning") på upp till 31 byte data från ej initierat minne. Detta orsakas av att utfyllnaden av TLS-stacken är ett sessions-ID som skickas från klienten, med data som gör den 32 bitar lång. |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).