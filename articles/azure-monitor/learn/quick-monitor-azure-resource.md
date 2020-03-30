---
title: Övervaka en Azure-resurs med Azure Monitor
description: Lär dig hur du samlar in och analyserar data för en Azure-resurs i Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661872"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Snabbstart: Övervaka en Azure-resurs med Azure Monitor
[Azure Monitor](../overview.md) börjar samla in data från Azure-resurser så fort de skapas. Den här snabbstarten ger en kort genomgång av de data som samlas in automatiskt för en resurs och hur du visar den i Azure-portalen för en viss resurs. Senare kan du lägga till konfiguration för att samla in ytterligare data och gå till Azure Monitor-menyn för att använda samma verktyg för att komma åt data som samlats in för alla resurser i din prenumeration.

Mer detaljerade beskrivningar av övervakningsdata som samlats in från Azure-resurser finns [i Övervaka Azure-resurser med Azure Monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Översiktssida
Många tjänster kommer att omfatta övervakning av data på deras **översiktssida** som en snabb titt på deras funktion. Detta baseras vanligtvis på en delmängd av plattformsmått som lagras i Azure Monitor Metrics.

1. Hitta en Azure-resurs i din prenumeration.
2. Gå till sidan **Översikt** och notera om det visas några prestandadata. Dessa data kommer att tillhandahållas av Azure Monitor. Exemplet nedan är **översiktssidan** för ett Azure-lagringskonto och du kan se att det finns flera mått som visas.

    ![Översiktssida](media/quick-monitor-azure-resource/overview.png)

3. Du kan klicka på någon av diagrammen för att öppna data i mått explorer som beskrivs nedan.

## <a name="view-the-activity-log"></a>Visa aktivitetsloggen
Aktivitetsloggen ger insikt i åtgärderna på varje Azure-resurs i prenumerationen. Detta inkluderar sådan information som när en resurs skapas eller ändras, när ett jobb startas eller när en viss operation inträffar.

1. Högst upp på menyn för din resurs väljer du **Aktivitetslogg**.
2. Det aktuella filtret är inställt på händelser som är relaterade till din resurs. Om du inte ser några händelser kan du prova att ändra **Tidsspan för** att öka tidsomfånget.

    ![Aktivitetslogg](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Om du vill se händelser från andra resurser i prenumerationen ändrar du antingen villkoren i filtret eller till och med tar bort filteregenskaper.

    ![Aktivitetslogg](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Visa mått
Mått är numeriska värden som beskriver någon aspekt av din resurs vid en viss tidpunkt. Azure Monitor samlar automatiskt in plattformsmått med en minuts intervall från alla Azure-resurser. Du kan visa dessa mått med hjälp av statistik explorer.

1. Välj **Mått**under avsnittet **Övervakning** på resursens meny . Detta öppnar statistikutforskaren med scopet inställt på din resurs.
2. Klicka på **Lägg till mått om** du vill lägga till ett mått i diagrammet.
   
   ![Statistikutforskare](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Välj ett **mått** i listrutan och sedan en **aggregering**. Detta definierar hur de insamlade värdena ska provtas under varje tidsintervall.

    ![Statistikutforskare](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klicka på **Lägg till mått** om du vill lägga till ytterligare mått- och aggregeringskombinationer i diagrammet.

    ![Statistikutforskare](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Nästa steg
I den här snabbstarten visade du aktivitetsloggen och måtten för en Azure-resurs som automatiskt samlas in av Azure Monitor. Resursloggar ger insikt i den detaljerade åtgärden av resursen men måste konfigureras för att samlas in. Fortsätt till självstudien för att samla in resursloggar till en Log Analytics-arbetsyta där de kan analyseras med hjälp av loggfrågor.

> [!div class="nextstepaction"]
> [Samla in och analysera resursloggar med Azure Monitor](tutorial-resource-logs.md)
