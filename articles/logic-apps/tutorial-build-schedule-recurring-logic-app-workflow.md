---
title: Skapa schemabaserade automatiserade arbetsflöden
description: Självstudiekurs - Skapa ett schemabaserat, återkommande, automatiserat arbetsflöde med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 17802228c8f08e3c8f1533296e2d39080f6f8b7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456620"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Självstudiekurs: Skapa automatiserade, schemabaserade, återkommande arbetsflöden med hjälp av Azure Logic Apps

Den här självstudien visar hur du skapar en [logikapp](../logic-apps/logic-apps-overview.md) och automatiserar ett återkommande arbetsflöde som körs enligt ett schema. Den här exempellogiska appen körs varje vardagsmorgon och kontrollerar restiden, inklusive trafik, mellan två platser. Om tiden överskrider en viss gräns, skickar logikappen ett e-postmeddelande med restiden och den extra tid som krävs för att nå ditt mål.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en återkommande utlösare som anger schemat för logikappen.
> * Lägg till en Bing Maps-åtgärd som får restiden för en rutt.
> * Lägg till en åtgärd som skapar en variabel, konverterar restiden från sekunder till minuter och lagrar som resulterar i variabeln.
> * Lägger till ett villkor som jämför restiden med en angiven tidsgräns.
> * Lägg till en åtgärd som skickar e-post om restiden överskrider gränsen.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Översikt över arbetsflödet för logikappar på hög nivå](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). Den här snabbstarten använder ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användargränssnittet kan skilja sig något åt.

