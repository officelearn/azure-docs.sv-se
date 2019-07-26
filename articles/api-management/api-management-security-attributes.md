---
title: Säkerhetsattribut för Azure API Management
description: En check lista över säkerhetsattribut för utvärdering av API Management
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442285"
---
# <a name="security-attributes-for-api-management"></a>Säkerhetsattribut för API Management

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i API Management.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja (endast kryptering på tjänst sidan) | Känsliga data, till exempel certifikat, nycklar och hemliga värden, krypteras med hanterade tjänst instanser, per tjänst instans nycklar. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | [Express Route](../expressroute/index.yml) och VNET-kryptering tillhandahålls av [Azure-nätverk](../virtual-network/index.yml). |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej | Alla krypterings nycklar är per tjänst instans och hanteras av tjänsten. |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Hanterings Plans anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. En giltig JSON Web token (JWT) krävs.  Data Plans anrop kan skyddas med TLS och en av de autentiseringsmekanismer som stöds (till exempel klient certifikat eller JWT).
 |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Ja | Använda nätverks säkerhets grupper (NSG) och Azure Application Gateway (eller annan program varu installation). |
| Stöd för Tvingad tunnel trafik| Ja | Azure-nätverk tillhandahåller Tvingad tunnel trafik. |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | |
| Authorization| Ja | |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | [Azure Monitor aktivitets loggar](../azure-monitor/platform/activity-logs-overview.md) |
| Loggning och granskning av data planet| Ja | [Azure Monitor diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) och (valfritt) [Azure Application](../azure-monitor/app/app-insights-overview.md)insikter.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Använda [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Sårbarhet söker efter falska positiva identifieringar

I det här avsnittet dokumenteras vanliga sårbarheter, som inte påverkar Azure-API Management.

| Sårbarhet               | Beskrivning                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed är ett säkerhets problem i implementeringen av TLS SessionTicket-tillägget som finns i vissa F5-produkter. Det tillåter läckage ("avblödning") på upp till 31 byte data från ej initierat minne. Detta orsakas av att utfyllnaden av TLS-stacken är ett sessions-ID som skickas från klienten, med data som gör den 32 bitar lång. |
