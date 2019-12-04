---
title: Bygg godkännande-baserade automatiserade arbets flöden
description: Självstudie – Skapa ett godkännande baserat automatiserat arbets flöde som bearbetar prenumerationer på e-postlistor med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: bcd90859066911797d78737187cae6d361029ddd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784671"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Självstudie: skapa automatiska godkännande-baserade arbets flöden med hjälp av Azure Logic Apps

Den här självstudien visar hur du skapar en Logi Kap [par som automatiserar ett godkännande](../logic-apps/logic-apps-overview.md) baserat arbets flöde. Mer specifikt bearbetar den här Logic-appen prenumerations begär Anden för en e-postlista som hanteras av [MailChimp](https://mailchimp.com/) -tjänsten. Den här logikappen övervakar ett e-postkonto och söker efter begäranden, skickar begäranden om godkännande och lägger till godkända medlemmar på distributionslistan.

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

![Översikt över färdiga Logic-appar på hög nivå](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* Ett MailChimp-konto som innehåller en lista med namnet "test-members-ML" där din Logic app kan lägga till e-postadresser för godkända medlemmar. Om du inte har ett konto kan du [Registrera dig för ett kostnads fritt konto](https://login.mailchimp.com/signup/)och sedan lära dig [hur du skapar en MailChimp-lista](https://us17.admin.mailchimp.com/lists/#).

* Ett e-postkonto i Office 365 Outlook eller Outlook.com, som stöder arbets flöden för godkännande. Den här artikeln använder Office 365 Outlook. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Från huvud menyn i Azure väljer du **skapa en resurs** > **integration** > **Logic app**.

   ![Skapa din nya Logic app-resurs](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Skapa**.

   ![Ange information om din Logic app](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | LA-MailingList | Din Logic Apps namn, som endast får innehålla bokstäver, siffror, bindestreck (`-`), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). I det här exemplet används "LA-MailingList". |
   | **Prenumeration** | <*your-Azure-subscription-name*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | LA-MailingList-RG | Namnet på Azure- [resurs gruppen](../azure-resource-manager/resource-group-overview.md)som används för att organisera relaterade resurser. I det här exemplet används "LA-MailingList-RG". |
   | **Plats** | USA, västra | Tdet region där du kan lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app går du till Azure-verktygsfältet och väljer **aviseringar** > **gå till resurs** för din distribuerade Logic-app.

   ![Gå till din nya Logic app-resurs](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps designer öppnas och visar en sida med en introduktions video och ofta använda utlösare och Logic app-mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj Tom Logic app-mall](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Lägg sedan till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med prenumerationsbegäranden. Varje Logi Kap par måste börja med en utlösare som utlöses när en enskild händelse inträffar eller när nya data uppfyller ett angivet villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Lägga till utlösare för att övervaka e-post

1. Skriv `when email arrives` som filter i rutan Sök i Logic App Designer. I listan **utlösare** väljer du alternativet **när ett nytt e-postmeddelande anländer** till e-postleverantören.

   I det här exemplet används Office 365 Outlook-utlösare:

   ![Välj "när ett nytt e-postmeddelande kommer"-utlösare för e-postleverantör](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om du uppmanas till det loggar du in på ditt e-postkonto med dina autentiseringsuppgifter så att Logic Apps kan skapa en anslutning till ditt e-postkonto.

1. Ange kriterierna för att kontrol lera alla nya e-postmeddelanden i utlösaren.

   1. Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Egenskap | Värde | Beskrivning |
      |----------|-------|-------------|
      | **Mapp** | `Inbox` | E-postmappen som ska övervakas |
      | **Intervall** | `1` | Antalet intervaller som ska förflyta mellan kontrollerna |
      | **Frekvens** | `Hour` | Den tidsenhet som används för upprepningen |
      ||||

   1. Lägg nu till en annan egenskap i utlösaren så att du kan filtrera på e-postämnesrad. Öppna **listan Lägg till ny parameter**och välj egenskapen **ämne filter** .

      ![Lägg till egenskapen "ämnes filter" för att utlösa](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Mer information om den här utlösarens egenskaper finns i [referens för Office 365 Outlook Connector](https://docs.microsoft.com/connectors/office365/) eller [kopplings referens för Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. När egenskapen visas i utlösaren anger du följande text: `subscribe-test-members-ML`

      ![Ange text för egenskapen "ämnes filter"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Om du vill dölja utlösarinformationen för tillfället klickar du på utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu lanserats men gör inget annat än att kontrollera inkommande e-post. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-approval-email"></a>Skicka e-postmeddelande om godkännande

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande för att godkänna eller avvisa begäran.

1. Under utlösaren väljer du **nytt steg**. 

1. Under **Välj en åtgärd**går du till rutan Sök och anger `approval` som ditt filter. I listan åtgärder väljer du e-poståtgärden **Skicka godkännande** för e-postleverantören. 

   I det här exemplet används Office 365 Outlook-åtgärd:

   ![Välj åtgärden "skicka e-post till godkännande"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Ange information om den här åtgärden enligt beskrivningen: 

   ![Skicka e-postegenskaper för godkännande](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Till** | <*your-email-address*> | Godkännarens e-postadress. I testsyfte kan du använda din egen adress. I det här exemplet används den fiktivasophia.owen@fabrikam.come-postadressen. |
   | **Ämne** | `Approve member request for test-members-ML` | En beskrivande e-postrubrik |
   | **Användaralternativ** | `Approve, Reject` | Svars alternativen som god kännaren kan välja. Som standard kan god kännaren välja antingen "Godkänn" eller "avvisa" som svar. |
   ||||

   För närvarande kan du ignorera den dynamiska innehålls listan som visas när du klickar i vissa redigerings rutor. I den här listan kan du välja tillgängliga utdata från tidigare åtgärder som du kan använda som indata i arbets flödet.

   Mer information om den här åtgärdens egenskaper finns i [referens för Office 365 Outlook Connector](https://docs.microsoft.com/connectors/office365/) eller [kopplings referens för Outlook.com](https://docs.microsoft.com/connectors/outlook/).
 
1. Spara din logikapp.

Lägg sedan till ett villkor för att kontrol lera god kännaren för det valda svaret.

## <a name="check-approval-response"></a>Kontrollera godkännandesvar

1. Under åtgärden **skicka e-post för godkännande** väljer du **nytt steg**".

1. Under **Välj en åtgärd**väljer du **inbyggt**. I sökrutan anger du `condition` som ditt filter. I listan åtgärder väljer du **villkors** åtgärden.

   ![Sök efter och Välj åtgärden "villkor"](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Byt namn på villkoret med en bättre beskrivning.

   1. I villkorets namn List väljer du knappen med **ellipser** ( **...** ) > **Byt namn**.

      ![Beskrivning av Byt namn på villkor](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Byt namn på villkoret med den här beskrivningen: `If request approved`

1. Bygg ett villkor som kontrollerar om god kännaren har valt **Godkänn**.

   1. I villkoret klickar du i rutan **Välj ett värde** på villkorets vänstra sida.

   1. I listan med dynamiskt innehåll som visas under **skicka e-post för godkännande**väljer du egenskapen **SelectedOption** .

      ![Välj "SelectedOption" från listan med dynamiskt innehåll](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. I rutan mellan jämförelse väljer du operatorn **är lika** med.

   1. I rutan **Välj ett värde** på villkorets högra sida anger du följande text: `Approve`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Slut villkor för godkänt exempel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Spara din logikapp.

Ange sedan den åtgärd som utförs av logikappen när granskaren godkänner begäran. 

## <a name="add-member-to-mailchimp-list"></a>Lägg till medlem i MailChimp-lista

Lägg nu till en åtgärd som lägger till den godkända medlemmen i din distributions lista.

1. Välj **Lägg till en åtgärd**i villkoret **om sant** i slutet.

1. Under **Välj en åtgärd**anger du `mailchimp` som filter och väljer åtgärden **Lägg till medlem i lista** .

   ![Välj åtgärden Lägg till medlem i listan](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Om du uppmanas att få åtkomst till ditt MailChimp-konto loggar du in med dina MailChimp-autentiseringsuppgifter.

1. Ange information om den här åtgärden som det visas och beskrivs här:

   ![Ange information för Add member to list (Lägg till medlem på listan)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **List Id** | Ja | `test-members-ML` | Namnet på MailChimp-e-postlistan. I det här exemplet används "test-members-ML". |
   | **Status** | Ja | `subscribed` | Välj prenumerations status för den nya medlemmen. I det här exemplet används "prenumererar". <p>Mer information finns i dokumentationen om att [hantera prenumeranter med MailChimp-API](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **Email Address** | Ja | <*new-member-email-address*> | Från listan med dynamiskt innehåll väljer du **från** under **när ett nytt e-postmeddelande kommer**, vilket skickar e-postadressen för den nya medlemmen. |
   ||||

   Mer information om den här åtgärdens egenskaper finns i [referens för MailChimp-koppling](https://docs.microsoft.com/connectors/mailchimp/).

1. Spara din logikapp.

Sedan lägger till ett villkor så att du kan kontrollera om den nya medlemmen har lagts till på distributionslistan. På så sätt kan logikappen meddela dig om den här åtgärden lyckas eller misslyckas.

## <a name="check-for-success-or-failure"></a>Kontrollera om status är lyckad eller misslyckad

1. Välj **Lägg till en åtgärd**i listan **om sant** under åtgärden **Lägg till medlem till lista** .

1. Under **Välj en åtgärd**väljer du **inbyggt**. I sökrutan anger du `condition` som ditt filter. I listan åtgärder väljer du **villkor**.

1. Byt namn på villkoret med den här beskrivningen: `If add member succeeded`

1. Skapa ett villkor som kontrollerar om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan:

   1. I villkoret klickar du i rutan **Välj ett värde** , som finns på villkorets vänstra sida. Välj egenskapen **status** under **Lägg till medlem i listan**i listan med dynamiskt innehåll.

      Ditt villkor ser t. ex. ut så här:

      ![Under Add member to list (Lägg till medlem på listan) väljer du Status](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. I rutan mellan jämförelse väljer du operatorn **är lika** med.

   1. I rutan **Välj ett värde** på villkorets högra sida anger du följande text: `subscribed`

      När du är klar ser villkoret ut som i det här exemplet:

      ![Slut villkor för prenumererande exempel](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Skapa de meddelanden som skickas om den godkända medlemmen lyckas eller misslyckas med att ansluta till distributionslistan.

## <a name="send-email-if-member-added"></a>Skicka e-post om medlem läggs till

1. Under villkoret **om Lägg till medlem lyckades** i grenen **om sant** väljer du **Lägg till en åtgärd**.

   ![I grenen "om sant" väljer du Lägg till en åtgärd](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Under **Välj en åtgärd**i sökrutan anger du `outlook send email` som ditt filter och väljer åtgärden **skicka e-post** .

   ![Lägg till åtgärden "Skicka ett e-postmeddelande"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on success`

1. Ange information för åtgärden som det visas och beskrivs:

   ![Ange information om e-post vid lyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Till** | Ja | <*your-email-address*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   | **Ämne** | Ja | <*subject-for-success-email*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Success! Member added to "test-members-ML": ` <p>I listan med dynamiskt innehåll, under **Lägg till medlem i listan**, väljer du egenskapen **e-postadress** . |
   | **Brödtext** | Ja | <*body-for-success-email*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`New member has joined "test-members-ML":` <p>Välj egenskapen **e-postadress** i listan med dynamiskt innehåll. <p>Ange följande text på nästa rad: `Member opt-in status: ` <p> Välj egenskapen **status** under **Lägg till medlem i listan**i listan med dynamiskt innehåll. |
   |||||

1. Spara din logikapp.

## <a name="send-email-if-member-not-added"></a>Skicka e-post om medlem inte läggs till

1. Under villkoret **om Lägg till medlem lyckades** , i grenen **om falskt** , väljer du **Lägg till en åtgärd**.

   ![I grenen "om falskt" väljer du "Lägg till en åtgärd"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Under **Välj en åtgärd**i sökrutan anger du `outlook send email` som ditt filter och väljer åtgärden **skicka e-post** .

   ![Lägg till åtgärd för Send an email (Skicka ett e-postmeddelande)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Byt namn på åtgärden med den här beskrivningen: `Send email on failure`

1. Ange information om den här åtgärden som det visas och beskrivs här:

   ![Ange information om e-post vid misslyckad anslutning](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Till** | Ja | <*your-email-address*> | E-postadress att skicka e-postmeddelandet till. I testsyfte kan du använda din egen e-postadress. |
   | **Ämne** | Ja | <*subject-for-failure-email*> | Ämnesraden för e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Failed, member not added to "test-members-ML": ` <p>I listan med dynamiskt innehåll, under **Lägg till medlem i listan**, väljer du egenskapen **e-postadress** . |
   | **Brödtext** | Ja | <*body-for-failure-email*> | Brödtext i e-postmeddelandet. För den här självstudiekursen anger du den här texten: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Spara din logikapp. 

Nu testar du logikappen, som ser ut som i det här exemplet:

![Exempel på sluten Logic app-arbetsflöde](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka en begäran till dig själv om att ansluta till distributionslistan. Vänta tills begäran visas i inkorgen.

1. Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. 

   Om e-postmeddelandet har ett ämne som matchar ämnesfiltret för utlösaren skickar logikappen ditt e-postmeddelande för att godkänna prenumerationsbegäran.

1. I e-postmeddelandet för godkännande väljer du **Godkänn**.

1. Om prenumerantens e-postadress inte finns på distributionslistan lägger logikappen till personens e-postadress och skickar dig ett e-postmeddelande liknande det i det här exemplet:

   ![Exempel på e-post – lyckad prenumeration](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Om logikappen inte kan lägga till prenumeranten får du ett e-postmeddelande som liknar det i det här exemplet:

   ![Exempel på e-post – misslyckad prenumeration](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Klart! Nu har du skapat och kör en logikapp som integrerar information över Azure, Microsoft-tjänster och andra SaaS-appar.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver appen exempel logik tar du bort resurs gruppen som innehåller din Logic app och relaterade resurser. 

1. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen.

1. I menyn resurs grupp väljer du **översikt** > **ta bort resurs grupp**. 

   ![”Översikt” > ”Ta bort resursgrupp”](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Ange resurs gruppens namn som bekräftelse och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapade du en logikapp som hanterar godkännande av begäranden till distributionslistor. Nu får du lära dig att skapa en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions.

> [!div class="nextstepaction"]
> [Bearbeta e-postbilagor](../logic-apps/tutorial-process-email-attachments-workflow.md)