* För att kunna hämta restiden för en resväg behöver du en åtkomstnyckel för Bing Maps-API:t. Hämta nyckeln genom att följa anvisningarna för [hur du hämtar en Bing Maps-nyckel](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. På Huvudmenyn i Azure väljer du Skapa en > **resursintegrationslogikapp** > **Logic App**. **Create a resource**

   ![Skapa logikappresursen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Skapa**.

   ![Ange information om logikappen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | LA-TravelTime | Logikappens namn, som bara kan innehålla bokstäver,`-`siffror, bindestreck (`(`), `)`understreck`.`(`_`), parenteser ( och punkter ( ). I det här exemplet används "LA-TravelTime". |
   | **Prenumeration** | <*ditt-Azure-prenumerationsnamn*> | Ditt Azure-prenumerationsnamn |
   | **Resursgrupp** | LA-TravelTime-RG | Namnet på [Azure-resursgruppen](../azure-resource-manager/management/overview.md), som används för att ordna relaterade resurser. I det här exemplet används "LA-TravelTime-RG". |
   | **Location** | USA, västra | TRegionen var du ska lagra din logikappinformation. I det här exemplet används "Västra USA". |
   | **Logga Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app väljer du **Meddelanden** > **gå till resurs** för din distribuerade logikapp i Verktygsfältet i Azure.

   ![Gå till din nya logikappresurs](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Du kan också söka efter och välja logikappen genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnar och visar en sida med en introduktionsvideo och vanliga utlösare och logikappmönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj tom logikappmall](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Lägg sedan till återkommande [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses baserat på ett angivet schema. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Lägga till utlösaren för återkommande

1. Ange "återkommande" som filter i sökrutan i logikappdesignern. Välj utlösaren **för upprepning** i listan **Utlösare.**

   ![Lägga till utlösaren "Återkommande"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Markera knappen **ellipser** (**...**) i formen **Återkommande** och välj sedan **Byt namn**. Byt namn på utlösaren med den här beskrivningen: `Check travel time every weekday morning`

   ![Byta namn på beskrivningen av utlösaren för upprepning](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Ändra dessa egenskaper i utlösaren.

   ![Ändra intervall och frekvens för återkommande utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Intervall** | Ja | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Ja | Vecka | Den tidsenhet som används för upprepningen |
   |||||

1. Öppna listan **Lägg till ny parameter** under **Intervall** och **Frekvens**och välj de här egenskaperna som du vill lägga till i utlösaren.

   * **Dessa dagar**
   * **Vid dessa timmar**
   * **Vid dessa minuter**

   ![Lägga till egenskaper för återkommande utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Ange nu värdena för de ytterligare egenskaper som visas och beskrivs här.

   ![Ange schema- och upprepningsinformation](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Dessa dagar** | Måndag,Tisdag,Onsdag,Torsdag,Fredag | Endast tillgängligt när Vecka har valts i fältet **Frekvens** |
   | **Vid dessa timmar** | 7,8,9 | Endast tillgängligt när Vecka eller Dag har valts i fältet **Frekvens**. Välj vid vilka timmar på dagen den här upprepningen ska köras. Det här exemplet körs vid 7, 8 och 9. |
   | **Vid dessa minuter** | 0,15,30,45 | Endast tillgängligt när Vecka eller Dag har valts i fältet **Frekvens**. Välj vid vilka minuter på dagen den här upprepningen ska köras. Det här exemplet körs varje kvart och börjar vid noll minuter. |
   ||||

   Den här utlösaren utlöses varje kvart under vardagar med start kl. 7:00 och slut kl. 9:45. Upprepningsschemat visas i rutan **Förhandsgranskning**. Mer information finns i [Schemalägga aktiviteter och arbetsflöden](../connectors/connectors-native-recurrence.md) och [Arbetsflödesåtgärder och utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Om du vill dölja utlösarens information för tillfället klickar du på formens namnlist.

   ![Minimera form för att dölja information](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Logikappen har nu publicerats men gör inget annat än att upprepas. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="get-the-travel-time-for-a-route"></a>Hämta restiden för en resväg

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som hämtar restiden mellan två platser. Logic Apps tillhandahåller en anslutningsapp för Bing Maps-API:t så att du lätt kan hämta den här informationen. Innan du börjar kontrollerar du att du har en Bing Maps API-nyckel (beskrivs i förutsättningarna för den här kursen).

1. Välj **Nytt steg**under utlösaren i Logic App Designer.

1. Under **Välj en åtgärd**väljer du **Standard**. I sökrutan anger du "bing maps" som filter och väljer åtgärden **Hämta rutt.**

   ![Välj åtgärden Hämta rutt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Om du inte har en Bing Maps-anslutning uppmanas du att skapa en anslutning. Ange dessa anslutningsinformation och välj **Skapa**.

   ![Skapa anslutning till API:et för Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | BingMapsConnection | Ange ett namn på anslutningen. I det här exemplet används "BingMapsConnection". |
   | **API-nyckel** | Ja | <*your-Bing-Maps-nyckel*> | Ange Bing Maps-nyckeln som du fick tidigare. Om du inte har en Bing Maps-nyckel tar du reda på [hur du hämtar en nyckel](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Byt namn på åtgärden med den här beskrivningen: `Get route and travel time with traffic`

1. Öppna listan Lägg **till ny parameter**i åtgärden och välj de här egenskaperna som du vill lägga till i åtgärden.

   * **Optimera**
   * **Avståndsenhet**
   * **Travel mode** (Färdsätt)

   ![Lägg till egenskaper i åtgärden Hämta rutt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Ange nu värdena för åtgärdens egenskaper som visas och beskrivs här.

   ![Ange information om åtgärden "Hämta rutt"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Waypoint 1** (Platsmarkör 1) | Ja | <*start-plats*> | Startpunkt för resvägen |
   | **Waypoint 2** (Platsmarkör 2) | Ja | <*slutplats*> | Slutpunkten för resvägen |
   | **Optimera** | Inga | timeWithTraffic | En parameter för att optimera färdvägen, till exempel avstånd, restid med aktuell trafik med mera. Välj parametern "timeWithTraffic". |
   | **Avståndsenhet** | Inga | <*dina önskemål*> | Avståndsenhet för din resväg. I det här exemplet används "Mile" som enhet. |
   | **Travel mode** (Färdsätt) | Inga | Driving (Bil) | Färdsättet för din resväg. Välj "Körläge". |
   ||||

   Mer information om dessa parametrar finns [Calculate a route](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) (Beräkna en resväg).

1. Spara din logikapp.

Skapa sedan en variabel så att du kan omvandla och lagra den aktuella restiden i minuter i stället för sekunder. På så sätt behöver du inte upprepa omvandlingen och det blir enklare att använda värdet i senare steg. 

## <a name="create-a-variable-to-store-travel-time"></a>Skapa en variabel för att lagra restid

Ibland kanske du vill köra åtgärder på data i arbetsflödet och sedan använda resultaten i senare åtgärder. Du kan skapa variabler för att spara resultaten efter att de har bearbetats så att du enkelt kan återanvända dem eller referera till dem. Du kan skapa variabler endast på den översta nivån i din logikapp.

Som standard returnerar den föregående **hämta flödesåtgärden** aktuell restid med trafik i sekunder från egenskapen **Trafik för resor.** Genom att omvandla och lagra det här värdet i minuter i stället för sekunder blir det enklare att använda värdet senare utan att du behöver omvandla det på nytt.

1. Under åtgärden **Hämta rutt** väljer du **Nytt steg**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Skriv "variabler" i sökrutan och välj variabelåtgärden **Initialize.**

   ![Välj åtgärden Initiera variabel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Byt namn på åtgärden med den här beskrivningen: `Create variable to store travel time`

1. Ange detaljer för variabeln enligt beskrivningen nedan:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | travelTime | Namnet på variabeln. I det här exemplet används "travelTime". |
   | **Typ** | Ja | Integer | Datatypen för variabeln |
   | **Värde** | Inga| Ett uttryck som omvandlar den aktuella restiden från sekunder till minuter (se stegen under den här tabellen). | Det inledande värdet för variabeln |
   ||||

   1. Om du vill skapa uttrycket för egenskapen **Value** klickar du i rutan så att listan med dynamiskt innehåll visas. Du kan behöva öka webbläsarens bredd för att kunna se listan. Välj **Uttryck**i listan med dynamiskt innehåll .

      ![Ange information för åtgärden "Initiera variabel"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      När du klickar i vissa redigeringsrutor visas listan med dynamiskt innehåll. I den här listan visas alla egenskaper från tidigare åtgärder som du kan använda som indata i arbetsflödet. Listan med dynamiskt innehåll har en uttrycksredigerare där du kan välja funktioner för att köra åtgärder. Uttrycksredigeraren visas bara i listan med dynamiskt innehåll.

   1. Ange det här uttrycket i uttrycksredigeraren: `div(,60)`

      ![Ange det här uttrycket: ”div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Placera markören i uttrycket mellan vänsterparentesen (**(**) och kommatecknet (**,**). 
   välj **Dynamiskt innehåll**.

      ![Placera markören, välj "Dynamiskt innehåll"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. I listan med dynamiskt innehåll väljer du **Travel Duration Traffic** (Restid med aktuell trafik).

      ![Välj egenskapen "Trafik för resor"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. När egenskapsvärdet har lösts i uttrycket väljer du **OK**.

      ![Om du vill slutföra inbyggningen av uttrycket väljer du "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Egenskapen **Value** visas nu som visas här:

      ![Egenskapen "Värde" visas med ett löst uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Spara din logikapp.

Lägg sedan till ett villkor som kontrollerar om den aktuella restiden är större än en specifik gräns.

## <a name="compare-the-travel-time-with-limit"></a>Jämför restiden med gränsen

1. Under föregående åtgärd väljer du **Nytt steg**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange "villkor" som filter i sökrutan. Välj åtgärden **Villkor** i åtgärdslistan.

   ![Välj åtgärd "Villkor"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Byt namn på villkoret med den här beskrivningen: `If travel time exceeds limit`

1. Skapa ett villkor som kontrollerar om egenskapsvärdet **travelTime** överskrider den angivna gränsen enligt beskrivningen och visas här:

   1. I villkoret klickar du i rutan **Välj ett värde** på villkorets vänstra sida.

   1. Välj egenskapen **travelTime** under **Variabler**i listan dynamiskt innehåll som visas.

      ![Bygg villkorets vänstra sida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. I den mellersta jämförelserutan väljer du **operatorn är större än.**

   1. Ange den här gränsen i rutan **Välj ett värde** på villkorets högra sida:`15`

      När du är klar ser villkoret ut så här:

      ![Avslutat villkor för att kontrollera restiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Spara din logikapp.

Lägg sedan till åtgärden som ska köras när restiden överskrider din gräns.

## <a name="send-email-when-limit-exceeded"></a>Skicka ett e-postmeddelande när gränsen överskrids

Nu lägger du till en åtgärd som skickar ett e-postmeddelande när restiden överskrider din gräns. E-postmeddelandet innehåller den aktuella restiden och den extra restid som krävs för den angivna resvägen.

1. I villkorets **Om true-gren** väljer du **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **Standard**. Skriv "skicka e-post" i sökrutan. Listan returnerar många resultat, så välj först den e-postkoppling som du vill ha, till exempel:

   ![Markera den e-postanslutning som du vill använda](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * För Azure-arbets- eller skolkonton väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

1. När kopplingens åtgärder visas väljer du "skicka e-poståtgärd" som du vill använda, till exempel:

   ![Välj åtgärden ”send email” (skicka e-post)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Om du inte har någon anslutning ombeds du logga in på ditt e-postkonto.

   Logic Apps skapar en anslutning till ditt e-postkonto.

1. Byt namn på åtgärden med den här beskrivningen: `Send email with travel time`

1. Ange mottagarens e-postadress i fältet **Till**. I testsyfte använder du din egen e-postadress.

1. I rutan **Ämne** anger du ämnet för e-postmeddelandet och tar med variabeln **travelTime** (Restid).

   1. Ange texten `Current travel time (minutes):` med ett avslutande blanksteg. 

   1. Välj **Visa mer**under **Variabler**i listan med dynamiskt innehåll i listan med dynamiskt innehåll .

      ![Hitta variabeln "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. När **travelTime** visas under **Variabler**väljer du **travelTime**.

      ![Ange ämnestext och uttryck som returnerar restiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Ange innehållet för e-postmeddelandet i rutan **Brödtext**.

   1. Ange texten `Add extra travel time (minutes):` med ett avslutande blanksteg.

   1. Välj **Uttryck**i listan med dynamiskt innehåll .

      ![Skapa uttryck för e-postmeddelandets brödtext](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. I uttrycksredigeraren anger du det här uttrycket för att beräkna hur många minuter som överskrider gränsen: ```sub(,15)```

      ![Ange ett uttryck för att beräkna den extra restiden i minuter](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Placera markören i uttrycket mellan vänsterparentesen (**(**) och kommatecknet (**,**). Välj **Dynamiskt innehåll**.

      ![Fortsätt att skapa uttrycket för att beräkna den extra restiden i minuter](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Välj **travelTime** (Restid) under **Variables** (Variabler).

      ![Välj egenskapen "travelTime" som ska användas i uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. När egenskapen har lösts i uttrycket väljer du **OK**.

      ![När egenskapen "Body" har lösts väljer du "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Egenskapen **Body** visas nu som visas här:

      ![Löst egenskap "Body" i uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Spara din logikapp.

Nu testar du logikappen, som ser ut som i det här exemplet:

![Arbetsflödet för logikappar i färdigt exempel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta logikappen manuellt väljer du **Kör**i verktygsfältet Designer.

* Om den aktuella restiden håller sig under din gräns gör logikappen inget annat och väntar eller nästa intervall innan du kontrollerar igen. 

* Om den aktuella restiden överskrider din gräns får du ett e-postmeddelande med aktuell restid och antalet minuter över din gräns. Här är ett exempel på ett e-postmeddelande som logikappen skickar:

![Exempel skickade e-postmeddelanden som visar restiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar! Du har nu skapat och kört en logikapp med en återkommande åtgärd enligt ett schema. 

Om du vill skapa andra logikappar som använder **utlösaren För återkommande** går du till dessa mallar som är tillgängliga när du har skapat en logikapp:

* Få dagliga påminnelser skickade till dig.
* Ta bort gamla Azure-blobar.
* Lägg till ett meddelande i en Azure Storage-kö.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver exempellogiken tar du bort resursgruppen som innehåller logikappen och relaterade resurser. 

1. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen.

1. Välj **Resursgrupp för översikt** > ta bort på resursgruppsmenyn på**resursgruppsmenyn**. 

   ![”Översikt” > ”Ta bort resursgrupp”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Ange resursgruppsnamnet som bekräftelse och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du skapat en logikapp som kontrollerar trafiken baserat på ett angivet schema (på morgonen på vardagar) och vidtar en åtgärd (skickar ett e-postmeddelande) när restiden överstiger en angiven gräns. Nu kan du lära dig hur du skapar en logikapp som skickar begäranden om distributionslista för godkännande genom att integrera Azure-tjänster, Microsoft-tjänster och andra SaaS-appar.

> [!div class="nextstepaction"]
> [Hantera begäranden om distributionslista](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
