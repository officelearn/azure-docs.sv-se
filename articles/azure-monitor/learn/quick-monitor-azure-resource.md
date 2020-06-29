---
title: Övervaka en Azure-resurs med Azure Monitor
description: Lär dig hur du samlar in och analyserar data för en Azure-resurs i Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 597b12a584e7b4c17ec21635e18368f897ad168a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85443884"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Snabb start: övervaka en Azure-resurs med Azure Monitor
[Azure Monitor](../overview.md) börjar samla in data från Azure-resurser när de har skapats. Den här snabb starten innehåller en kort genom gång av de data som samlas in automatiskt för en resurs och hur du kan visa den i Azure Portal för en viss resurs. Senare kan du lägga till en konfiguration för att samla in ytterligare data och kan gå till Azure Monitor menyn för att använda samma verktyg för att få åtkomst till data som samlats in för alla resurser i din prenumeration.

Mer detaljerad information om övervaknings data som samlas in från Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Översikts sida
Många tjänster omfattar övervaknings data på sina **översikts** sidor så att du snabbt kan se hur de fungerar. Detta är vanligt vis baserat på en delmängd av plattforms måtten som lagras i Azure Monitor Mät värden.

1. Leta upp en Azure-resurs i din prenumeration.
2. Gå till sidan **Översikt** och notera om det finns några prestanda data som visas. Dessa data kommer att tillhandahållas av Azure Monitor. Exemplet nedan är **översikts** sidan för ett Azure Storage-konto och du kan se att flera mått visas.

    ![Översikts sida](media/quick-monitor-azure-resource/overview.png)

3. Du kan klicka på något av diagrammen för att öppna data i mått Utforskaren som beskrivs nedan.

## <a name="view-the-activity-log"></a>Visa aktivitets loggen
Aktivitets loggen ger inblick i åtgärderna för varje Azure-resurs i prenumerationen. Detta inkluderar sådan information som när en resurs skapas eller ändras när ett jobb startas eller när en viss åtgärd utförs.

1. Överst på menyn för din resurs väljer du **aktivitets logg**.
2. Det aktuella filtret anges till händelser som är relaterade till din resurs. Om du inte ser några händelser kan du försöka ändra **TimeSpan** för att öka tidsintervallet.

    ![Aktivitetslogg](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Om du vill se händelser från andra resurser i din prenumeration kan du antingen ändra villkoren i filtret eller till och med ta bort filter egenskaper.

    ![Aktivitetslogg](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Visa mått
Mått är numeriska värden som beskriver viss aspekt av din resurs vid en viss tidpunkt. Azure Monitor samlar automatiskt in plattforms mått med ett minuters intervall från alla Azure-resurser. Du kan visa dessa mått med hjälp av Metrics Explorer.

1. Under avsnittet **övervakning** på resurs menyn väljer du **mått**. Mät Utforskaren öppnas med den omfattning som angetts för din resurs.
2. Klicka på **Lägg till mått** om du vill lägga till ett mått i diagrammet.
   
   ![Måttutforskare](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Välj ett **mått** i list rutan och sedan en **agg regering**. Detta definierar hur insamlade värden kommer att samplas över varje tidsintervall.

    ![Måttutforskare](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klicka på **Lägg till mått** om du vill lägga till ytterligare mått-och agg regerings kombinationer i diagrammet.

    ![Måttutforskare](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Nästa steg
I den här snabb starten visade du aktivitets loggen och måtten för en Azure-resurs som samlas in automatiskt av Azure Monitor. Fortsätt till nästa snabb start som visar hur du samlar in aktivitets loggen på en Log Analytics arbets yta där de kan analyseras med hjälp av [logg frågor](../log-query/log-query-overview.md).

> [!div class="nextstepaction"]
> [Skicka Azure aktivitets logg till Log Analytics arbets yta](quick-monitor-azure-resource.md)
