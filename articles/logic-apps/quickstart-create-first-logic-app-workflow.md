---
title: Skapa ditt första automatiserade arbets flöde
description: Snabb start – Bygg ditt första automatiserade arbets flöde med hjälp av Azure Logic Apps för system integrering och EAI-lösningar (Enterprise Application Integration)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 2fdb83a61f7eaaefb3fab10af46315893791590c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536291"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Snabb start: skapa ditt första arbets flöde med hjälp av Azure Logic Apps-Azure Portal

I den här snabb starten beskrivs de grundläggande allmänna koncepten bakom hur du skapar ditt första arbets flöde med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md), till exempel skapa en tom Logic-app, lägga till en utlösare och en åtgärd och sedan testa din Logic app. I den här snabb starten skapar du en Logic-app som regelbundet kontrollerar en webbplats RSS-flöde för nya objekt. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Exempel på högnivås exempel på Logic app-arbetsflöde](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

I det här scenariot behöver du en Azure-prenumeration eller [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/), ett e-postkonto från en tjänst som stöds av Azure Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra e-posttjänster som stöds [granskar du kopplings listan här](/connectors/). I det här exemplet använder Logic-appen ett Office 365 Outlook-konto. Om du använder en annan e-posttjänst är de övergripande allmänna stegen desamma, men användar gränssnittet kan skilja sig något.

> [!IMPORTANT]
> Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. På Azures start sida går du till sökrutan och letar upp och väljer **Logic Apps**.

   ![Sök efter och välj "Logic Apps"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. På sidan **Logic Apps** väljer du **Lägg till**.

   ![Lägg till ny Logic-app](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. I fönstret **Logic app** anger du information om din Logi Kap par som visas nedan. När du är klar väljer du **skapa**.

   ![Ange information om den nya Logic-appen](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*Logic – App-Name*> | Ditt Logic app-namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) och punkter ( `.` ). I det här exemplet används "min-First-Logic-app". <p><p>**Obs**: namn för logi Kap par måste vara unika i flera regioner. |
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | <*Azure-resurs-grupp-namn*> | Namnet på den [Azure-resurs grupp](../azure-resource-manager/management/overview.md) som används för att organisera relaterade resurser. I det här exemplet används "My-First-LA-RG". <p><p>**Obs**: namn för resurs grupper måste vara unika i flera regioner. |
   | **Plats** | <*Azure-region*> | Den region där du vill lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app går du till Azure-verktygsfältet och väljer **aviseringar**  >  **gå till resurs** för din distribuerade Logic-app.

   ![Gå till en nyligen skapad Logic app-resurs](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj tom mall för Logic app](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg därefter till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt RSS-flödesobjekt kommer. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps-motorn en Logic App-instans som startar och kör ditt arbets flöde.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Lägg till RSS-utlösaren

1. I **Logic App Designer**väljer du **alla**under sökrutan.

1. I sökrutan anger `rss` du för att hitta RSS-anslutningen. I listan utlösare väljer du alternativet **när ett feed-objekt publiceras som** utlösare.

   ![Välj "när ett feed-objekt publiceras" utlösare](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Ange informationen för utlösaren enligt beskrivningen i tabellen:

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **RSS-feedens webbadress** | <*RSS-feed-URL*> | Länken för det RSS-flöde som du vill övervaka. I det här exemplet används `http://feeds.reuters.com/reuters/topNews` . |
   | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller  |
   ||||

   Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. Logikappen kontrollerar feeden varje minut.

1. Om du vill komprimera utlösnings informationen för tillfället klickar du i Utlösarens namn List.

   ![Dölj Logic app-form för att dölja information](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="add-the-send-email-action"></a>Lägg till åtgärden "skicka e-post"

Lägg nu till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar ett e-postmeddelande när ett nytt objekt visas i RSS-flödet.

1. Under **när ett flödes objekt publiceras** utlösare väljer du **nytt steg**.

   ![Under utlösare väljer du "nytt steg"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Under **Välj en åtgärd** och sökrutan väljer du **alla**.

1. I sökrutan anger `send an email` du för att hitta anslutningar som erbjuder den här åtgärden. I listan åtgärder väljer du åtgärden "Skicka ett e-postmeddelande" för den e-posttjänst som du vill använda. I det här exemplet används Office 365 Outlook Connector som har åtgärden **skicka e-post** .

   ![Välj åtgärden "Skicka ett e-postmeddelande" för Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Om du vill filtrera åtgärdslistan till ett specifikt program eller en tjänst kan du välja det programmet eller den tjänsten först:

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om din valda e-postanslutning kräver att du autentiserar din identitet, slutför du det steget nu för att skapa en anslutning mellan din Logic app och e-posttjänsten.

   > [!NOTE]
   > I det här exemplet kan du autentisera din identitet manuellt. Men kopplingar som kräver autentisering skiljer sig åt i de autentiseringstyper som de stöder. Du har också alternativ för att ställa in hur du vill hantera autentisering. När du till exempel använder Azure Resource Manager mallar för distribution kan du Parameterisera och förbättra säkerheten för indata som du vill ändra ofta eller enkelt, till exempel anslutnings information. Mer information finns i de här ämnena:
   >
   > * [Mallparametrar för distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Auktorisera OAuth-anslutningar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Autentisera åtkomst med hanterade identiteter](../logic-apps/create-managed-service-identity.md)
   > * [Autentisera anslutningar för Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. I åtgärden **Skicka ett e-postmeddelande** anger du de data som du vill att e-postmeddelandet ska innehålla.

   1. Ange mottagarens e-postadress i fältet **Till**. I test syfte kan du använda din e-postadress.

      För närvarande kan du listan **Lägg till dynamiskt innehåll** som visas. När du klickar i vissa redigeringsrutor blir listan synlig och visar eventuella tillgängliga parametrar från föregående steg som du kan ta med som indata i arbetsflödet.

   1. I rutan **Ämne** anger du den här texten med ett avslutande blanksteg: `New RSS item: `

      ![I egenskapen "ämne" anger du e-postmeddelandets ämne](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. På listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik** för att ta med RSS-objektets titel.

      ![Från listan med dynamiskt innehåll väljer du egenskapen feed-rubrik](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Det färdiga e-postmeddelandets exempel för tillagd feed-rubrik](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Om en For each-loop (för varje) visas i designern har du valt en token för en matris, till exempel token **categories-item**. För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. Om du vill ta bort loopen väljer du **ellipserna** (**...**) på loopens namn list och väljer sedan **ta bort**.

   1. I **brödtextrutan** anger du texten och väljer dessa token för e-postmeddelandets brödtext. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

      ![Välj Egenskaper för innehåll för e-postbrödtext](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Flödesrubrik** | Objektets rubrik |
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid |
      | **Flödets primära länk** | Objektets webbadress |
      |||

1. Spara logikappen.

Testa därefter logikappen.

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. Eller vänta tills logikappen har kontrollerat RSS-flödet baserat på ditt angivna schema (varje minut). Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen tills nästa intervall innan kontrollen utförs på nytt. Om du inte får e-post kan du titta i mappen Skräppost.

Här är ett exempel på ett e-postmeddelande som den här logikappen skickar.

![Exempel på e-postmeddelande som skickas när ett nytt RSS-flöde visas](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Tekniskt, när utlösaren kontrollerar RSS-flödet och hittar nya objekt, utlöses utlösaren och Azure Logic Apps motor skapar en instans av ditt Logic app-arbetsflöde som kör åtgärderna i arbets flödet. Om utlösaren inte hittar nya objekt utlöses den inte, och den ”hoppar över” att instansiera arbetsflödet.

Grattis, nu har du skapat och kört din första Logic-app med Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem.

1. Välj **resurs grupper**på huvud menyn i Azure och välj sedan din Logic Apps resurs grupp. I **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Hitta, markera och ta bort resurs grupp](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. När fönstret bekräftelse visas anger du namnet på resurs gruppen och väljer **ta bort**.

   ![Bekräfta borttagningen genom att välja "ta bort"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du först en logikapp som söker efter RSS-uppdateringar baserat på ditt angivna schema (varje minut) och vidtar åtgärder (skickar e-post) när det finns uppdateringar. Om du vill lära dig mer fortsätter du med den här självstudien där du får skapa mer avancerade schemabaserade arbetsflöden:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
