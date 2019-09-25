---
title: Loggar och mått i Azure | Microsoft Docs
description: Översikt över diagnostikloggar i Azure som ger omfattande, frekventa data om driften av en Azure-resurs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262367"
---
# <a name="logs-and-metrics-in-azure"></a>Loggar och mått i Azure
Det finns olika [loggar](data-platform-logs.md) och [mått](data-platform-metrics.md)

Övervakning av program och tjänster i Azure kan delas upp i som lagras i [Azure Data Platform](data-platform.md). 

Loggar och mått kan delas upp i två kategorier

- Plattform – loggar och mät värden som genereras automatiskt utan någon konfiguration som krävs annat än bara 



| Lager | Plattforms loggar | Plattforms mått | Anpassade loggar | Anpassade mått |
|:---|:---|:---|:---|:---|
| Program  | | | | |
| Gästoperativsystem     | Pulsslag |  | Diagnostik-tillägg<br>Log Analytics agent | Diagnostik-tillägg |
| Resource     | [Resurs loggar](resource-logs-overview.md)<br>(gäller varje tjänst) | [Resurs mått](metrics-supported.md)<br>(gäller varje tjänst) | | [Anpassade mått](metrics-custom-overview.md) |
| Subscription | [Aktivitetslogg](activity-logs-overview.md) | | | |
| Klientorganisation       | 

## <a name="next-steps"></a>Nästa steg

* [Stream resursdiagnostikloggar till **Event Hubs**](resource-logs-stream-event-hubs.md)
* [Ändra resursdiagnostikinställningar med hjälp av Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)
* [Analysera loggar från Azure Storage med Azure Monitor](collect-azure-metrics-logs.md)
