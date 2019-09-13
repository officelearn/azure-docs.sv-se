---
title: Säkerhets kontroller för Azure Service Bus relä
description: En check lista över säkerhets kontroller för att utvärdera Azure Service Bus relä
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a131313f6c270debd9509a934fbf3ce74918ed42
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886375"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Säkerhets kontroller för Azure Service Bus relä

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Service Bus relä.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej |  |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Gäller inte | Relä är TLS-tunneln  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Loggning och granskning av data planet| Ja | Lyckad/misslyckad anslutning och fel och loggad.  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Via SAS. |
| Authorization|  Ja | Via SAS. |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar |  Gäller inte | Relay är en webbsocket och bevarar inte data. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Nej | Använder endast Microsoft TLS-certifikat.  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Tjänsten kräver TLS. |
| Krypterade API-anrop| Ja | HTTPS. |


## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).