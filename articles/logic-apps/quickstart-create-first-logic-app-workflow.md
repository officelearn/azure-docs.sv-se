---
title: Skapa ditt första automatiserade arbetsflöde
description: Snabbstart – Skapa ditt första automatiska arbetsflöde med hjälp av Azure Logic Apps för systemintegration och EAI-lösningar (Enterprise Application Integration)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: c2a26f2f40e0312fbfa0962e69ddfd0cfdabad5f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191285"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Snabbstart: Skapa ditt första arbetsflöde med hjälp av Azure Logic Apps - Azure portal

Den här snabbstarten introducerar de grundläggande allmänna begreppen bakom hur du skapar ditt första arbetsflöde med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md), till exempel att skapa en tom logikapp, lägga till en utlösare och en åtgärd och sedan testa logikappen. I den här snabbstarten skapar du en logikapp som regelbundet kontrollerar en webbplats RSS-flöde efter nya objekt. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Exempel på logikapparbetsflöde på hög nivå](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

I det här scenariot behöver du ett e-postkonto från en tjänst som stöds av Azure Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra e-posttjänster som stöds [läser du listan över kopplingar här](https://docs.microsoft.com/connectors/). I det här exemplet använder logikappen Office 365 Outlook. Om du använder en annan e-posttjänst är de allmänna stegen de allmänna stegen desamma, men användargränssnittet kan skilja sig något åt.

Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Sök efter och välj **Logic Apps**på Startsidan för Azure.

   ![Sök efter och välj "Logikappar"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. På sidan **Logikappar** väljer du **Lägg till**.

   ![Lägga till ny logikapp](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. I fönstret **Logikapp** anger du information om logikappen som visas nedan. När du är klar väljer du **Skapa**.

   ![Ange information om ny logikapp](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*logik-app-namn*> | Logikappnamnet, som bara kan innehålla bokstäver, siffror, bindestreck`-`( ), understreck (`.``_`), parenteser (`(`och `)`punkter ( ). I det här exemplet används "My-First-Logic-App". |
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Ditt Azure-prenumerationsnamn |
   | **Resursgrupp** | <*Azure-resource-group-namn*> | Namnet på [Azure-resursgruppen](../azure-resource-manager/management/overview.md) som används för att ordna relaterade resurser. I det här exemplet används "My-First-LA-RG". |
   | **Location** | <*Azure-region*> | Regionen där du vill lagra din logikappinformation. I det här exemplet används "Västra USA". |
   | **Logga Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app väljer du **Meddelanden** > **gå till resurs** för din distribuerade logikapp i Verktygsfältet i Azure.

   ![Gå till nyskapade logikappresurs](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Du kan också söka efter och välja logikappen genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj tom mall för logikapp](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg därefter till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt RSS-flödesobjekt kommer. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps-motorn en logikappinstans som startar och kör arbetsflödet.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Lägg till RSS-utlösaren

1. Välj **Alla**under sökrutan i **logikappdesignern**.

1. I sökrutan anger `rss` du för att hitta RSS-kontakten. Välj utlösaren **När ett flödesobjekt publiceras i** listan utlösare.

   ![Välj utlösare för "När ett flödesobjekt publiceras"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Ange den här informationen för utlösaren enligt beskrivningen och beskrivningen här:

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **RSS-feedens webbadress** | `http://feeds.reuters.com/reuters/topNews` | Länken till det RSS-flöde som du vill övervaka |
   | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller  |
   ||||

   Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. Logikappen kontrollerar feeden varje minut.

1. Om du vill dölja utlösarinformationen för tillfället klickar du i utlösarens namnlist.

   ![Dölj logikappform för att dölja detaljer](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="add-the-send-email-action"></a>Lägg till åtgärden "skicka e-post"

Lägg nu till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande när ett nytt objekt visas i RSS-flödet.

1. Under **utlösaren När ett flödesobjekt publiceras** väljer du **Nytt steg**.

   ![Under utlösaren väljer du "Nytt steg"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Under **Välj en åtgärd** och sökrutan väljer du **Alla**.

1. I sökrutan anger `send an email` du för att hitta kopplingar som erbjuder den här åtgärden. I åtgärdslistan väljer du åtgärden "Skicka ett e-postmeddelande" för den e-posttjänst som du vill använda. I det här exemplet används Office 365 Outlook-anslutningen, som har åtgärden **Skicka ett e-postmeddelande.**

   ![Välj åtgärden Skicka ett e-postmeddelande för Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Om du vill filtrera åtgärdslistan till ett specifikt program eller en tjänst kan du välja det programmet eller den tjänsten först:

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om den valda e-postappen uppmanar dig att autentisera din identitet slutför du det steget nu för att skapa en anslutning mellan logikappen och din e-posttjänst.

   > [!NOTE]
   > I det här specifika exemplet autentiserar du din identitet manuellt. Kopplingar som kräver autentisering skiljer sig dock åt i de autentiseringstyper som de stöder. Du har också alternativ för att ställa in hur du vill hantera autentisering. När du till exempel använder Azure Resource Manager-mallar för distribution kan du parameterisera och förbättra säkerheten för indata som du vill ändra ofta eller enkelt, till exempel anslutningsinformation. Mer information finns i de här ämnena:
   >
   > * [Mallparametrar för distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autentisera åtkomst med hanterade identiteter](../logic-apps/create-managed-service-identity.md)
   > * [Autentisera anslutningar för distribution av logikapp](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. I åtgärden **Skicka ett e-postmeddelande** anger du de data som du vill att e-postmeddelandet ska innehålla.

   1. Ange mottagarens e-postadress i fältet **Till**. I testsyfte kan du använda din e-postadress.

      För närvarande kan du listan **Lägg till dynamiskt innehåll** som visas. När du klickar i vissa redigeringsrutor blir listan synlig och visar eventuella tillgängliga parametrar från föregående steg som du kan ta med som indata i arbetsflödet.

   1. I rutan **Ämne** anger du den här texten med ett avslutande blanksteg: `New RSS item: `

      ![Ange ditt e-postämne i egenskapen "Ämne"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. På listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik** för att ta med RSS-objektets titel.

      ![Välj egenskapen Feed title i listan med dynamiskt innehåll](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Exempel på e-postämne för tillagd feedtitel](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Om en For each-loop (för varje) visas i designern har du valt en token för en matris, till exempel token **categories-item**. För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. Om du vill ta bort slingan markerar du **ellipserna** (**...**) i loopens namnlist och väljer sedan **Ta bort**.

   1. I **brödtextrutan** anger du texten och väljer dessa token för e-postmeddelandets brödtext. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

      ![Välj egenskaper för e-postkroppsinnehåll](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Flödesrubrik** | Objektets rubrik |
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid |
      | **Flödets primära länk** | Objektets webbadress |
      |||

1. Spara din logikapp.

Testa därefter logikappen.

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta logikappen manuellt väljer du **Kör**i verktygsfältet Designer. Eller vänta tills logikappen har kontrollerat RSS-flödet baserat på ditt angivna schema (varje minut). Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen tills nästa intervall innan kontrollen utförs på nytt. Om du inte får e-post kan du titta i mappen Skräppost.

Här är ett exempel på ett e-postmeddelande som den här logikappen skickar.

![Exempel på e-post som skickas när nytt RSS-feedobjekt visas](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tekniskt sett när utlösaren kontrollerar RSS-feeden och hittar nya objekt, utlöser utlösaren och Azure Logic Apps-motorn skapar en instans av logikapparbetsflödet som kör åtgärderna i arbetsflödet. Om utlösaren inte hittar nya objekt utlöses den inte, och den ”hoppar över” att instansiera arbetsflödet.

Grattis, du har nu byggt och kört din första logikapp med Azure-portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem.

1. På huvudmenyn i Azure väljer du **Resursgrupper**och väljer sedan logikappens resursgrupp. Välj Ta bort **resursgrupp**i **fönstret Översikt** .

   ![Sök, markera och ta bort resursgrupp](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. När bekräftelsefönstret visas anger du resursgruppens namn och väljer **Ta bort**.

   ![Om du vill bekräfta borttagningen väljer du "Ta bort"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du först en logikapp som söker efter RSS-uppdateringar baserat på ditt angivna schema (varje minut) och vidtar åtgärder (skickar e-post) när det finns uppdateringar. Om du vill lära dig mer fortsätter du med den här självstudien där du får skapa mer avancerade schemabaserade arbetsflöden:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
