---
title: Få ett meddelande när ett måttvärde uppfyller ett villkor
description: En snabbstartsguide som hjälper användare att skapa ett mått för en logikapp
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: quickstart
ms.date: 02/08/2018
ms.author: ancav
ms.custom: mvc
ms.component: alerts
ms.openlocfilehash: 965a35eb4cf33abe78ca802e8d59f13a649dec0a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279236"
---
# <a name="receive-a-notification-when-a-metric-value-meets-a-condition"></a>Få ett meddelande när ett måttvärde uppfyller ett villkor

Azure Monitor tillhandahåller mått för många Azure-resurser. Dessa mått förmedlar prestanda och hälsa för dessa resurser. I flera fall kan måttvärdena peka på något som är fel med en resurs. Du kan skapa måttaviseringar för att övervaka om det förekommer onormala beteenden och i så fall få ett meddelande om detta. Den här snabbstarten visar hur du skapar en logikapp, skapar ett jobb och visar mått för logikappen. Därefter visas hur du skapar en avisering och får ett meddelande för ett mått för logikappresursen.

Mer information om mått och måttaviseringar finns i [Översikt över mått i Azure Monitor](../monitoring/monitoring-data-collection.md) och [Översikt över aviseringar i Azure Monitor](./monitoring-overview-alerts.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-logic-app"></a>Skapa en logikapp

1. Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Sök efter och välj **Logikapp**. Klicka på knappen **Skapa**.

3. Ange namnet myLogicApp och resursgruppen myResourceGroup. Använd din prenumeration.  Använd standardplatsen. Markera alternativet **Fäst på instrumentpanelen**.  Klicka på **Skapa** när du är klar. 

    ![Ange grundläggande information om logikappen i Portal](./media/monitor-quick-resource-metric-alert-portal/create-logic-app-portal.png)  


4. Logikappen bör fästas på instrumentpanelen. Navigera till logikappen genom att klicka på den.

5. Välj **Logikapp designer** på panelen Logikapp

     ![Skapa en upprepningsutlösare i designern på portalpanelen](./media/monitor-quick-resource-metric-alert-portal/logic-app-designer.png)  

6. Ställ in dina värden enligt följande diagram.

    ![Konfigurera logikappsutlösaren på portalpanelen](./media/monitor-quick-resource-metric-alert-portal/create-logic-app-triggers.png) 

7. Välj **upprepningsutlösaren** i designern.

8. Ange 20 som intervall och sekunder som frekvens så att logikappen utlöses var 20:e sekund.

9. Klicka på knappen **Nytt steg** och välj **Lägg till en åtgärd**.

10. Välj alternativet **HTTP** och välj **HTTP-HTTP**.

11. Ange POST som **metod** och **uri** till en webbadress som du väljer.

12. Klicka på **Spara**.

13. Det kan ta upp till 5 minuter för logikappen att köra de åtgärder som ska utföras.  

## <a name="view-metrics-for-your-logic-app"></a>Visa mått för din logikapp

1. Klicka på alternativet **Övervaka** i det vänstra navigeringsfönstret.

2. Välj fliken **Mått** och fyll i information för **Prenumeration**, **Resursgrupp**, **Resurstyp** och **Resurs** för logikappen.

3. Välj **Misslyckade körningar** i listan över mått.

4. Ändra **tidsintervallet** för diagrammet för att visa data för den senaste timmen.

5. Nu visas ett diagram med det totala antalet körningar som logikappen har startat den senaste timmen. Om du inte ser något kontrollerar du att du har väntat i minst 5 minuter från ovanstående steg. Uppdatera sedan webbläsaren. 

    ![Rita ett måttdiagram för logikappresursen](./media/monitor-quick-resource-metric-alert-portal/logic-app-metric-chart.png)

## <a name="create-a-metric-alert-for-your-logic-app"></a>Skapa en måttavisering för logikappen

1.  Klicka på knappen **Lägg till metrisk varning** på längst upp till höger på måttpanelen.

2. Ge måttaviseringen namnet myLogicAppAlert och ge en kort beskrivning av aviseringen.

3. Välj Större än som **villkor** för måttaviseringen, välj 10 som **tröskel** och välj Under de senaste 5 minuterna som **period**.

4. Ange slutligen din e-postadress under **Ytterligare e-postadresser för administratörer**. Den här aviseringen ser till att du får ett e-postmeddelande om din logikapp har mer än 10 misslyckade körningar inom en period på fem minuter.

    ![Konfigurera logikappsaviseringen på portalpanelen](./media/monitor-quick-resource-metric-alert-portal/logic-app-metrics-alert-portal.png)

## <a name="receive-metric-alert-notifications-for-your-logic-app"></a>Få måttaviseringar för logikappen
1. Inom kort får du ett e-postmeddelande från Microsoft Azure Alerts om att aviseringen har aktiverats.

2. Gå tillbaka till logikappen och ändra upprepningsutlösaren genom att ange 1 som intervall och timme som frekvens.

3. Inom ett par minuter får du ett e-postmeddelande från Microsoft Azure Alerts om att aviseringen har lösts.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du tänker fortsätta med efterföljande snabbstarter eller självstudier ska du inte rensa resurserna du har skapat i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1. Klicka på **Övervaka** på den vänstra menyn i Azure Portal.

2. Välj fliken **Aviseringar**, leta reda på aviseringen du skapade i snabbstartsguiden och klicka på den.

3. Klicka på **Ta bort** på panelen för måttaviseringen.

4. Sök efter **Logikapp** på den vänstra menyn i Azure Portal och klicka sedan på **Logikappar**.

5. I textrutan på panelen klickar du på den logikapp som du skapade i den här snabbstarten. Klicka sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att skapa en måttavisering för dina resurser. Klicka vidare till översikten om aviseringar om du vill ha mer information om måttaviseringar.

> [!div class="nextstepaction"]
> [Åtgärder för aviseringar för Azure Monitor-prenumeration](./monitor-quick-audit-notify-action-in-subscription.md )
