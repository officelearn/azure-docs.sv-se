---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986738"
---
De här exemplen visar hur du använder Azure Monitor för att skapa aviseringar för prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Skickar frågor till den senaste 1 dagen av aktiviteten i en hanteringsklient och [rapporterar om eventuella tillagda eller borttagna delegeringar](../articles/lighthouse/how-to/monitor-delegation-changes.md) (eller försök som inte lyckades).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Den här mallen skapar en Azure-avisering och ansluter till en befintlig åtgärdsgrupp.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Skapar flera loggaviseringar baserat på Kusto-frågor.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Distribuerar en avisering i en klientorganisation när en användare delegerar en prenumeration till en hanteringsklient.|
