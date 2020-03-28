---
title: Skapa godkännandebaserade automatiserade arbetsflöden
description: Självstudiekurs - Skapa ett godkännandebaserat automatiserat arbetsflöde som bearbetar e-postlisteprenumerationer med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456612"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Självstudiekurs: Skapa automatiska godkännandebaserade arbetsflöden med hjälp av Azure Logic Apps

Den här självstudien visar hur du skapar en [logikapp](../logic-apps/logic-apps-overview.md) som automatiserar ett godkännandebaserat arbetsflöde. Den här logikappen behandlar prenumerationsbegäranden för en e-postlista som hanteras av [MailChimp-tjänsten.](https://mailchimp.com/) Den här logikappen övervakar ett e-postkonto och söker efter begäranden, skickar begäranden om godkännande och lägger till godkända medlemmar på distributionslistan.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar och söker efter prenumerationsbegäranden.
> * Lägg till en åtgärd som skickar e-postmeddelanden för att godkänna och avvisa förfrågningar.
> * Lägg till ett villkor som kontrollerar godkännandesvaret.
> * Lägg till en åtgärd som lägger till godkända medlemmar på distributionslistan.
> * Lägg till ett villkor som kontrollerar om dessa medlemmar har lagts till på listan.
> * Lägg till en åtgärd som skickar e-post angående om dessa medlemmar har lagts till på listan.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Översikt över färdiga logikappar på hög nivå](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Ett MailChimp-konto som innehåller en lista med namnet "test-members-ML" där logikappen kan lägga till e-postadresser för godkända medlemmar. Om du inte har ett konto [registrerar du dig för ett kostnadsfritt konto](https://login.mailchimp.com/signup/)och lär dig sedan hur du skapar en [MailChimp-lista](https://us17.admin.mailchimp.com/lists/#).

* Ett e-postkonto i Office 365 Outlook eller Outlook.com som stöder godkännandearbetsflöden. Den här artikeln använder Office 365 Outlook. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. På Huvudmenyn i Azure väljer du Skapa en > **resursintegrationslogikapp** > **Logic App**. **Create a resource**

   ![Skapa din nya logikappresurs](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Skapa**.

   ![Ange information om logikappen](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | LA-MailingList | Logikappens namn, som bara kan innehålla bokstäver,`-`siffror, bindestreck (`(`), `)`understreck`.`(`_`), parenteser ( och punkter ( ). I det här exemplet används "LA-MailingList". |
   | **Prenumeration** | <*ditt-Azure-prenumerationsnamn*> | Ditt Azure-prenumerationsnamn |
   | **Resursgrupp** | LA-MailingList-RG | Namnet på [Azure-resursgruppen](../azure-resource-manager/management/overview.md), som används för att ordna relaterade resurser. I det här exemplet används "LA-MailingList-RG". |
   | **Location** | USA, västra | TRegionen var du ska lagra din logikappinformation. I det här exemplet används "Västra USA". |
   | **Logga Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app väljer du **Meddelanden** > **gå till resurs** för din distribuerade logikapp i Verktygsfältet i Azure.

   ![Gå till din nya logikappresurs](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Du kan också söka efter och välja logikappen genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnar och visar en sida med en introduktionsvideo och vanliga utlösare och logikappmönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj tom logikappmall](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Lägg sedan till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med prenumerationsbegäranden. Varje logikapp måste börja med en utlösare som utlöses när en viss händelse inträffar eller när nya data uppfyller ett visst villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. Ange `when email arrives` som filter i sökrutan i logikappdesignern. Välj utlösaren **När ett nytt e-postmeddelande anländer till** din e-postleverantör i listan **Utlösare.**

   I det här exemplet används Utlösaren i Office 365 Outlook:

   ![Välj "När ett nytt e-postmeddelande anländer" utlösare för e-postleverantör](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om du uppmanas till det loggar du in på ditt e-postkonto med dina autentiseringsuppgifter så att Logic Apps kan skapa en anslutning till ditt e-postkonto.

1. I utlösaren anger du kriterierna för att kontrollera all ny e-post.

   1. Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Egenskap | Värde | Beskrivning |
      |----------|-------|-------------|
      | **Mapp** | `Inbox` | E-postmappen som ska övervakas |
      | **Intervall** | `1` | Antalet intervaller som ska förflyta mellan kontrollerna |
      | **Frekvens** | `Hour` | Den tidsenhet som används för upprepningen |
      ||||

   1. Lägg nu till en annan egenskap i utlösaren så att du kan filtrera på ämnesraden för e-post. Öppna **listan Lägg till ny parameter**och välj egenskapen **Ämnesfilter.**

      ![Lägg till egenskapen Ämnesfilter för att utlösa](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Mer information om den här utlösarens egenskaper finns i [Office 365 Outlook-anslutningsreferensen](https://docs.microsoft.com/connectors/office365/) eller [Outlook.com anslutningsreferensen](https://docs.microsoft.com/connectors/outlook/).

   1. När egenskapen visas i utlösaren anger du den här texten:`subscribe-test-members-ML`

      ![Ange text för egenskapen "Ämnesfilter"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Om du vill dölja utlösarinformationen för tillfället klickar du på utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Logikappen har nu lanserats men gör inget annat än att kontrollera inkommande e-post. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande om godkännande

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller avvisa begäran.

1. Under utlösaren väljer du **Nytt steg**. 

1. Ange `approval` som filter i sökrutan under **Välj en åtgärd.** Välj åtgärden **Skicka e-post** för din e-postleverantör i åtgärdslistan. 

   I det här exemplet används åtgärden Office 365 Outlook:

   ![Välj åtgärden Skicka e-post för godkännande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Ange information om den här åtgärden enligt beskrivningen: 

   ![Skicka e-postegenskaper för godkännande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Att** | <*din e-postadress*> | Godkännarens e-postadress. I testsyfte kan du använda din egen adress. I det härsophia.owen@fabrikam.comexemplet används den fiktiva " " e-postadressen. |
   | **Subjekt** | `Approve member request for test-members-ML` | En beskrivande e-postrubrik |
   | **Användaralternativ** | `Approve, Reject` | De svarsalternativ som godkännaren kan välja. Som standard kan godkännaren välja antingen "Godkänn" eller "Avvisa" som svar. |
   ||||

   Ignorera för tillfället listan över dynamiskt innehåll som visas när du klickar i specifika redigeringsrutor. I den här listan kan du välja tillgängligt utdata från tidigare åtgärder som du kan använda som indata i arbetsflödet.

   Mer information om åtgärdens egenskaper finns i [Office 365 Outlook-anslutningsreferensen](https://docs.microsoft.com/connectors/office365/) eller [Outlook.com anslutningsreferensen](https://docs.microsoft.com/connectors/outlook/).
 
1. Spara din logikapp.

Lägg sedan till ett villkor för att kontrollera godkännarens valda svar.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under **åtgärden Skicka godkännande e-post** väljer du **Nytt steg**".

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `condition` i sökrutan. Välj åtgärden **Villkor** i åtgärdslistan.

   ![Sök efter och välj åtgärden "Villkor"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Byt namn på villkoret med en bättre beskrivning.

   1. I villkorets namnlist väljer du **knappen ellipser** (**...**) > **Byt namn**.

      ![Ändra namn på villkorets beskrivning](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Byt namn på villkoret med den här beskrivningen: `If request approved`

1. Skapa ett villkor som kontrollerar om godkännaren har valt **Godkänn**.

   1. I villkoret klickar du i rutan **Välj ett värde** på villkorets vänstra sida.

   1. Välj egenskapen **SelectedOption** i listan med dynamiskt innehåll som visas under **Skicka e-post**för godkännande.

      ![Välj "SelectedOption" i listan med dynamiskt innehåll](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. I rutan mellanjämförelse väljer du operatorn **är lika med.**

   1. I rutan **Välj ett värde** på villkorets högra sida anger du den här texten:`Approve`

      När du är klar ser villkoret ut så här:

      ![Färdigt villkor för godkänt exempel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Spara din logikapp.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg nu till en åtgärd som lägger till den godkända medlemmen i e-postlistan.

1. I villkorets **Om true-gren** väljer du **Lägg till en åtgärd**.

1. Under **Välj en** `mailchimp` åtgärd anger du som filter och väljer åtgärden **Lägg till medlem i listan.**

   ![Välj åtgärden Lägg till medlem i listan](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Om du uppmanas att få åtkomst till ditt MailChimp-konto loggar du in med dina MailChimp-autentiseringsuppgifter.

1. Ge information om den här åtgärden enligt beskrivningen och beskrivningen här:

   ![Ange information för Add member to list (Lägg till medlem på listan)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **List Id** | Ja | `test-members-ML` | Namnet på din MailChimp e-postlista. I det här exemplet används "test-members-ML". |
   | **Status** | Ja | `subscribed` | Välj prenumerationsstatus för den nya medlemmen. I det här exemplet används "prenumerera". <p>Mer information finns i dokumentationen om att [hantera prenumeranter med MailChimp-API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-postadress** | Ja | <*ny-medlem-e-postadress*> | I listan med dynamiskt innehåll väljer du **Från** under **När ett nytt e-postmeddelande anländer**, som skickar in e-postadressen för den nya medlemmen. |
   ||||

   Mer information om den här åtgärdens egenskaper finns i [MailChimp-anslutningsreferensen](https://docs.microsoft.com/connectors/mailchimp/).

1. Spara din logikapp.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har lagts till på distributionslistan. På så sätt kan logikappen meddela dig om den här åtgärden lyckas eller misslyckas.

## <a name="check-for-success-or-failure"></a>Kontrollera om status är lyckad eller misslyckad

1. Välj **Lägg till en åtgärd**under åtgärden Lägg till medlem i **listan** i grenen **Om sant** .

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `condition` i sökrutan. Välj **Villkor**i åtgärdslistan .

1. Byt namn på villkoret med den här beskrivningen: `If add member succeeded`

1. Skapa ett villkor som kontrollerar om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan:

   1. I villkoret klickar du i rutan **Välj ett värde,** som finns på villkorets vänstra sida. Välj egenskapen Status under **Lägg till medlem i listan**i listan **dynamiskt** innehåll.

      Ditt tillstånd ser till exempel ut så här:

      ![Under Add member to list (Lägg till medlem på listan) väljer du Status](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. I rutan mellanjämförelse väljer du operatorn **är lika med.**

   1. I rutan **Välj ett värde** på villkorets högra sida anger du den här texten:`subscribed`

      När du är klar ser villkoret ut så här:

      ![Villkor för prenumererade för prenumererat exempel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Skapa de meddelanden som skickas om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs till

1. Välj **Lägg till en åtgärd**i grenen Om **sant** under lägg **till medlem.**

   ![I grenen "Om sant" väljer du "Lägg till en åtgärd"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Under **Välj en åtgärd**anger du `outlook send email` som filter i sökrutan och väljer åtgärden Skicka ett **e-postmeddelande.**

   ![Lägg till åtgärden Skicka ett e-postmeddelande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on success`

1. Ange information för åtgärden som det visas och beskrivs:

   ![Ange information om e-post vid lyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Att** | Ja | <*din e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   | **Subjekt** | Ja | <*ämne-för-framgång-e-post*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Success! Member added to "test-members-ML": ` <p>Välj egenskapen **E-postadress** under **Lägg till medlem i listan**i listan dynamiskt innehåll. |
   | **Brödtext** | Ja | <*body-for-success-e-post*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`New member has joined "test-members-ML":` <p>Välj egenskapen **E-postadress** i listan För dynamiskt innehåll. <p>På nästa rad anger du den här texten:`Member opt-in status: ` <p> Välj egenskapen Status under **Lägg till medlem i listan**i listan **dynamiskt** innehåll. |
   |||||

1. Spara din logikapp.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs till

1. Välj **Lägg till en åtgärd**i den falska grenen Om **falskt** under Lägg **till medlem.**

   ![I grenen "Om falskt" väljer du "Lägg till en åtgärd"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Under **Välj en åtgärd**anger du `outlook send email` som filter i sökrutan och väljer åtgärden Skicka ett **e-postmeddelande.**

   ![Lägg till åtgärd för Send an email (Skicka ett e-postmeddelande)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on failure`

1. Ge information om den här åtgärden enligt beskrivningen och beskrivningen här:

   ![Ange information om e-post vid misslyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Att** | Ja | <*din e-postadress*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   | **Subjekt** | Ja | <*ämne-för-fel-e-post*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Failed, member not added to "test-members-ML": ` <p>Välj egenskapen **E-postadress** under **Lägg till medlem i listan**i listan dynamiskt innehåll. |
   | **Brödtext** | Ja | <*body-for-failure-e-post*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Spara din logikapp. 

Nu testar du logikappen, som ser ut som i det här exemplet:

![Exempel på avslutat arbetsflöde för logikapp](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka en begäran till dig själv om att ansluta till distributionslistan. Vänta tills begäran visas i inkorgen.

1. Om du vill starta logikappen manuellt väljer du **Kör**i verktygsfältet Designer. 

   Om e-postmeddelandet har ett ämne som matchar ämnesfiltret för utlösaren skickar logikappen ditt e-postmeddelande för att godkänna prenumerationsbegäran.

1. Välj Godkänn i **e-postmeddelandet för**godkännande .

1. Om prenumerantens e-postadress inte finns på distributionslistan lägger logikappen till personens e-postadress och skickar dig ett e-postmeddelande liknande det i det här exemplet:

   ![Exempel på e-post - lyckad prenumeration](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Om logikappen inte kan lägga till prenumeranten får du ett e-postmeddelande som liknar det i det här exemplet:

   ![Exempel på e-post - misslyckad prenumeration](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Klart! Nu har du skapat och kör en logikapp som integrerar information över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver exempellogiken tar du bort resursgruppen som innehåller logikappen och relaterade resurser. 

1. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen.

1. Välj **Resursgrupp för översikt** > ta bort på resursgruppsmenyn på**resursgruppsmenyn**. 

   ![”Översikt” > ”Ta bort resursgrupp”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Ange resursgruppsnamnet som bekräftelse och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapade du en logikapp som hanterar godkännande av begäranden till distributionslistor. Nu får du lära dig att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Bearbeta e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)
