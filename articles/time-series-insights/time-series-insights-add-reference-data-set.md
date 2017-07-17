---
title: "Lägga till en referensdatauppsättning i miljön för Azure Time Series Insights | Microsoft Docs"
description: "I den här självstudien ansluter du en referensdatauppsättning till Time Series Insights-miljön"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 23444297b5231e6a026bcd9ce3ee9f943bf05867
ms.contentlocale: sv-se
ms.lasthandoff: 07/04/2017

---

# Skapa en referensdatauppsättning för miljön för Time Series Insights med Ibiza Portal
<a id="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal" class="xliff"></a>

En referensdatauppsättning är en samling objekt som är förstärkta med händelser från din händelsekälla. Bearbetningsmotorn för tidsserieinsikter kopplar en händelse från din händelsekälla till ett objekt i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på de nycklar som definierats i referensdatauppsättningen.

## Steg för att lägga till en referensdatauppsättning i din miljö
<a id="steps-to-add-a-reference-data-set-to-your-environment" class="xliff"></a>

1. Logga in på [Ibiza portal](https://portal.azure.com).
2. Klicka på ”Alla resurser” på menyn på vänster sida av Ibiza Portal.
3. Välj Time Series Insights-miljö.

    ![Skapa Time Series Insights-referensdatauppsättningen](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Välj ”referensdatauppsättningar”, klicka på ”+ Lägg till”.

    ![Skapa Time Series Insights-referensdatauppsättningen - information](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Ange namnet på referensdatauppsättningen.
6. Ange namnet och dess typ. Detta namn och typ används för att välja rätt egenskap från händelsen i din händelsekälla. Till exempel, om du tillhandahåller nyckelnamn som ”DeviceId” och typ som ”String” söker motorn för tidserieinsikter sedan en egenskap med namnet ”DeviceId” av typen ”String” i den inkommande händelsen. Du kan ange mer än en nyckel att ansluta till händelsen. Matchningen med egenskapsnamn är skiftlägeskänslig.

     ![Skapa Time Series Insights-referensdatauppsättningen - information](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Klicka på ”Skapa”.

## Nästa steg
<a id="next-steps" class="xliff"></a>

* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.
* En fullständig API-referens, se dokumentet [Referensdata-API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) .
