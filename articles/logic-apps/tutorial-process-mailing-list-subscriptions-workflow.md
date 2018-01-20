---
title: "Skapa godkännandearbetsflöden för att bearbeta e-postlista begäranden - Azure Logic Apps | Microsoft Docs"
description: "Den här kursen visar hur du skapar arbetsflöden för automatiskt godkännande för bearbetning av e-postlista prenumerationer med Azure Logikappar"
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
ms.openlocfilehash: 26ef6f69ef2f2d50628f4d0b021159526c9a04a7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="manage-mailing-list-requests-with-a-logic-app"></a>Hantera e-postlista begäranden med en logikapp

Med Azure Logikappar kan du automatisera arbetsflöden och integrera data på Azure-tjänster, Microsoft-tjänster, andra programvara som en tjänst (SaaS)-appar och lokalt system. Den här kursen visar hur du kan skapa en [logikapp](../logic-apps/logic-apps-overview.md) som bearbetar prenumerationsbegäranden för en distributionslista som hanteras av den [MailChimp](https://mailchimp.com/) service.
Den här logikapp övervakar ett e-postkonto för dessa begäranden, skickar begäran om godkännande och lägger till godkända medlemmar i listan.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar e-postmeddelanden för prenumerationsbegäranden.
> * Lägg till en åtgärd som skickar e-postmeddelanden för att godkänna eller avvisa förfrågningar.
> * Lägg till ett villkor som kontrollerar godkännande svaret.
> * Lägg till en åtgärd som lägger tillför godkända medlemmar i listan.
> * Lägg till ett villkor som kontrollerar om dessa medlemmar har anslutit i listan.
> * Lägg till en åtgärd som skickar e-postmeddelanden bekräfta om dessa medlemmar har anslutit i listan.

När du är klar logikappen som ser ut som det här arbetsflödet på en hög nivå:

![Övergripande klar logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Om du inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a> innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett MailChimp-konto. Skapa en lista med namnet ”test-medlemmar-ML” där din logikapp kan lägga till e-postadresser för godkända medlemmar. Om du inte har något konto, [registrera dig för ett kostnadsfritt konto](https://login.mailchimp.com/signup/) och lära dig [så här skapar du en lista](https://us17.admin.mailchimp.com/lists/#). 

* Ett e-postkonto med Office 365 Outlook eller Outlook.com, som stöder godkännandearbetsflöden. Den här artikeln använder Office 365 Outlook. Om du använder en annan e-postkonto allmänna steg förblir densamma, men ditt användargränssnitt visas något annorlunda.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> med dina Azure-autentiseringsuppgifter.

## <a name="create-your-logic-app"></a>Skapa din logikapp

1. Välj **Ny** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Skapa en logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. Under **skapa logikapp**, ger den här informationen om din logikapp som visas och beskrivs. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Ange information om logik appen](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | LA-MailingList | Namn för din logikapp | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Namn för din Azure-prenumeration | 
   | **Resursgrupp** | LA-MailingList-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) används för att organisera relaterade resurser | 
   | **Plats** | Östra USA 2 | Regionen där att lagra information om din logikapp | 
   | **Log Analytics** | Av | Behåll den **av** för diagnostikloggning. | 
   |||| 

3. När Azure distribuerar appen, Designer för Logic Apps öppnas och visar en sida med en video introduktion och mallar för vanliga logik app mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Lägg till en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-post med prenumerationsbegäranden.
Varje logikapp måste börja med en utlösare som utlöses när en viss händelse inträffar eller när nya data uppfyller ett visst villkor. Mer information finns i [skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. Ange ”när e-postmeddelandet” i sökrutan på designern. Välj utlösaren för din e-postleverantör:  **< *din e-post providern*> – när en ny e-postmeddelandet**
   
   ![Välj den här utlösaren för e-provider: ”när en ny e-postmeddelandet”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

2. Om du tillfrågas om autentiseringsuppgifter, logga in på ditt e-postkonto så att Logic Apps kan skapa en anslutning till ditt e-postkonto.

3. Nu kriterierna som utlösaren söker i alla nya e-post.

   1. Ange mappen, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mappen, intervall och frekvens för att kontrollera e-post.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Mappen** | Inkorg | E-mappen för att övervaka | 
      | **Intervall** | 1 | Antalet intervall som ska förflyta mellan kontroller | 
      | **Frekvens** | Timme | Tidsenhet för varje intervall mellan kontrollerar  | 
      |  |  |  | 

   2. Välj **visa avancerade alternativ**. I den **ämne Filter** ange texten för utlösaren i ämnet för e-post:```subscribe-test-members-ML```

      ![Ange avancerade alternativ](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Om du vill dölja utlösarens information nu klickar du på utlösarens namnlist.

   ![Dölj form för att dölja detaljer](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

   Din logikapp nu är live men göra inte något annat än Kontrollera din inkommande e-post. 
   Lägg därför till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande för godkännande

Nu när du har en utlösare, lägga till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller Avvisa begäran. 

1. Välj under utlösare, **+ nytt steg** > **lägga till en åtgärd**. Sök efter ”godkännande” och markera den här åtgärden:  **< *din e-post providern*> – Skicka e-post för godkännande**

   ![Välj ”< din-e-post-provider > – Skicka e-post för godkännande”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Ange information för den här åtgärden som visas och beskrivs: 

   ![Ange e-postinställningar för godkännande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*approver-email-address*> | Godkännarens e-postadress. I testsyfte kan använda du din egen adress. | 
   | **Användaralternativ** | Godkänn, avvisa | Svarsalternativ som godkännaren kan välja. Som standard godkännaren kan välja antingen ”godkänna” eller ”ignorera” som deras svar. | 
   | **Ämne** | Godkänn begäran om medlemmen för ML-test-medlemmar | En beskrivande rubrik | 
   |  |  |  | 

   Ignorera dynamiskt innehåll lista eller infogade parameterlista som visas när du klickar i specifika rutor just nu. 
   Den här listan kan du välja parametrar från tidigare åtgärder som kan användas som indata i arbetsflödet. 
   Din webbläsarbredd anger vilken lista visas. 
 
4. Spara din logikapp.

Lägg till ett villkor för att kontrollera godkännarens valda svar.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under den **skicka e-post för godkännande** åtgärd, Välj **+ nytt steg** > **Lägg till ett villkor**.

   Villkorsformen visas tillsammans med alla tillgängliga parametrar som du kan inkludera som indata till arbetsflödet. 

2. Byt namn på villkoret med en bättre beskrivning.

   1. Välj på det villkoret namnlisten **ellipserna** (**...** ) knappen > **Byt namn på**.

      Till exempel om webbläsaren visas i smala vy:

      ![Byt namn på villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Om din webbläsare är bred vy och dynamiska innehållslistan blockerar åtkomsten till på puknappen, stänger du listan genom att välja **lägga till dynamiskt innehåll** i villkoret.

   2. Byt namn på villkoret med den här beskrivningen:```If request approved```

3. Skapa ett villkor som kontrollerar om godkännaren markerade **Godkänn**:

   1. I villkoret, klicka i den **väljer ett värde** i rutan till vänster (wide Webbläsarvy) eller överst (smala Webbläsarvy).
   Markera parameterlistan eller dynamiska innehållslistan den **SelectedOption** fältet under **skicka e-post för godkännande**.

      Till exempel om du arbetar i bred vy villkoret ser ut som i det här exemplet:

      ![Välj under ”Skicka godkännande e-post”, ”SelectedOption”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Välj den här operatorn i rutan jämförelse operator: **är lika med**

   3. I högra (wide vy) eller nedre (smala vy) **väljer ett värde** ange det här värdet:```Approve```

      När du är klar, villkoret som ser ut som i det här exemplet:

      ![Slutförd](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Spara din logikapp.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg till en åtgärd som godkända medlemmen läggs till i en distributionslista.

1. I villkoret **om värdet är true** Förgrena, Välj **lägga till en åtgärd**.
Sök efter ”mailchimp” och markera den här åtgärden: **MailChimp - Lägg till medlem i listan**

   ![Välj ”MailChimp - Lägg till medlem i listan”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Om du uppmanas att logga in med dina MailChimp-konto, logga in med dina MailChimp.

4. Ange information för den här åtgärden som visas som beskrivs här:

   ![Ange information för ”Lägg till medlem i listan”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **List-Id** | ML-test-medlemmar | Namn för distributionslistan MailChimp | 
   | **Status** | prenumererar på | Prenumerationsstatus för den nya medlemmen. Mer information finns i <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">hantera prenumeranter med MailChimp-API: et</a>. | 
   | **E-postadress** | <*new-member-email-address*> | Markera parameterlista eller dynamiska innehållslistan **från** under **när en ny e-post**, där passerar den e-postadress för den nya medlemmen. 
   |  |  |  | 

5. Spara din logikapp.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har anslutit distributionslistan. På så sätt kan din logikapp meddelar dig om den här åtgärden lyckas eller misslyckas.

## <a name="check-for-success-or-failure"></a>Sök efter lyckad eller misslyckad

1. I den **om värdet är true** Förgrena under den **Lägg till medlem i listan** åtgärd, Välj **mer...**   >  **Lägg till ett villkor**.

2. Byt namn på villkoret med den här beskrivningen:```If add member succeeded```

3. Skapa ett villkor som kontrollerar om godkända medlemmen lyckas eller misslyckas i ansluta till en distributionslista:

   1. I villkoret, klicka i den **väljer ett värde** i rutan till vänster (wide Webbläsarvy) eller överst (smala Webbläsarvy).
   Markera parameterlistan eller dynamiska innehållslistan den **Status** fältet under **Lägg till medlem i listan**.

      Till exempel om du arbetar i bred vy villkoret ser ut som i det här exemplet:

      ![Under ”Lägg till medlem i listan”, Välj ”Status”](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Välj den här operatorn i rutan jämförelse operator: **är lika med**

   3. I högra (wide vy) eller nedre (smala vy) **väljer ett värde** ange det här värdet:```subscribed```

   När du är klar, villkoret som ser ut som i det här exemplet:

   ![Slutförda villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Konfigurera sedan e-postmeddelanden ska skickas när godkända medlemmen lyckas eller misslyckas i ansluta till en distributionslista.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs

1. I den **om värdet är true** grenen för villkoret **om lägga till medlem lyckades**, Välj **lägga till en åtgärd**.

   ![I ”om värdet är true” grenen för villkoret, välja ”Lägg till en åtgärd”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Sök efter ”outlook skicka e-post” och markera den här åtgärden:  **< *din e-post providern*> – skicka ett e-postmeddelande**

   ![Lägga till åtgärden för ”skicka ett e-post”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Byt namn på åtgärden med den här beskrivningen:```Send email on success```

4. Ange information för den här åtgärden som visas och beskrivs:

   ![Ange information för lyckade e-post](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*your-email-address*> | E-postadress att skicka e-postmeddelandet lyckades. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | <*subject-for-success-email*> | Ämne för e-post för lyckades. Ange texten för den här kursen och markera det angivna fältet under **Lägg till medlem i listan** i listan parameterlista eller dynamiskt innehåll: <p>”Success! Medlem läggs till 'ML-test-medlemmar': **e-postadress**” | 
   | **Brödtext** | <*body-for-success-email*> | Brödtext innehåll för lyckade e-postmeddelandet. Ange texten för den här självstudiekursen och välj fält som anges under **Lägg till medlem i listan** i listan parameterlista eller dynamiskt innehåll:  <p>”'ML-test-medlemmar' har anslutit till ny medlem: **e-postadress**”</br>”Status för deltagande i medlem: **Status**” | 
   | | | | 

5. Spara din logikapp.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs

1. I den **om falskt** grenen för villkoret **om lägga till medlem lyckades**, Välj **lägga till en åtgärd**.

   ![I ”om det är false” grenen för villkoret, välja ”Lägg till en åtgärd”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Sök efter ”outlook skicka e-post” och markera den här åtgärden:  **< *din e-post providern*> – skicka ett e-postmeddelande**

   ![Lägga till åtgärden för ”skicka ett e-post”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Byt namn på åtgärden med den här beskrivningen:```Send email on failure```

4. Ange information för den här åtgärden som visas som beskrivs här:

   ![Ange information för misslyckade e-post](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*your-email-address*> | E-postadress att skicka e-post för fel. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | <*subject-for-failure-email*> | Ämne för e-post för fel. Ange texten för den här kursen och markera det angivna fältet under **Lägg till medlem i listan** i listan parameterlista eller dynamiskt innehåll: <p>”Misslyckades, medlem inte har lagts till” test-medlemmar-ML': **e-postadress**” | 
   | **Brödtext** | <*body-för-fel-e-post*> | Innehållet brödtext för e-post för fel. Ange texten för den här självstudiekursen: <p>”Medlem kanske redan finns. Kontrollera ditt konto MailChimp ”. | 
   | | | | 

5. Spara din logikapp. 

Därefter testa din logikapp nu ser ut ungefär så här:

 ![Klar logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka ett e-post-begäran att ansluta till en distributionslista till dig själv.
Vänta tills begäran att visas i din inkorg.

3. Om du vill starta logikappen manuellt väljer du **Kör** i Designer-verktygsfältet. 

   Om din e-post har ett ämne som matchar den utlösaren ämne filter, skickar din logikapp du e-post och Godkänn prenumerationsbegäran om.

4. I e-post med godkännande väljer **Godkänn**.

5. Om det inte finns prenumerantens e-postadress i en distributionslista logikappen lägger till personens e-postadress och skickar ett e-postmeddelande som det här exemplet:

   ![Lyckade e-post](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Om din logikapp inte kan lägga till prenumeranten, kan du få ett e-postmeddelande som det här exemplet:

   ![Misslyckade e-post](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Om du inte får e-post, kontrollera din e-post skräppost mappen. 
   E-skräppost filtret kan dirigera om dessa typer av e-post. 
   Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar, nu har du skapat och kör en logikapp som integrerar över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller din logikapp och relaterade resurser när de inte längre behövs. På Azure Huvudmeny, gå till **resursgrupper**, och markera resursgruppen för din logikapp. Välj **ta bort resursgruppen**. Anger resursgruppens namn som bekräftelse och väljer **ta bort**.

![”Overview” > ”Ta bort resursgrupp”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som hanterar godkännanden för e-postlista begäranden i den här självstudiekursen. Lär dig nu att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Processen för e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)