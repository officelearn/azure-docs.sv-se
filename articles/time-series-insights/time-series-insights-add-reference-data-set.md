---
title: "Hur du lägger till en referens datauppsättning Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här artikeln beskriver hur du lägger till en referens datauppsättning Azure tid serien Insights-miljö. Referensdata är användbar för att ansluta till datakällan till utöka värdena."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referens för datauppsättning för tid serien insikter miljön med hjälp av Azure portal

Den här artikeln beskriver hur du lägger till en referens datauppsättning Azure tid serien Insights-miljö. Referensdata är användbar för att ansluta till datakällan till utöka värdena.

En referensdatauppsättning är en samling objekt som är förstärkta med händelser från din händelsekälla. Bearbetningsmotorn för tidsserieinsikter kopplar en händelse från din händelsekälla till ett objekt i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på de nycklar som definierats i referensdatauppsättningen.

## <a name="add-a-reference-data-set"></a>Lägg till en referens för datauppsättning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga miljö tid serien insikter. Klicka på **alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

3. Under den **miljö topologi** rubrik, Välj **Referensdatamängder**.

    ![Skapa Time Series Insights-referensdatauppsättningen](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Välj **+ Lägg till** att lägga till en ny referens datauppsättning.

5. Ange ett unikt **referens datauppsättningsnamnet**.

    ![Skapa Time Series Insights-referensdatauppsättningen - information](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Ange den **nyckelnamn** i fältet tomt och välj dess **typen**. Detta namn och typ används för att välja rätt egenskap från händelser i dina händelsekällan för att ansluta till referensdata. 

   Om du anger nyckelnamn som till exempel **DeviceId** och som **sträng**, och sedan tiden serien insikter ingång hitta en egenskap med namnet **DeviceId** av typen **Sträng** i varje inkommande händelse att leta upp och ansluta med. Du kan ange mer än en nyckel att ansluta till händelsen. Matchningen av nyckelnamnet är skiftlägeskänsligt.

7. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg
* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.
* En fullständig API-referens, se dokumentet [Referensdata-API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) .
