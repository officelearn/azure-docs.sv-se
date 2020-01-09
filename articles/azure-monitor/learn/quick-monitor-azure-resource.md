---
title: Samla in data från en virtuell Azure-dator med Azure Monitor | Microsoft Docs
description: Lär dig hur du aktiverar Log Analytics-agentens tillägg för virtuella datorer och hur du aktiverar insamling av data från virtuella datorer i Azure med Log Analytics.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 85c953c4acdc31cc6d79600951ba745346771b0c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533974"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Snabb start: övervaka en Azure-resurs med Azure Monitor
[Azure Monitor](../overview.md) börjar samla in data från Azure-resurser när de har skapats. Den här snabb starten innehåller en kort genom gång av de data som samlas in automatiskt för en resurs och hur du kan visa den i Azure Portal för en viss resurs. Senare kan du lägga till en konfiguration för att samla in ytterligare data och kan gå till Azure Monitor menyn för att använda samma verktyg för att få åtkomst till data som samlats in för alla resurser i din prenumeration.

Mer detaljerad information om övervaknings data som samlas in från Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Logga in på Azure Portal

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
   
   ![Mått Utforskaren](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Välj ett **mått** i list rutan och sedan en **agg regering**. Detta definierar hur insamlade värden kommer att samplas över varje tidsintervall.

    ![Mått Utforskaren](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klicka på **Lägg till mått** om du vill lägga till ytterligare mått-och agg regerings kombinationer i diagrammet.

    ![Mått Utforskaren](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Nästa steg
I den här snabb starten visade du aktivitets loggen och måtten för en Azure-resurs som samlas in automatiskt av Azure Monitor. Resurs loggar ger inblick i den detaljerade åtgärden för resursen men måste konfigureras för att kunna samlas in. Fortsätt till självstudien för att samla in resurs loggar till en Log Analytics arbets yta där de kan analyseras med hjälp av logg frågor.

> [!div class="nextstepaction"]
> [Samla in och analysera resurs loggar med Azure Monitor](tutorial-resource-logs.md)
