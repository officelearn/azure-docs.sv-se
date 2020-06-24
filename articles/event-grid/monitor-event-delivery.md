---
title: Visa Azure Event Grid mått och ange aviseringar
description: Den här artikeln beskriver hur du använder Azure Portal för att visa mått för Azure Event Grid ämnen och prenumerationer och skapa aviseringar på dem.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100597"
---
# <a name="monitor-event-grid-message-delivery"></a>Övervaka Event Grid meddelande leverans 
Den här artikeln beskriver hur du använder portalen för att se mått för Event Grid ämnen och prenumerationer och hur du skapar aviseringar. 

## <a name="metrics"></a>Mått

Portalen visar mått för statusen för att leverera händelse meddelanden.

Här följer några av måtten för ämnen:

* **Publiceringen lyckades**: händelsen har skickats till ämnet och bearbetats med ett 2xx-svar.
* **Publiceringen misslyckades**: händelsen skickades till ämnet men avvisades med en felkod.
* **Omatchad**: händelsen har publicerats till ämnet, men inte matchad med en händelse prenumeration. Händelsen släpptes.

Här är några av måtten för prenumerationer:

* **Leveransen lyckades**: händelsen har levererats till prenumerationens slut punkt och ett 2xx-svar togs emot.
* **Leveransen misslyckades**: varje gång tjänsten försöker leverera och händelse hanteraren inte returnerar en lyckad 2xx-kod ökar den **misslyckade leverans** räknaren. Om vi försöker leverera samma händelse flera gånger och Miss lyckas, ökar den **misslyckade leverans** räknaren för varje fel.
* **Utgångna händelser**: händelsen levererades inte och alla nya återförsök har skickats. Händelsen släpptes.
* **Matchade händelser**: händelsen i ämnet matchades av händelse prenumerationen.

    > [!NOTE]
    > En fullständig lista över mått finns i [mått som stöds av Azure Event Grid](metrics.md).

## <a name="view-custom-topic-metrics"></a>Visa mått för anpassade ämnen

Om du har publicerat ett anpassat ämne kan du visa måtten för det. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I Sök fältet i avsnittet skriver du **Event Grid ämnen**och väljer **Event Grid ämnen** i list rutan. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Sök efter och välj Event Grid ämnen":::
3. Välj ditt anpassade ämne i listan med ämnen. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Välj ditt anpassade ämne":::
4. Visa måtten för avsnittet anpassad händelse på sidan **Event Grid ämne** . I följande bild minimeras avsnittet **Essentials** som visar resurs gruppen, prenumerationen osv. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Visa händelse mått":::

Du kan skapa diagram med mått som stöds med hjälp av fliken **mått** på sidan **Event Grid ämne** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Ämne – mått Sidan":::

Mer information om mått finns [i mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)

Se till exempel mått diagrammet för måttet **publicerade händelser** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Mått för publicerade händelser":::


## <a name="view-subscription-metrics"></a>Visa prenumerations mått
1. Gå till sidan **Event Grid ämne** genom att följa stegen i föregående avsnitt. 
2. Välj prenumerationen i det nedre fönstret, som du ser i följande exempel. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Välj händelse prenumeration":::    

    Du kan också söka efter **Event Grid prenumerationer** i Sök fältet i Azure Portal, Välj **ämnes typ**, **prenumeration**och **plats** för att se en händelse prenumeration. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Sidan Välj händelse prenumeration från Event Grid prenumerationer":::        

    För anpassade ämnen väljer du **Event Grid ämnen** som **typ av ämne**. I system avsnitt väljer du typ av Azure-resurs, till exempel **lagrings konton (BLOB, GPv2)**. 
3. Se prenumerations måtten på Start sidan för prenumerationen i ett diagram. Du kan se **allmänna**, **fel**, **svars tider**och värden för **obeställbara meddelanden** under de senaste 1 timmarna, 6 timmar, 12 timmar, 1 dag, 7 dagar eller 30 dagar. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Mått på Start sidan för prenumerationen":::    

