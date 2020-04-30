---
title: Bygg schemabaserade automatiserade arbets flöden
description: Självstudie – Skapa ett schema baserat, återkommande, automatiserat arbets flöde med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 5d4990fd806aed75d9b5e5ddd3e9a615631d9d65
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82146512"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Självstudie: skapa automatiserade, schemabaserade, återkommande arbets flöden med hjälp av Azure Logic Apps

I den här självstudien visas hur du skapar en [Logic app](../logic-apps/logic-apps-overview.md) och automatiserar ett återkommande arbets flöde som körs enligt ett schema. Mer specifikt körs den här exempel logiks appen varje vardags morgon och kontrollerar res tiden, inklusive trafik, mellan två platser. Om tiden överskrider en viss gräns, skickar logikappen ett e-postmeddelande med restiden och den extra tid som krävs för att nå ditt mål.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en upprepnings utlösare som anger schemat för din Logic app.
> * Lägg till en Bing Maps-åtgärd som hämtar res tiden för en väg.
> * Lägg till en åtgärd som skapar en variabel, konverterar res tiden från sekunder till minuter och lagrar resultatet i variabeln.
> * Lägger till ett villkor som jämför restiden med en angiven tidsgräns.
> * Lägg till en åtgärd som skickar e-post om res tiden överskrider gränsen.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Översikt över Logic app-arbetsflöde med hög nivå](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). I den här snabb starten används ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användar gränssnittet kan skilja sig något.

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* För att kunna hämta restiden för en resväg behöver du en åtkomstnyckel för Bing Maps-API:t. Hämta nyckeln genom att följa anvisningarna för [hur du hämtar en Bing Maps-nyckel](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Från huvud menyn i Azure väljer du **skapa en app för resurs** > **integrerings** > **logik**.

   ![Skapa din Logic app-resurs](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Skapa**.

   ![Ange information om din Logic app](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | LA-TravelTime | Din Logic Apps namn, som endast får innehålla bokstäver, siffror,`-`bindestreck (), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). I det här exemplet används "LA-TravelTime". |
   | **Prenumeration** | <*ditt-Azure-prenumerations namn*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | LA-TravelTime-RG | Namnet på Azure- [resurs gruppen](../azure-resource-manager/management/overview.md)som används för att organisera relaterade resurser. I det här exemplet används "LA-TravelTime-RG". |
   | **Position** | USA, västra | Tdet region där du kan lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app går du till Azure-verktygsfältet och väljer **aviseringar** > **gå till resurs** för din distribuerade Logic-app.

   ![Gå till din nya Logic app-resurs](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps designer öppnas och visar en sida med en introduktions video och ofta använda utlösare och Logic app-mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj Tom Logic app-mall](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Lägg sedan till upprepnings [utlösaren, som aktive](../logic-apps/logic-apps-overview.md#logic-app-concepts)ras baserat på ett angivet schema. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Lägg till upprepnings utlösaren

1. Skriv "upprepning" som filter i rutan Sök i Logic App Designer. I listan **utlösare** väljer du **upprepnings** utlösaren.

   ![Lägg till utlösare för upprepning](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Välj knappen med **tre punkter** (**...**) i formen **upprepning** och välj sedan **Byt namn**. Byt namn på utlösaren med den här beskrivningen: `Check travel time every weekday morning`

   ![Byt namn på beskrivningen av upprepnings utlösaren](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. I utlösaren ändrar du dessa egenskaper.

   ![Ändra intervall och frekvens för upprepnings utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Intervall** | Ja | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Ja | Vecka | Den tidsenhet som används för upprepningen |
   |||||

1. Under **intervall** och **frekvens**, öppna listan **Lägg till ny parameter** och välj de egenskaper som ska läggas till i utlösaren.

   * **Dessa dagar**
   * **Vid dessa timmar**
   * **Vid dessa minuter**

   ![Lägg till egenskaper för upprepnings utlösare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Ange värdena för de ytterligare egenskaperna enligt vad som visas och beskrivs här.

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

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu publicerats men gör inget annat än att upprepas. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="get-the-travel-time-for-a-route"></a>Hämta restiden för en resväg

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som hämtar restiden mellan två platser. Logic Apps tillhandahåller en anslutningsapp för Bing Maps-API:t så att du lätt kan hämta den här informationen. Innan du börjar kontrollerar du att du har en Bing Maps API-nyckel (beskrivs i förutsättningarna för den här kursen).

1. Välj **nytt steg**under utlösaren i Logic App Designer.

1. Under **Välj en åtgärd**väljer du **standard**. Skriv "Bing Maps" som filter i sökrutan och Välj åtgärden **Hämta väg** .

   ![Välj åtgärden Hämta väg](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Om du inte har en Bing Maps-anslutning uppmanas du att skapa en anslutning. Ange anslutnings informationen och välj **skapa**.

   ![Skapa anslutning till Bing Maps API](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutnings namn** | Ja | BingMapsConnection | Ange ett namn på anslutningen. I det här exemplet används "BingMapsConnection". |
   | **API-nyckel** | Ja | <*din-Bing-Maps-nyckel*> | Ange Bing Maps-nyckeln som du fick tidigare. Om du inte har en Bing Maps-nyckel tar du reda på [hur du hämtar en nyckel](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Byt namn på åtgärden med den här beskrivningen: `Get route and travel time with traffic`

1. I åtgärden öppnar du **listan Lägg till ny parameter**och väljer de här egenskaperna som du vill lägga till i åtgärden.

   * **Optimera**
   * **Avståndsenhet**
   * **Travel mode** (Färdsätt)

   ![Lägg till egenskaper till åtgärden Hämta väg](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Ange värdena för åtgärdens egenskaper enligt vad som visas och beskrivs här.

   ![Ange information för åtgärden "Hämta väg"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Waypoint 1** (Platsmarkör 1) | Ja | <*Start-location*> | Startpunkt för resvägen |
   | **Waypoint 2** (Platsmarkör 2) | Ja | <*slut punkt*> | Slutpunkten för resvägen |
   | **Optimera** | Nej | timeWithTraffic | En parameter för att optimera färdvägen, till exempel avstånd, restid med aktuell trafik med mera. Välj parametern "timeWithTraffic". |
   | **Avståndsenhet** | Nej | <*dina inställningar*> | Avståndsenhet för din resväg. I det här exemplet används "mil" som enhet. |
   | **Travel mode** (Färdsätt) | Nej | Driving (Bil) | Färdsättet för din resväg. Välj "kör"-läge. |
   ||||

   Mer information om dessa parametrar finns [Calculate a route](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) (Beräkna en resväg).

1. Spara din logikapp.

Skapa sedan en variabel så att du kan omvandla och lagra den aktuella restiden i minuter i stället för sekunder. På så sätt behöver du inte upprepa omvandlingen och det blir enklare att använda värdet i senare steg. 

## <a name="create-a-variable-to-store-travel-time"></a>Skapa en variabel för att lagra res tiden

Ibland kanske du vill köra åtgärder på data i arbets flödet och sedan använda resultatet i senare åtgärder. Du kan skapa variabler för att spara resultaten efter att de har bearbetats så att du enkelt kan återanvända dem eller referera till dem. Du kan skapa variabler endast på den översta nivån i din logikapp.

Som standard returnerar den föregående åtgärden **Hämta väg** den aktuella res tiden med trafik i sekunder från egenskapen **res varaktighets trafik** . Genom att omvandla och lagra det här värdet i minuter i stället för sekunder blir det enklare att använda värdet senare utan att du behöver omvandla det på nytt.

1. Under åtgärden **Hämta väg** väljer du **nytt steg**.

1. Under **Välj en åtgärd**väljer du **inbyggt**. I rutan Sök anger du "variabler" och väljer åtgärden **initiera variabel** .

   ![Välj åtgärden initiera variabel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Byt namn på åtgärden med den här beskrivningen: `Create variable to store travel time`

1. Ange detaljer för variabeln enligt beskrivningen nedan:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | travelTime | Namnet på variabeln. I det här exemplet används "travelTime". |
   | **Typ** | Ja | Integer | Datatypen för variabeln |
   | **Värde** | Nej| Ett uttryck som omvandlar den aktuella restiden från sekunder till minuter (se stegen under den här tabellen). | Det inledande värdet för variabeln |
   ||||

   1. Skapa uttrycket för egenskapen **Value** genom att klicka i rutan så att listan med dynamiskt innehåll visas. Du kan behöva öka webbläsarens bredd för att kunna se listan. I listan med dynamiskt innehåll väljer du **uttryck**.

      ![Ange information för åtgärden initiera variabel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      När du klickar i vissa redigerings rutor visas listan med dynamiskt innehåll. I den här listan visas egenskaper från tidigare åtgärder som du kan använda som indata i arbets flödet. Listan med dynamiskt innehåll har en uttrycks redigerare där du kan välja funktioner för att köra åtgärder. Uttrycksredigeraren visas bara i listan med dynamiskt innehåll.

   1. Ange det här uttrycket i uttrycksredigeraren: `div(,60)`

      ![Ange det här uttrycket: ”div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Placera markören i uttrycket mellan vänsterparentesen (**(**) och kommatecknet (**,**). 
   Välj **dynamiskt innehåll**.

      ![Positions markör, välj "dynamiskt innehåll"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. I listan med dynamiskt innehåll väljer du **Travel Duration Traffic** (Restid med aktuell trafik).

      ![Välj egenskaps trafik för rese varaktighet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. När egenskap svärdet har matchats inuti uttrycket väljer du **OK**.

      ![Slutför skapandet av uttrycket genom att välja OK](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Egenskapen **Value** visas nu som visas här:

      ![Egenskapen "värde" visas med matchat uttryck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Spara din logikapp.

Lägg sedan till ett villkor som kontrollerar om den aktuella restiden är större än en specifik gräns.

## <a name="compare-the-travel-time-with-limit"></a>Jämför res tiden med gränsen

1. Under den föregående åtgärden väljer du **nytt steg**.

1. Under **Välj en åtgärd**väljer du **inbyggt**. I rutan Sök anger du "Condition" som filter. I listan åtgärder väljer du **villkors** åtgärden.

   ![Välj "villkor"-åtgärd](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Byt namn på villkoret med den här beskrivningen: `If travel time exceeds limit`

1. Bygg ett villkor som kontrollerar om egenskap svärdet **travelTime** överskrider din angivna gräns enligt beskrivningen och visas här:

   1. I villkoret klickar du i rutan **Välj ett värde** på villkorets vänstra sida.

   1. I listan med dynamiskt innehåll som visas under **variabler**väljer du egenskapen **travelTime** .

      ![Bygg villkorets vänstra sida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. I rutan mellan jämförelser väljer du operatorn **är större än** .

   1. I rutan **Välj ett värde** på villkorets högra sida anger du den här gränsen:`15`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Slut villkor för att kontrol lera res tiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Spara din logikapp.

Lägg sedan till åtgärden som ska köras när res tiden överskrider din gräns.

## <a name="send-email-when-limit-exceeded"></a>Skicka ett e-postmeddelande när gränsen överskrids

Nu lägger du till en åtgärd som skickar ett e-postmeddelande när restiden överskrider din gräns. E-postmeddelandet innehåller den aktuella restiden och den extra restid som krävs för den angivna resvägen.

1. Välj **Lägg till en åtgärd**i villkoret **om sant** i slutet.

1. Under **Välj en åtgärd**väljer du **standard**. Skriv "skicka e-post" i rutan Sök. Listan returnerar många resultat, så först väljer du den e-postanslutning som du vill ha, till exempel:

   ![Välj den e-postkoppling som du vill använda](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**.
   * För personliga Microsoft-konton väljer du **Outlook.com**.

1. När kopplingens åtgärder visas väljer du "skicka e-post åtgärd" som du vill använda, till exempel:

   ![Välj åtgärden ”send email” (skicka e-post)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Om du inte har någon anslutning ombeds du logga in på ditt e-postkonto.

   Logic Apps skapar en anslutning till ditt e-postkonto.

1. Byt namn på åtgärden med den här beskrivningen: `Send email with travel time`

1. Ange mottagarens e-postadress i fältet **Till**. I testsyfte använder du din egen e-postadress.

1. I rutan **Ämne** anger du ämnet för e-postmeddelandet och tar med variabeln **travelTime** (Restid).

   1. Ange texten `Current travel time (minutes):` med ett avslutande blanksteg. 

   1. I listan med dynamiskt innehåll under **variabler**väljer du **Se fler**.

      ![Hitta variabeln "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. När **travelTime** visas under **variabler**väljer du **travelTime**.

      ![Ange ämnestext och uttryck som returnerar restiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Ange innehållet för e-postmeddelandet i rutan **Brödtext**.

   1. Ange texten `Add extra travel time (minutes):` med ett avslutande blanksteg.

   1. I listan med dynamiskt innehåll väljer du **uttryck**.

      ![Skapa uttryck för e-postmeddelandets brödtext](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. I uttrycksredigeraren anger du det här uttrycket för att beräkna hur många minuter som överskrider gränsen: ```sub(,15)```

      ![Ange ett uttryck för att beräkna den extra restiden i minuter](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Placera markören i uttrycket mellan vänsterparentesen (**(**) och kommatecknet (**,**). Välj **dynamiskt innehåll**.

      ![Fortsätt att skapa uttrycket för att beräkna den extra restiden i minuter](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Välj **travelTime** (Restid) under **Variables** (Variabler).

      ![Välj egenskapen "travelTime" som ska användas i uttrycket](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. När egenskapen har matchats inuti uttrycket väljer du **OK**.

      ![När "Body"-egenskapen har matchats väljer du "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Egenskapen **Body** visas nu som visas här:

      ![Egenskapen "Body" har matchats i uttrycket](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Spara din logikapp.

Nu testar du logikappen, som ser ut som i det här exemplet:

![Avsluta exempel på Logic app-arbetsflöde](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**.

* Om den aktuella res tiden ligger under gränsen, gör din Logic app inget annat och väntar eller nästa intervall innan du kontrollerar igen. 

* Om den aktuella res tiden överskrider din gräns får du ett e-postmeddelande med den aktuella res tiden och antalet minuter över din gräns. Här är ett exempel på ett e-postmeddelande som logikappen skickar:

![Exempel på skickad e-post som visar res tiden](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar! Du har nu skapat och kört en logikapp med en återkommande åtgärd enligt ett schema. 

Om du vill skapa andra Logi Kap par som använder **upprepnings** utlösaren kan du kolla in de här mallarna som är tillgängliga när du har skapat en Logic app:

* Få dagliga påminnelser skickade till dig.
* Ta bort gamla Azure-blobar.
* Lägg till ett meddelande i en Azure Storage-kö.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver appen exempel logik tar du bort resurs gruppen som innehåller din Logic app och relaterade resurser. 

1. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen.

1. Välj **Översikt** > **ta bort resurs grupp**på menyn resurs grupp. 

   ![”Översikt” > ”Ta bort resursgrupp”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Ange resurs gruppens namn som bekräftelse och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du skapat en logikapp som kontrollerar trafiken baserat på ett angivet schema (på morgonen på vardagar) och vidtar en åtgärd (skickar ett e-postmeddelande) när restiden överstiger en angiven gräns. Nu kan du lära dig hur du skapar en logikapp som skickar begäranden om distributionslista för godkännande genom att integrera Azure-tjänster, Microsoft-tjänster och andra SaaS-appar.

> [!div class="nextstepaction"]
> [Hantera begäranden om distributionslista](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
