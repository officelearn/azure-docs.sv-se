---
title: "Skapa scheduler-baserade automatiska arbetsflöden - Azure Logic Apps | Microsoft Docs"
description: "Den här kursen visar hur du skapar ett scheduler-baserade återkommande, automatiserat arbetsflöde med Azure Logikappar"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: deb2572de363ca5d0dec0f78f2e30ad648e9b5f8
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="check-traffic-with-a-scheduler-based-logic-app"></a>Kontrollera trafik med en logikapp med Schemaläggaren

Med Azure Logikappar kan du automatisera arbetsflöden som körs enligt ett schema. Den här kursen visar hur du kan skapa en [logikapp](../logic-apps/logic-apps-overview.md) med en scheduler-utlösare som körs varje morgon veckodag och kontrollerar den tid, inklusive trafik mellan två platser. Om tiden överskrider en viss gräns, skickar logikappen e-post med tid och den extra tid som krävs för ditt mål.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tom logikapp. 
> * Lägg till en utlösare som fungerar som en schemaläggare för din logikapp.
> * Lägg till en åtgärd som hämtar tid för en väg.
> * Lägg till en åtgärd som skapar en variabel, konverterar tid från sekunder minuter och sparar resultatet i variabeln.
> * Lägg till ett villkor som jämför tid mot en angiven gräns.
> * Lägg till en åtgärd som skickar e-postmeddelande om tid överskrider gränsen.

När du är klar logikappen som ser ut som det här arbetsflödet på en hög nivå:

