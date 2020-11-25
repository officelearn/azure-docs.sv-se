---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1e4ae81c85d8de22bc87f22538c85cdc4c15b79c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027925"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Mått varningar (klassisk) |100 aktiva varnings regler per prenumeration. | Ring support |
| Måttaviseringar |5 000 aktiva varnings regler per prenumeration i Azure offentlig, Azure Kina 21Vianet och Azure Government moln. Om du når den här gränsen kan du utforska om du kan använda [samma typ av aviseringar med flera resurser](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).   | Kontakta supporten. |
| Aktivitetsloggaviseringar | 100 aktiva varnings regler per prenumeration. | Samma som standard |
| Loggaviseringar | 512 aktiva varnings regler per prenumeration. 200 aktiva varnings regler per resurs. | Ring support |
| Åtgärdsgrupper |2 000 åtgärds grupper per prenumeration. | Ring support |
| Beskrivnings längd för varnings regler och åtgärds regler| Loggs ökning varningar 4096 tecken<br/>Alla andra 2048-tecken | Samma som standard |