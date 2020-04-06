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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421142"
---
De här exemplen visar hur du använder Azure Monitor för att skapa aviseringar för prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Skickar frågor till den senaste 1 dagen av aktiviteten i en hanteringsklient och [rapporterar om eventuella tillagda eller borttagna delegeringar](../articles/lighthouse/how-to/monitor-delegation-changes.md) (eller försök som inte lyckades).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Den här mallen skapar en Azure-avisering och ansluter till en befintlig åtgärdsgrupp.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Skapar flera loggaviseringar baserat på Kusto-frågor.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Distribuerar en avisering i en klientorganisation när en användare delegerar en prenumeration till en hanteringsklient.|