![Övergripande logikapp](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Om du inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a> innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett e-postkonto från en e-provider som stöds av Logikappar, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra leverantörer [granska kopplingarna listas här](https://docs.microsoft.com/connectors/). Denna Snabbstart använder en Outlook.com-kontot. Om du använder en annan e-postkonto allmänna steg förblir densamma, men ditt användargränssnitt visas något annorlunda.

* För att få tid för ett flöde, behöver du en åtkomstnyckel för Bing Maps API. Följ anvisningarna för att få den här nyckeln <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">få en nyckel för Bing Maps</a>. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> med dina Azure-autentiseringsuppgifter.

## <a name="create-your-logic-app"></a>Skapa din logikapp

1. Välj **Ny** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Skapa en logikapp](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. Under **skapa logikapp**, ger den här informationen om din logikapp som visas och beskrivs. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Ange information om logik appen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | LA TravelTime | Namn för din logikapp | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Namn för din Azure-prenumeration | 
   | **Resursgrupp** | LA-TravelTime-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) används för att organisera relaterade resurser | 
   | **Plats** | Östra USA 2 | Regionen där att lagra information om din logikapp | 
   | **Log Analytics** | Av | Behåll den **av** för diagnostikloggning. | 
   |||| 

3. När Azure distribuerar appen, Designer för Logic Apps öppnas och visar en sida med en video introduktion och mallar för vanliga logik app mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Lägg till återkommande [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), vilka utlöses baserat på ett angivet schema. Varje logikapp måste börja med en utlösare som utlöses när en viss händelse inträffar eller när nya data uppfyller ett visst villkor. Mer information finns i [skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-scheduler-trigger"></a>Lägga till scheduler-utlösare

1. Ange ”återkommande” i sökrutan på designern. Välj den här utlösaren: **schema - upprepning**

   ![Söka efter och lägga till ”Schemalägg återkommande” utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. På den **återkommande** form, Välj den **ellipserna** (**...** ) knappen och välj **Byt namn på**. Byta namn på utlösare med beskrivningen:```Check travel time every weekday morning```

   ![Byta namn på utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. I utlösare, Välj **visa avancerade alternativ**.

4. Ange schema och återkommande uppgifter för din utlösare som visas och beskrivs:

   ![Ange schema och återkommande information](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Intervall** | 1 | Antalet intervall som ska förflyta mellan kontroller | 
   | **Frekvens** | Vecka | Tidsenhet för återkommande | 
   | **Tidszon** | Ingen | Gäller bara när du anger en starttid. Användbart för att ange en icke-lokala tidszon. | 
   | **Starttid** | Ingen | Fördröjning upprepning tills ett visst datum och tid. Mer information finns i [schemalägga aktiviteter och arbetsflöden som körs regelbundet](../connectors/connectors-native-recurrence.md). | 
   | **På följande dagar** | Måndag, tisdag, onsdag, torsdag, fredag | Endast tillgängligt när **frekvens** är inställd på ”vecka” | 
   | **Vid dessa timmar** | 7,8,9 | Endast tillgängligt när **frekvens** är inställd på ”vecka” eller ”dag”. Välj tidpunkter på dagen att köra upprepningen. Det här exemplet körs vid 7, 8 och 9 timmars märken. | 
   | **Vid dessa minuter** | 0,15,30,45 | Endast tillgängligt när **frekvens** är inställd på ”vecka” eller ”dag”. Välj minuter på dagen för att köra upprepningen. Det här exemplet körs varje kvart som börjar på noll timmars markering. | 
   ||||

   Den här utlösaren utlöses varje veckodag varje kvart som börjar på 7:00:00 och slutar kl 9:45. 
   Den **Preview** upprepningsschemat visas i rutan. 
   Mer information finns i [schemalägga aktiviteter och arbetsflöden](../connectors/connectors-native-recurrence.md) och [arbetsflödesåtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

5. Klicka i namnlisten för formens om du vill dölja utlösarens information för tillfället.

   ![Dölj form för att dölja detaljer](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet. 

Din logikapp nu är live men göra inte något annat utför. Lägg därför till en åtgärd som svarar när utlösaren utlöses.

## <a name="get-the-travel-time-for-a-route"></a>Hämta tid för en väg

Nu när du har en utlösare, lägga till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som hämtar tid mellan två platser. Logic Apps tillhandahåller en koppling för Bing Maps API så att du lätt kan hämta den här informationen. Kontrollera att du har en Bing Maps API-nyckel som beskrivs i den här kursen krav innan du börjar den här uppgiften.

1. I logik App Designer under utlösaren, Välj **+ nytt steg** > **lägga till en åtgärd**.

2. Sök efter ”maps” och markera den här åtgärden: **Bing Maps - Get-väg**

3. Om du inte har en Bing Maps-anslutning, uppmanas du att skapa en anslutning. Ange dessa anslutningsinformationen och välj **skapa**.

   ![Välj ”Bing Maps - Get-väg” åtgärd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Anslutningsnamn** | BingMapsConnection | Ange ett namn för anslutningen. | 
   | **API-nyckel** | <*your-Bing-Maps-key*> | Ange Bing Maps-nyckel som du tidigare har fått. Lär dig mer om du inte har en Bing Maps-nyckel, <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">få en nyckel</a>. | 
   | | | |  

4. Byt namn på åtgärden med den här beskrivningen:```Get route and travel time with traffic```

5. Ange information för den **Get väg** åtgärd som visas och som beskrivs här, till exempel:

   ![Ange information för ”Bing Maps - Get-väg” åtgärd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Waypoint 1** | <*Start-plats*> | Din väg ursprung | 
   | **Waypoint 2** | <*End-plats*> | Din väg mål | 
   | **Undvik** | Ingen | Alla objekt för att undvika längs vägen, till exempel motorvägar, vägtullar och så vidare | 
   | **Optimera** | timeWithTraffic | En parameter för att optimera din flödet, till exempel avståndet reser tid med aktuella trafik och så vidare. Välj den här parametern: ”timeWithTraffic” | 
   | **Avståndet enhet** | <*dina inställningar*> | Enheten för avstånd för din vägen. Den här artikeln använder denna enhet: ”mil”  | 
   | **Resa läge** | Körning | Resa läge för din vägen. Välj det här läget: ”kör” | 
   | **Datum och tid för överföringen** | Ingen | Gäller endast överföring-läge | 
   | **Överföring datum-typ** | Ingen | Gäller endast överföring-läge | 
   |||| 

   Mer information om dessa parametrar finns [beräkna en väg](https://msdn.microsoft.com/library/ff701717.aspx).

6. Spara din logikapp.

Skapa sedan en variabel så att du kan omvandla och lagra den aktuella tid som minuter i stället för sekunder. På så sätt kan du undvika att upprepa konverteringen och använder värdet enklare i senare steg. 

## <a name="create-variable-to-store-travel-time"></a>Skapa variabeln för att lagra tid

Ibland kanske du vill utföra åtgärder på data i arbetsflödet och använda resultaten i senare åtgärder. Du kan skapa variabler för att lagra dessa resultat efter bearbetning av dem för att spara de här resultaten returneras så att du kan enkelt återanvända eller refererar till dem. Du kan skapa variabler endast på den översta nivån i din logikapp.

Som standard den tidigare **Get väg** åtgärd returnerar den aktuella tid med trafik i sekunder till den **reser varaktighet trafik** fältet. Genom att konvertera och lagrar det här värdet minuter i stället kan kontrollera du värdet lättare att återanvända senare utan att konvertera igen.

1. Under den **Get väg** åtgärd, Välj **+ nytt steg** > **lägga till en åtgärd**.

2. Sök efter ”variabler” och markera den här åtgärden: **variabler - initiera variabeln**

   ![Välj ”variabler - initiera variabeln” åtgärd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Byt namn på den här åtgärden med den här beskrivningen:```Create variable to store travel time```

4. Ange detaljer för variabeln enligt nedan:

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | travelTime | Namnet på variabeln | 
   | **Typ** | Integer | Datatypen för variabeln | 
   | **Värde** | Ett uttryck som konverterar den aktuella tid från sekunder minuter (se steg under den här tabellen). | Det initiala värdet för variabeln | 
   |||| 

   1. Skapa uttryck för den **värdet** fältet, klicka i fältet så att dynamiskt innehåll visas. 
   Om det behövs bredda webbläsaren tills listan visas. 
   I listan över dynamiskt innehåll, Välj **uttryck**. 

      ![Ange information för ”variabler - initiera variabeln” åtgärd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      När du klickar i vissa rutor visas en dynamisk innehåll lista eller en infogad parameterlista. I listan visas alla parametrar från tidigare åtgärder som kan användas som indata i arbetsflödet. 
      Listan över dynamiskt innehåll innehåller en uttrycksredigerare där du kan välja funktioner för att utföra åtgärder. 
      Den här uttrycksredigerare visas bara i listan över dynamiskt innehåll.

      Din webbläsarbredd anger vilken lista visas. 
      Om din webbläsare är stort, visas dynamiskt innehåll. 
      Om din webbläsare är begränsade, visas en parameterlista infogade under redigeringsrutan som har fokus för tillfället.

   2. Ange det här uttrycket i uttrycksredigeraren för:```div(,60)```

      ![Ange det här uttrycket: ”div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Placera markören i uttrycket mellan vänsterparentes (**(**) och kommatecknet (**,**). 
   Välj **dynamiskt innehåll**.

      ![Placera markören, Välj ”dynamiskt innehåll”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. Välj i listan om dynamiskt innehåll, **resa varaktighet trafik**.

      ![Markera fältet ”resa varaktighet trafik”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Välj när fältet försvinner i uttrycket **OK**.

      ![Klicka på ”OK”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Den **värdet** fältet visas nu som visas här:

      ![Fältet ”värde” med löst uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Spara din logikapp.

Sedan lägger till ett villkor som kontrollerar om den aktuella tid är större än en viss gräns.

## <a name="compare-travel-time-with-limit"></a>Jämför tid med gräns

1. Välj under den föregående åtgärden **+ nytt steg** > **Lägg till ett villkor**. 

2. Byt namn på villkoret med den här beskrivningen:```If travel time exceeds limit```

3. Skapa ett villkor som kontrollerar om **travelTime** överskrider den angivna gränsen som beskrivs och visas här:

   1. I villkoret, klicka i den **väljer ett värde** i rutan till vänster (wide Webbläsarvy) eller överst (smala Webbläsarvy).

   2. Den dynamiska innehåll eller parameterlistan, Välj den **travelTime** fältet under **variabler**.

   3. Välj den här operatorn i rutan jämförelse: **är större än**

   4. I den **väljer ett värde** till höger (wide vy) eller längst ned (smala vy), ange den här gränsen:```15```

   Till exempel om du arbetar i begränsade vyn är här hur du skapar detta villkor:

   ![Skapa villkor i smala vy](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Spara din logikapp.

Lägg sedan till åtgärden som ska utföras när tid överskrider gränsen.

## <a name="send-email-when-limit-exceeded"></a>Skicka e-post när gränsen överskrids

Lägg till en åtgärd som e-postmeddelanden när tid överskrider gränsen. E-postmeddelandet innehåller den aktuella tid och den extra tid som krävs för att förflytta sig den angivna vägen. 

1. I villkoret **om värdet är true** Förgrena, Välj **lägga till en åtgärd**.

2. Sök efter ”skicka e-post” och välj den e-anslutningen och ”skicka e-åtgärd” som du vill använda.

   ![Sök efter och välj åtgärden ”Skicka e-post”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Personliga Microsoft-konton, Välj **Outlook.com**. 
   * Azure fungerar eller skolkonton, Välj **Office 365 Outlook**.

3. Om du inte redan har en anslutning, uppmanas du att logga in på ditt e-postkonto.

   Logic Apps skapar en anslutning till ditt e-postkonto.

4. Byt namn på åtgärden med den här beskrivningen:```Send email with travel time```

5. Ange mottagarens e-postadress i fältet **Till**. I testsyfte kan du använda din e-postadress.

6. I den **ämne** rutan Ange de e-postmeddelandets ämne och inkludera den **travelTime** variabeln.

   1. Ange texten ```Current travel time (minutes): ``` med ett avslutande blanksteg. 
   
   2. Markera parameterlistan eller dynamiska innehållslistan **travelTime** under **variabler**. 
   
      Till exempel om webbläsaren visas i smala vy:

      ![Ange ämnestext och uttryck som returnerar tid](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. I den **brödtext** anger du innehållet i e-postmeddelandets brödtext. 

   1. Ange texten ```Add extra travel time (minutes): ``` med ett avslutande blanksteg. 
   
   2. Om det behövs bredda webbläsaren tills listan dynamiskt innehåll visas. 
   I listan över dynamiskt innehåll, Välj **uttryck**.

      ![Skapa uttryck för e-postmeddelandets brödtext](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. Ange det här uttrycket i Uttrycksredigerare, så att du kan beräkna hur många minuter som överskrider gränsen:```sub(,15)```

      ![Ange uttryck för att beräkna extra minuter reser tid](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Placera markören i uttrycket mellan vänsterparentes (**(**) och kommatecknet (**,**). Välj **dynamiskt innehåll**.

      ![Fortsätta att skapa uttryck för att beräkna extra minuter reser tid](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. Under **variabler**väljer **travelTime**.

      ![Välj ”travelTime” fält som ska användas i uttrycket](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Välj när fältet försvinner i uttrycket **OK**.

      ![Fältet ”brödtext” med löst uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Den **brödtext** fältet visas nu som visas här:

      ![Fältet ”brödtext” med löst uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Spara din logikapp.

Därefter testa din logikapp nu ser ut ungefär så här:

![Klar logikapp](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta logikappen manuellt väljer du **Kör** i Designer-verktygsfältet. Om aktuellt reser tid förblir under din gräns, din logikapp har inget annat och väntar på nästa intervall innan kontrollen igen.
Men om den aktuella tid överskrider gränsen, du får ett e-postmeddelande med den aktuella tid och hur många minuter över gränsen. Här är ett exempel e-postmeddelande logikappen skickar:

![E-post skickas med tid](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Om du inte får e-post, kontrollera din e-post skräppost mappen. E-skräppost filtret kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Grattis, du har nu skapats och köra en schemabaserade återkommande logikapp. 

Skapa andra logikappar som använder den **schema - upprepning** Utlös, kolla mallarna som tillgänglig när du skapar en logikapp:

* Hämta dagliga påminnelser som skickats till dig.
* Ta bort äldre Azure BLOB.
* Lägga till ett meddelande till en Azure Storage-kö.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller din logikapp och relaterade resurser när de inte längre behövs. På Azure Huvudmeny, gå till **resursgrupper**, och markera resursgruppen för din logikapp. Välj **ta bort resursgruppen**. Anger resursgruppens namn som bekräftelse och väljer **ta bort**.

![”Overview” > ”Ta bort resursgrupp”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som kontrollerar trafik baserat på ett angivet schema (på veckodag mornings) i den här självstudiekursen och tar åtgärd (skickar e-post) när tid överskrider en angiven gräns. Lär dig nu att skapa en logikapp som skickar e-postlista begäran om godkännande genom att integrera Azure-tjänster, Microsoft-tjänster och andra SaaS-appar.

> [!div class="nextstepaction"]
> [Hantera begäranden om e-postlista](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)