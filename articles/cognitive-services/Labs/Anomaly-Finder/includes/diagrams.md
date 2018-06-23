---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353301"
---
De data som returneras med de förväntade värdet och standard övre och nedre marginalerna. I praktiken kan du definiera en [känslighet]-parameter och sedan använda (ExpectedValue + känslighet * UpperMargin) som den övre gränsen och (ExpectedValue - känslighet * LowerMargin) som den nedre gränsen för att justera avvikelseidentifiering pekar genom yourselves. Värdet för [känslighet] ska vara större än 1. Nedan visas några diagram för justering.

> [!NOTE]
> Diagrammen genereras inte av exempelprogrammet. De skapas som ett fristående verktyg med det här exempelprogrammet.

![Tunning: känslighet = 1.0](../media/sensitivity_1.png)
![Tunning: känslighet = 1.5](../media/sensitivity_1.5.png)
![Tunning: känslighet = 2](../media/sensitivity_2.png)
![Tunning: känslighet = 3.5](../media/sensitivity_3.5.png)