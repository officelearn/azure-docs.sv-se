---
title: Skapa godkännandearbetsflöden för att bearbeta e-postlistebegäranden – Azure Logic Apps | Microsoft Docs
description: Självstudie – Hur du skapar automatiska arbetsflöden för godkännande, för att bearbeta e-postlisteprenumerationer med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: b48ecce1c87c0a29996e437d621c3ce396a84856
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232672"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Hantera begäranden om distributionslistor med Azure Logic Apps

Med Azure Logic Apps lär du dig att automatisera arbetsflöden och integrera data i olika Azure-tjänster, Microsoft-tjänster och andra SaaS-appar (programvara som en tjänst) samt lokala system. I den här självstudiekursen visar vi hur du kan bygga en [logikapp](../logic-apps/logic-apps-overview.md) som bearbetar prenumerationsbegäranden för e-postlistor som hanteras via tjänsten [MailChimp](https://mailchimp.com/).
Den här logikappen övervakar ett e-postkonto och söker efter begäranden, skickar begäranden om godkännande och lägger till godkända medlemmar på distributionslistan.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar och söker efter prenumerationsbegäranden.
> * Lägg till en åtgärd som skickar e-postmeddelanden för att godkänna och avvisa förfrågningar.
> * Lägg till ett villkor som kontrollerar godkännandesvaret.
> * Lägg till en åtgärd som lägger till godkända medlemmar på distributionslistan.
> * Lägg till ett villkor som kontrollerar om dessa medlemmar har lagts till på listan.
> * Lägg till en åtgärd som skickar e-post angående om dessa medlemmar har lagts till på listan.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Logikapp på hög nivå](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Om du inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a> innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett MailChimp-konto. Skapa en lista med namnet test-members-ML där logikappen kan lägga till e-postadresser till godkända medlemmar. Om du inte har något konto kan du [registrera dig och skapa ett kostnadsfritt konto](https://login.mailchimp.com/signup/) och lära dig [hur du skapar en lista](https://us17.admin.mailchimp.com/lists/#). 

* Ett e-postkonto med Office 365 Outlook eller Outlook.com, som stöder godkännandearbetsflöden. Den här artikeln använder Office 365 Outlook. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Välj **Skapa en resurs** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Skapa en logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Ange information om din logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | LA-MailingList | Logikappens namn | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Azure-prenumerationens namn | 
   | **Resursgrupp** | LA-MailingList-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som används för att organisera relaterade resurser | 
   | **Plats** | USA, östra 2 | Regionen där informationen om logikappen ska lagras | 
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. | 
   |||| 

3. När Azure har distribuerat din app öppnas Logic Apps Designer och en sida med en introduktionsvideo och mallar för vanliga logikappar visas. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Lägg sedan till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med prenumerationsbegäranden.
Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. På designern anger du ”när e-post kommer” i sökrutan. Välj den här utlösaren för din e-postleverantör: **<*your-email-provider*> – När ett nytt e-postmeddelande kommer**
   
   ![Välj den här utlösaren för e-postleverantör: ”När ett nytt e-postmeddelande kommer”](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

2. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så att Logic Apps kan skapa en anslutning till e-postkontot.

3. Specificera nu kriterierna som utlösaren söker efter i ny e-post.

   1. Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Mapp** | Inkorgen | E-postmappen som ska övervakas | 
      | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna | 
      | **Frekvens** | Timme | Tidsenhet för varje intervall mellan kontroller  | 
      |  |  |  | 

   2. Välj **Visa avancerade alternativ**. I rutan **Ämnesfilter** skriver du den här texten som utlösaren hittar i e-postämnet: ```subscribe-test-members-ML```

      ![Ställ in avancerade alternativ](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Om du vill dölja utlösarinformationen för tillfället klickar du på utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

   Logikappen har nu lanserats men gör inget annat än att kontrollera inkommande e-post. 
   Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande om godkännande

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller avvisa begäran. 

1. Under utlösaren väljer du **+ Nytt steg** > **Lägg till en åtgärd**. Sök efter approval (godkännande) och välj den här åtgärden: **<*your-email-provider*> - Send approval email** (din-e-post-leverantör > – skicka e-post om godkännande)

   ![Välj <your-email-provider> - Send approval email (din-e-post-leverantör > – skicka e-post om godkännande)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Ange information för åtgärden som det visas och beskrivs: 

   ![Ställ in e-postinställningar för godkännande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*approver-email-address*> | Godkännarens e-postadress. I testsyfte kan du använda din egen adress. | 
   | **Användaralternativ** | Godkänn, Avvisa | Svarsalternativ som godkännaren kan välja. Standardinställningen är att godkännaren kan välja svaren Godkänn och Avvisa. | 
   | **Ämne** | Godkänn medlemsbegäran för test-members-ML | En beskrivande e-postrubrik | 
   |  |  |  | 

   För nu ska du ignorera listan med dynamiskt innehåll och listan med inlineparametrar som visas när du klickar i vissa redigeringsrutor. 
   I listan kan du välja parametrar från tidigare åtgärder som du kan använda som indata i arbetsflödet. 
   Webbläsarens bredd bestämmer vilken lista som visas. 
 
4. Spara din logikapp.

Lägg sedan till ett villkor för att kontrollera godkännarens valda svar.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under åtgärden **Send approval email** (Skicka e-postmeddelande om godkännande) väljer du **+ New step** > **Add a condition** (+ Nytt steg > Lägg till ett villkor).

   Villkorsformen visas tillsammans med alla tillgängliga parametrar som du kan inkludera som indata för arbetsflödet. 

2. Byt namn på villkoret med en bättre beskrivning.

   1. I villkorets rubriklist väljer du **ellipsknappen** (**...**) > **Byt namn**.

      Om webbläsaren till exempel visas i en smal vy:

      ![Byt namn på villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Om din webbläsare är i bred vy och den dynamiska innehållslistan blockerar åtkomsten till ellipsknappen ska du stänga listan genom att välja **Lägg till dynamiskt innehåll** i villkoret.

   2. Byt namn på villkoret med den här beskrivningen: ```If request approved```

3. Skapa ett villkor som kontrollerar om godkännaren markerade **Godkänn**:

   1. I villkoret klickar du i rutan **Choose a value** (Välj ett värde) till vänster (bred webbläsarvy) eller längst upp (smal webbläsarvy).
   Välj fältet **SelectedOption** (valt alternativ) under **Send approval email** (Skicka e-postmeddelande för godkännande) i listan med dynamiskt innehåll eller i parameterlistan.

      Om du till exempel arbetar i bred vy ser villkoret ut som i det här exemplet:

      ![Under Send approval email (Skicka e-postmeddelande om godkännande) väljer du SelectedOption (valt alternativ)](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. I jämförelseoperatorrutan väljer du operatorn **is equal to** (är lika med)

   3. Till höger (bred vy) eller längst ned (smal vy), i rutan **Välj ett värde** skriver du in det här värdet: ```Approve```

      När du är klar ser villkoret ut som i det här exemplet:

      ![Slutför villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Spara din logikapp.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg nu till en åtgärd som lägger till den godkända medlemmen på en distributionslista.

1. I grenen **Om sant** för villkoret väljer du **Lägg till en åtgärd**.
Sök efter mailchimp och markera den här åtgärden: **MailChimp - Add member to list** (MailChimp – lägg till medlem på listan)

   ![Välj MailChimp - Add member to list (MailChimp – lägg till medlem på listan)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Om du uppmanas att logga in med ditt MailChimp-konto loggar du in med MailChimp-uppgifterna.

4. Ange information för åtgärden som det visas och beskrivs här:

   ![Ange information för Add member to list (Lägg till medlem på listan)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **List Id** | test-members-ML | Namn på MailChimp-distributionslistan | 
   | **Status** | subscribed | Prenumerationsstatus för den nya medlemmen. Mer information finns i dokumentationen om att <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">hantera prenumeranter med MailChimp-API</a>. | 
   | **Email Address** | <*new-member-email-address*> | Välj ur antingen parameterlistan eller listan med dynamiskt innehåll. Välj **Från** under **När ett nytt e-postmeddelande kommer**, vad som händer med e-postadressen för den nya medlemmen. 
   |  |  |  | 

5. Spara din logikapp.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har lagts till på distributionslistan. På så sätt kan logikappen meddela dig om den här åtgärden lyckas eller misslyckas.

## <a name="check-for-success-or-failure"></a>Kontrollera om status är lyckad eller misslyckad

1. I förgreningen **If true** (Om sant) under åtgärden **Add member to list** (Lägg till medlem på listan) väljer du **More...** > **Add a condition** (Mer... > Lägg till ett villkor).

2. Byt namn på villkoret med den här beskrivningen: ```If add member succeeded```

3. Skapa ett villkor som kontrollerar om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan:

   1. I villkoret klickar du i rutan **Choose a value** (Välj ett värde) till vänster (bred webbläsarvy) eller längst upp (smal webbläsarvy).
   Välj från antingen parameterlistan eller listan med dynamiskt innehåll. Välj fältet **Status** under **Lägg till medlem i listan**.

      Om du till exempel arbetar i bred vy ser villkoret ut som i det här exemplet:

      ![Under Add member to list (Lägg till medlem på listan) väljer du Status](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. I jämförelseoperatorrutan väljer du operatorn **is equal to** (är lika med)

   3. Till höger (bred vy) eller längst ned (smal vy), i rutan **Välj ett värde** skriver du in det här värdet: ```subscribed```

   När du är klar ser villkoret ut som i det här exemplet:

   ![Slutförda villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Skapa de meddelanden som skickas om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs till

1. I förgreningen **If true** (Om sant) för villkoret **If add member succeeded** (Om medlem lades till) väljer du **Add an action** (Lägg till en åtgärd).

   ![I grenen Om sant för ett villkor väljer du Lägg till en åtgärd](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Sök efter outlook send email (outlook skicka e-post) och välj den här åtgärden: **<*your-email-provider*> - Send an email** (din-e-post-leverantör > – skicka ett e-postmeddelande)

   ![Lägg till åtgärd för Send an email (Skicka ett e-postmeddelande)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Byt namn på åtgärden med den här beskrivningen: ```Send email on success```

4. Ange information för åtgärden som det visas och beskrivs:

   ![Ange information om e-post vid lyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*your-email-address*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | <*subject-for-success-email*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten och väljer det angivna fältet under **Add member to list** (Lägg till medlem på listan) från listan med parametrar eller listan med dynamiskt innehåll: <p>"Klart. Medlemmen lades till på test-members-ML: **e-postadress**" | 
   | **Brödtext** | <*body-for-success-email*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten och väljer de angivna fälten under **Add member to list** (Lägg till medlem på listan) från listan med parametrar eller listan med dynamiskt innehåll:  <p>Ny medlem har anslutit till listan test-members-ML: **e-postadress**</br>Deltagarstatus för medlem: **status** | 
   | | | | 

5. Spara din logikapp.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs till

1. I förgreningen **If false** (Om falskt) för villkoret **If add member succeeded** (Om medlem lades till) väljer du **Add an action** (Lägg till en åtgärd).

   ![I grenen If false (Om falskt) för villkoret väljer du Add an action (Lägg till en åtgärd)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Sök efter outlook send email (outlook skicka e-post) och välj den här åtgärden: **<*your-email-provider*> - Send an email** (din-e-post-leverantör > – skicka ett e-postmeddelande)

   ![Lägg till åtgärd för Send an email (Skicka ett e-postmeddelande)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Byt namn på åtgärden med den här beskrivningen: ```Send email on failure```

4. Ange information för åtgärden som det visas och beskrivs här:

   ![Ange information om e-post vid misslyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Till** | <*your-email-address*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | <*subject-for-failure-email*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten och väljer det angivna fältet under **Add member to list** (Lägg till medlem på listan) från listan med parametrar eller listan med dynamiskt innehåll: <p>Medlemmen lades inte till på test-members-ML: **e-postadress**" | 
   | **Brödtext** | <*body-for-failure-email*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>”Medlemmen kanske redan finns. Kontrollera MailChimp-kontot." | 
   | | | | 

5. Spara din logikapp. 

Nu testar du logikappen, som ser ut som i det här exemplet:

 ![Färdig logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka en begäran till dig själv om att ansluta till distributionslistan.
Vänta tills begäran visas i inkorgen.

3. Om du vill starta logikappen manuellt väljer du **Kör** i Designer-verktygsfältet. 

   Om e-postmeddelandet har ett ämne som matchar ämnesfiltret för utlösaren skickar logikappen ditt e-postmeddelande för att godkänna prenumerationsbegäran.

4. I e-postmeddelandet med godkännandet väljer du **Godkänn**.

5. Om prenumerantens e-postadress inte finns på distributionslistan lägger logikappen till personens e-postadress och skickar dig ett e-postmeddelande liknande det i det här exemplet:

   ![E-post – lyckades](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Om logikappen inte kan lägga till prenumeranten får du ett e-postmeddelande som liknar det i det här exemplet:

   ![E-post – misslyckades](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Om du inte får e-post kan du titta i mappen Skräppost. 
   Ditt skräppostfilter kan dirigera om dessa typer av e-post. 
   Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Klart! Nu har du skapat och kör en logikapp som integrerar information över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när de inte längre behövs. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen. Välj **Ta bort resursgrupp**. Ange resursgruppens namn som bekräftelse och välj **Ta bort**.

![”Översikt” > ”Ta bort resursgrupp”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapade du en logikapp som hanterar godkännande av begäranden till distributionslistor. Nu får du lära dig att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Bearbeta e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)