---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888704"
---
De data som returneras med det förväntade värdet och standardmässiga övre och nedre marginaler. I praktiken kan du definiera en [sensitivity]-parameter (känslighet) och sedan använda (ExpectedValue + sensitivity * UpperMargin) som övre gräns och (ExpectedValue – sensitivity * LowerMargin) som nedre gräns för att justera avvikelsepunkterna på egen hand. Värdet för [sensitivity] måste vara större än 1. Nedan visas några diagram för justering.

> [!NOTE]
> Diagrammen genereras inte av exempelprogrammet. De skapas med ett separat verktyg med exempelprogrammet.

![Justering: sensitivity = 1,0](../media/sensitivity_1.png)
![Justering: sensitivity = 1,5](../media/sensitivity_1.5.png)
![Justering: sensitivity = 2](../media/sensitivity_2.png)
![Justering: sensitivity = 3,5](../media/sensitivity_3.5.png)