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
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796224"
---
| Gräns | Beskrivning |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [frågespråk för Kusto](/azure/kusto/query/) som Azure datautforskaren. Se [Azure Monitor logg frågor språk skillnader](../articles/azure-monitor/log-query/data-explorer-difference.md) för KQL språk element som inte stöds i Azure Monitor. |
| Azure-regioner | Logg frågor kan uppleva onödig belastning när data sträcker sig Log Analytics arbets ytor i flera Azure-regioner. Mer information finns i [fråga om begränsningar](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Kors resurs frågor | Maximalt antal Application Insights-resurser och Log Analytics arbets ytor i en enda fråga som är begränsade till 100.<br>Frågan över resurser stöds inte i View Designer.<br>Frågan över resurser i logg aviseringar stöds i det nya scheduledQueryRules-API: et.<br>Se [gränser för kors resurs frågor](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) för mer information. |
| Fråga begränsning | En användare är begränsad till 200 frågor per 30 sekunder på valfritt antal arbets ytor. Den här gränsen gäller för programmatiska frågor eller frågor som initieras av visualiserings delar som Azure-instrumentpaneler och sammanfattnings sidan Log Analytics-arbetsyta. |
