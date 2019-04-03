---
title: Snabbstart – Skapa och automatisera ditt första arbetsflöde – Azure Logic Apps | Microsoft Docs
description: Skapa din första logikapp som automatiserar uppgifter, processer och arbetsflöden med Azure Logic Apps. Skapa logikprogram för systemintegrering och EAI-lösningar (Enterprise Application Integration) för systemet och molntjänsterna
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 577a047fb88208d7e14e8ba06a1b9e7e87c5ef91
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884250"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Snabbstart: Skapa ditt första automatiserade arbetsflöde med Azure Logic Apps – Azure-portalen

I den här snabbstarten beskrivs hur du bygger ditt första automatiserade arbetsflöde med [Azure Logic Apps](../logic-apps/logic-apps-overview.md). I den här artikeln skapar du en logikapp som regelbundet kontrollerar om det finns nya objekt via en webbplats RRS-feed. Om det finns ett nytt objekt skickar logikappen ett e-postmeddelande för vart och ett. När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Översikt – exempel på logikapp](./media/quickstart-create-first-logic-app-workflow/overview.png)

Om du vill följa den här snabbstarten måste du ha ett e-postkonto från en provider som stöds av Logic Apps, som Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/). För den här logikappen används ett Office 365 Outlook-konto. Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-your-logic-app"></a>Skapa en logikapp 

1. Välj **Skapa en resurs** > **Integration** > **Logikapp** på Azure-huvudmenyn.

   ![Skapa en logikapp](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. Under **Skapa en logikapp** anger du information om din logikapp så som det visas här. När du är klar väljer du **Skapa**.

   ![Tillhandahålla information om logikappar](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------| 
   | **Namn** | MyFirstLogicApp | Logikappens namn | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Azure-prenumerationens namn | 
   | **Resursgrupp** | My-First-LA-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som används för att organisera relaterade resurser | 
   | **Plats** | Västra USA | Regionen där logikappens information ska lagras | 
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. | 
   |||| 

3. När Azure har distribuerat din app öppnas Logic Apps Designer och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Lägg därefter till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt RSS-flödesobjekt kommer. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Kontrollera RSS-feed med en utlösare

1. På designern anger du ”rss” i sökrutan. Välj den här utlösaren: **RSS – När ett flödesobjekt publiceras**

   ![Välj utlösare: ”RSS – När ett flödesobjekt publiceras”](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Ange den här informationen för utlösaren som det visas och beskrivs: 

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------| 
   | **RSS-feedens URL** | ```http://feeds.reuters.com/reuters/topNews``` | Länken till det RSS-flöde som du vill övervaka | 
   | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna | 
   | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller  | 
   |||| 

   Tillsammans definierar intervall och frekvens schemat för logikappens utlösare. 
   Logikappen kontrollerar feeden varje minut.

3. Om du vill dölja utlösarinformationen för tillfället klickar du in utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Spara din logikapp. Välj **Spara** i designerverktygsfältet. 

Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="send-email-with-an-action"></a>Skicka e-post med en åtgärd

Nu lägger du till en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) som skickar e-postmeddelande när ett nytt objekt visas i RSS-flödet. 

1. Under utlösaren **När ett flödesobjekt publiceras** väljer du **Nytt steg**.

   ![Lägga till en åtgärd](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. Under **Välj en åtgärd** anger du ”skicka ett e-postmeddelande” i sökrutan. Under sökrutan väljer du **Alla**. Välj åtgärden skicka ett e-postmeddelande för den önskade e-postleverantören. 

   ![Välj den här åtgärden: ”Office 365 Outlook – Skicka ett e-postmeddelande”](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Om du vill filtrera åtgärdslistan till ett specifikt program eller en tjänst kan du välja det programmet eller den tjänsten först:

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com.

3. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så Logic Apps skapar en anslutning till ditt e-postkonto.

4. I åtgärden **Skicka ett e-postmeddelande** anger du de data som du vill att e-postmeddelandet ska innehålla. 

   1. Ange mottagarens e-postadress i fältet **Till**. 
   I testsyfte kan du använda din egen e-postadress.

      För närvarande kan du listan **Lägg till dynamiskt innehåll** som visas. 
      När du klickar i vissa redigeringsrutor blir listan synlig och visar eventuella tillgängliga parametrar från föregående steg som du kan ta med som indata i arbetsflödet. 

   2. I rutan **Ämne** anger du den här texten med ett avslutande blanksteg: ```New RSS item:```

      ![Ange ämne för e-post](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. På listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik** för att ta med RSS-objektets titel.

      ![Lista med dynamiskt innehåll – ”Feed-titel”](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Tillagd flödesrubrik](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Om en For each-loop (för varje) visas i designern har du valt en token för en matris, till exempel token **categories-item**. 
      För sådana typer av token lägger designern automatiskt till den här loopen omkring åtgärden som refererar till token. 
      På så sätt kan din logikappsåtgärd utföra samma åtgärd på varje element i matrisen. 
      Om du vill ta bort loopen väljer du **ellipserna**  (**...**) på loopens namnlist och väljer **Ta bort**.

   4. I **brödtextrutan** anger du texten och väljer dessa token för e-postmeddelandets brödtext. 
   Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta. 

      ![Lägg till innehåll för e-postmeddelandets brödtext](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Egenskap  | Beskrivning | 
      |----------|-------------| 
      | **Flödesrubrik** | Objektets rubrik | 
      | **Flödet publicerat den** | Objektets publiceringsdatum och -tid | 
      | **Primär flödeslänk** | Objektets webbadress | 
      ||| 
   
5. Spara din logikapp.

Testa därefter logikappen.

## <a name="run-your-logic-app"></a>Kör logikappen

Om du vill starta logikappen manuellt väljer du **Kör** i Designer-verktygsfältet. Eller vänta tills logikappen har kontrollerat RSS-flödet baserat på ditt angivna schema (varje minut). Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Annars väntar logikappen tills nästa intervall innan kontrollen utförs på nytt. 

Här är ett exempel på ett e-postmeddelande som den här logikappen skickar. Om du inte får e-post kan du titta i mappen Skräppost.

![E-postmeddelande skickat för nytt RSS-flödesobjekt](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

När utlösaren kontrollerar RSS-flödet och hittar nya objekt utlöses den och Logic Apps-motorn skapar en instans av ditt logikapparbetsflöde som kör åtgärderna i arbetsflödet.
Om utlösaren inte hittar nya objekt utlöses den inte, och den ”hoppar över” att instansiera arbetsflödet.

Grattis! Nu har du skapar och kört din första logikapp med Azure Portal!

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem. 

1. På Azures huvudmeny öppnar du **Resursgrupper** och väljer logikappens resursgrupp. På sidan **Översikt** väljer du **Ta bort resursgrupp**. 

   ![”Resursgrupper” > ”Översikt” > ”Ta bort resursgrupp”](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. Ange resursgruppens namn som bekräftelse och välj **Ta bort**.

   ![Bekräfta borttagning](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du först en logikapp som söker efter RSS-uppdateringar baserat på ditt angivna schema (varje minut) och vidtar åtgärder (skickar e-post) när det finns uppdateringar. Om du vill lära dig mer fortsätter du med den här självstudien där du får skapa mer avancerade schemabaserade arbetsflöden:

> [!div class="nextstepaction"]
> [Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
