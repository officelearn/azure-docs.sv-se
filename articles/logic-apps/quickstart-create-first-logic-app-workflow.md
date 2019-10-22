---
title: Skapa och automatisera ditt första arbets flöde – Azure Logic Apps
description: Snabb start – skapa din första automatiserade uppgift eller arbets flöde med hjälp av Azure Logic Apps för system integrering och EAI-lösningar (Enterprise Application Integration)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: 8886472b9840c27bcbceb70265379db1682673a9
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679150"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Snabbstart: Skapa ditt första automatiserade arbetsflöde med Azure Logic Apps – Azure Portal

I den här snabbstarten beskrivs hur du bygger ditt första automatiserade arbetsflöde med [Azure Logic Apps](../logic-apps/logic-apps-overview.md). I den här artikeln skapar du en logikapp som regelbundet kontrollerar om det finns nya objekt via en webbplats RRS-feed. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Exempel på högnivås exempel på Logic app-arbetsflöde](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Om du vill följa den här snabbstarten måste du ha ett e-postkonto från en provider som stöds av Logic Apps, som Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). För den här logikappen används ett Office 365 Outlook-konto. Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något.

Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Från huvud menyn i Azure väljer du **skapa en resurs** > **integrations** > **Logic app**.

   ![Skapa logikapp – Azure-portalen](./media/quickstart-create-first-logic-app-workflow/create-new-logic-app.png)

1. Under **Skapa en logikapp** anger du information om din logikapp så som det visas här. När du är klar väljer du **skapa**.

   ![Ange information om den nya Logic-appen](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*logik-app-name*> | Ditt Logic app-namn, som endast får innehålla bokstäver, siffror, bindestreck (`-`), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). I det här exemplet används "min-First-Logic-app". |
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | <*Azure-resurs-grupp-namn* > | Namnet på den [Azure-resurs grupp](../azure-resource-manager/resource-group-overview.md) som används för att organisera relaterade resurser. I det här exemplet används "My-First-LA-RG". |
   | **Plats** | <*Azure-region*> | Den region där du vill lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app går du till Azure-verktygsfältet och väljer **aviseringar**  > **gå till resurs** för din distribuerade Logic-app.

   ![Gå till en nyligen skapad Logic app-resurs](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj tom mall för Logic app](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg därefter till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt RSS-flödesobjekt kommer. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Kontrollera RSS-feed med en utlösare

1. I Logic App Designer väljer du **alla**under sökrutan.

1. Skriv "RSS" i rutan Sök. Välj den här utlösaren i listan utlösare: **när ett feed-objekt publiceras – RSS**

   ![Välj "när ett feed-objekt publiceras" utlösare](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Ange den här informationen för utlösaren enligt vad som visas och beskrivs här:

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **RSS-feedens webbadress** | `http://feeds.reuters.com/reuters/topNews` | Länken till det RSS-flöde som du vill övervaka |
   | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller  |
   ||||

   Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. Logikappen kontrollerar feeden varje minut.

1. Om du vill dölja utlösarinformationen för tillfället klickar du in utlösarens rubriklist.

   ![Dölj Logic app-form för att dölja information](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**.

Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-email-with-an-action"></a>Skicka e-post med en åtgärd

Nu lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar e-postmeddelande när ett nytt objekt visas i RSS-flödet.

1. Under **när ett flödes objekt publiceras** utlösare väljer du **nytt steg**.

   ![Under utlösare väljer du "nytt steg"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Under **Välj en åtgärd** och sökrutan väljer du **alla**.

1. Skriv "Skicka ett e-postmeddelande" i rutan Sök. Välj åtgärden skicka ett e-postmeddelande för den önskade e-postleverantören.

   ![Välj åtgärden "Skicka ett e-postmeddelande" för Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Om du vill filtrera åtgärdslistan till ett specifikt program eller en tjänst kan du välja det programmet eller den tjänsten först:

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.
   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så Logic Apps skapar en anslutning till ditt e-postkonto.

1. I åtgärden **Skicka ett e-postmeddelande** anger du de data som du vill att e-postmeddelandet ska innehålla.

   1. Ange mottagarens e-postadress i fältet **Till**. I testsyfte kan du använda din egen e-postadress.

      För närvarande kan du listan **Lägg till dynamiskt innehåll** som visas. När du klickar i vissa redigeringsrutor blir listan synlig och visar eventuella tillgängliga parametrar från föregående steg som du kan ta med som indata i arbetsflödet.

   1. I rutan **Ämne** anger du den här texten med ett avslutande blanksteg: ```New RSS item:```

      ![I egenskapen "ämne" anger du e-postmeddelandets ämne](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. På listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik** för att ta med RSS-objektets titel.

      ![Från listan med dynamiskt innehåll väljer du egenskapen feed-rubrik](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Det färdiga e-postmeddelandets exempel för tillagd feed-rubrik](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Om en For each-loop (för varje) visas i designern har du valt en token för en matris, till exempel token **categories-item**. För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. Om du vill ta bort loopen väljer du **ellipserna** ( **...** ) på loopens namn list och väljer sedan **ta bort**.

   1. I **brödtextrutan** anger du texten och väljer dessa token för e-postmeddelandets brödtext. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

      ![Välj Egenskaper för innehåll för e-postbrödtext](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Flödesrubrik** | Objektets rubrik |
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid |
      | **Flödets primära länk** | Objektets webbadress |
      |||

1. Spara din logikapp.

Testa därefter logikappen.

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**. Eller vänta tills logikappen har kontrollerat RSS-flödet baserat på ditt angivna schema (varje minut). Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen tills nästa intervall innan kontrollen utförs på nytt. Om du inte får e-post kan du titta i mappen Skräppost.

Här är ett exempel på ett e-postmeddelande som den här logikappen skickar.

![Exempel på e-postmeddelande som skickas när ett nytt RSS-flöde visas](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

När utlösaren kontrollerar RSS-flödet och hittar nya objekt utlöses den och Logic Apps-motorn skapar en instans av ditt logikapparbetsflöde som kör åtgärderna i arbetsflödet. Om utlösaren inte hittar nya objekt utlöses den inte, och den ”hoppar över” att instansiera arbetsflödet.

Grattis, nu har du skapat och kört din första Logic-app med Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem.

1. Välj **resurs grupper**på huvud menyn i Azure och välj sedan din Logic Apps resurs grupp. I **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Hitta, markera och ta bort resurs grupp](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Ange resurs gruppens namn som bekräftelse och välj **ta bort**.

   ![Bekräfta borttagningen genom att välja "ta bort"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du först en logikapp som söker efter RSS-uppdateringar baserat på ditt angivna schema (varje minut) och vidtar åtgärder (skickar e-post) när det finns uppdateringar. Om du vill lära dig mer fortsätter du med den här självstudien där du får skapa mer avancerade schemabaserade arbetsflöden:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
