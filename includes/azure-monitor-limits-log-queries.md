---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405665"
---
| Gräns | Beskrivning |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [frågespråk för Kusto](/azure/kusto/query/) som Azure datautforskaren. Se [Azure Monitor logg frågor språk skillnader](../articles/azure-monitor/log-query/data-explorer-difference.md) för KQL språk element som inte stöds i Azure Monitor. |
| Azure-regioner | Logg frågor kan uppleva onödig belastning när data sträcker sig Log Analytics arbets ytor i flera Azure-regioner. Mer information finns i [fråga om begränsningar](../articles/azure-monitor/log-query/scope.md#query-limits) . |
