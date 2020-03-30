---
title: Säkerhetskontroller för Azure API Management
description: En checklista över säkerhetskontroller för utvärdering av API Management
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751136"
---
# <a name="security-controls-for-api-management"></a>Säkerhetskontroller för API-hantering

Den här artikeln dokumenterar de säkerhetskontroller som är inbyggda i API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Support för tjänstens slutpunkt| Inga | |  |
| Stöd för VNet-injektion| Ja | |  |
| Stöd för nätverksisolering och brandväggar| Ja | Använda nsg (networking) respektive Azure Application Gateway (eller annan programvaruinstallation). |  |
| Stöd för påtvingad tunnelning| Ja | Azure-nätverk ger påtvingad tunnel. |  |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | | |
| Kontroll- och hanteringsplan loggning och revision| Ja | [Aktivitetsloggar för Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Loggning och granskning av dataplan| Ja | [Diagnostikloggar för Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) och (eventuellt) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | |  |
| Auktorisering| Ja | |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Känsliga data som certifikat, nycklar och värden med hemligt namn krypteras med tjänsthanterade, per tjänstinstansnycklar. |  |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Inga | Alla krypteringsnycklar är per tjänstinstans och hanteras. |  |
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | [Express Route](../expressroute/index.yml) och VNet-kryptering tillhandahålls av [Azure-nätverk](../virtual-network/index.yml). |  |
| API-anrop krypterade| Ja | Hanteringsplananrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. En giltig JSON-webbtoken (JWT) krävs.  Dataplansamtal kan säkras med TLS och en av autentiseringsmekanismer som stöds (till exempel klientcertifikat eller JWT). |   |
 |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja | Använda [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Sårbarhet skannar falska positiva

Det här avsnittet dokumenterar vanliga säkerhetsproblem som inte påverkar Azure API Management.

| Säkerhetsrisk               | Beskrivning                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biljettblet (CVE-2016-9244) | Ticketbleed är säkerhetsproblem i implementeringen av TLS SessionTicket-tillägget som finns i vissa F5-produkter. Det möjliggör läckage ("blödning") på upp till 31 byte data från oinitierat minne. Detta orsakas av TLS stack utfyllnad ett sessions-ID, skickas från klienten, med data för att göra det 32 bitar lång. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).