## <a name="view-system-topic-metrics"></a>Visa statistik över system ämnen

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I Sök fältet i avsnittet skriver du **Event Grid system ämnen**och väljer sedan **Event Grid system ämnen** i list rutan. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Sök efter och välj Event Grid system ämnen":::
3. Välj system ämne i listan med ämnen. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Välj ditt system ämne":::
4. Visa måtten för system-avsnittet på ämnes sidan för **Event Grid systemet** . I följande bild minimeras avsnittet **Essentials** som visar resurs gruppen, prenumerationen osv. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Visa statistik för system ämnen på sidan Översikt":::

Du kan skapa diagram med mått som stöds med hjälp av fliken **mått** på sidan **Event Grid ämne** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="System ämne – mått Sidan":::

Mer information om mått finns [i mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="set-alerts"></a>Ställa in avisering
Du kan ställa in aviseringar för mått för ämnen och domäner i Azure Portal. 

Följande procedur visar hur du skapar en avisering om mått för **obeställbara händelser** för ett anpassat ämne. I det här exemplet skickas ett e-postmeddelande till Azures resurs grupps ägare när antalet förlorade händelser för ett ämne hamnar ovanför 10. 

1. På sidan **Event Grid ämne** för ditt ämne väljer du **aviseringar** på den vänstra menyn och väljer sedan **+ ny varnings regel**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Sidan aviseringar – knappen Ny varnings regel":::
    
    
    Du kan också skapa aviseringar med hjälp av sidan **mått** . Stegen är liknande. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Sidan mått – knappen Skapa avisering":::   
    
2. På sidan **skapa aviserings regel** kontrollerar du att ditt ämne är markerat för resursen. Klicka sedan på **Välj villkor**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Sidan aviseringar – Välj villkor":::    
3. Följ dessa steg på sidan **Konfigurera signal logik** :
    1. Välj ett mått eller en aktivitets logg post. I det här exemplet är inloggade **brevade händelser** markerade. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Välj händelser för obeställbara meddelanden":::        
    2. Välj dimensioner (valfritt). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Konfigurera signallogiken":::        
    3. Rulla nedåt. I avsnittet **aviserings logik** väljer du en **operator**, **sammansättnings typ**och anger ett **tröskelvärde**och väljer **klar**. I det här exemplet utlöses en avisering när det totala antalet död antalet förlorade händelser är större än 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Aviserings logik":::                
4. Gå tillbaka till sidan **skapa aviserings regel** och klicka på **Välj åtgärds grupp**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Knappen Välj åtgärds grupp":::
5. Välj **skapa åtgärds grupp** i verktygsfältet för att skapa en ny åtgärds grupp. Du kan också välja en befintlig åtgärds grupp.        
6. Följ dessa steg på sidan **Lägg till åtgärds grupp** :
    1. Ange ett **namn på åtgärds gruppen**.
    1. Ange ett **kort namn** för åtgärds gruppen.
    1. Välj en **Azure-prenumeration** där du vill skapa åtgärds gruppen.
    1. Välj den **Azure-resurs grupp** där du vill skapa åtgärds gruppen.
    1. Ange ett **namn för åtgärden**. 
    1. Välj **Åtgärds typ**. I det här exemplet är **e-postAzure Resource Manager rollen** markerad, särskilt rollen **ägare** . 
    1. Välj **OK** för att stänga sidan. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Sidan Lägg till åtgärds grupp":::                   
7. Gå tillbaka till sidan **skapa aviserings regel** , ange ett namn för aviserings regeln och välj sedan **skapa aviserings regel**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Namn på aviseringsregel":::  
8. På sidan **aviseringar** i avsnittet ser du nu en länk för att hantera aviserings regler om det inte finns några aviseringar än. Om det finns aviseringar väljer du **chefs aviserings regler** i verktygsfältet.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Hantera aviseringar":::

    > [!NOTE]
    > Den här artikeln beskriver inte alla de olika stegen och kombinationerna som du kan använda för att skapa en avisering. En översikt över aviseringar finns i [Översikt över aviseringar](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
