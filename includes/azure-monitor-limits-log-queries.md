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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796224"
---
| Gräns | Beskrivning |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [Kusto-frågespråk](/azure/kusto/query/) som Azure Data Explorer. Se [Språkskillnader för Azure Monitor-loggfrågor](../articles/azure-monitor/log-query/data-explorer-difference.md) för KQL-språkelement som inte stöds i Azure Monitor. |
| Azure-regioner | Loggfrågor kan uppleva överdrivna omkostnader när data sträcker sig över Log Analytics-arbetsytor i flera Azure-regioner. Mer information [finns i Frågegränser.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Frågor om flera resurser | Maximalt antal Application Insights-resurser och Log Analytics-arbetsytor i en enda fråga som är begränsad till 100.<br>Frågan över flera resurser stöds inte i View Designer.<br>Fråga över flera resurser i loggaviseringar stöds i det nya schemalagdaQueryRules API:et.<br>Mer information finns i frågegränser för [flera resurser.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |
| Fråga begränsning | En användare är begränsad till 200 frågor per 30 sekunder på valfritt antal arbetsytor. Den här gränsen gäller för programmatiska frågor eller frågor som initierats av visualiseringsdelar som Azure-instrumentpaneler och sammanfattningssidan för Logganalysarbetsyta. |
