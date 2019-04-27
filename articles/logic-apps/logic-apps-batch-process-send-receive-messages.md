---
title: Batch bearbetar meddelanden som en grupp eller samling – Azure Logic Apps | Microsoft Docs
description: Skicka och ta emot meddelanden som batchar i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: c33b1d46ecf710f050fc998ce27f6448337c6b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683776"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Skicka, ta emot och bearbetar meddelanden i Azure Logic Apps för batch

För att skicka och bearbeta meddelanden tillsammans på ett visst sätt som grupper, kan du skapa en batchbearbetningen lösning som samlar in meddelanden i en *batch* tills villkor är uppfyllda för att lansera och bearbeta batch meddelanden. Batchbearbetning kan minska hur ofta logikappen bearbetar meddelanden. Den här artikeln visar hur du skapar en batchbearbetningen lösning genom att skapa två logikappar i samma Azure-prenumeration, Azure-region och följa den här ordningen: 

* Den [”batch mottagare”](#batch-receiver) logikapp som accepterar och samlar in meddelanden i en batch tills villkor har uppfyllts för lanserar och bearbetar dessa meddelanden.

  Kontrollera att du först skapa batch-mottagaren så du kan senare välja batch målet när du skapar batch-avsändaren.

* En eller flera [”batch avsändare”](#batch-sender) logic apps, som skickar meddelanden till den tidigare skapade batch-mottagaren. 

   Du kan också ange en unik nyckel, till exempel ett kundnummer som *partitioner* eller delar batch mål i logiska delmängder baserat på nyckeln. På så sätt kan kan appen mottagare samla in alla objekt med samma nyckel och bearbeta dem tillsammans.

Kontrollera att din batch-mottagare och avsändare för batch delar samma Azure-prenumeration *och* Azure-region. Om inte, kan inte du välja batch-mottagare när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller, [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Ett e-postkonto med alla [e-postleverantör som stöds av Azure Logic Apps](../connectors/apis-list.md)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Om du vill använda Visual Studio i stället för Azure-portalen, se till att du [ställa in Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Skapa batch-mottagare

Innan du kan skicka meddelanden till en batch, måste den batch först finnas som målet dit du skickar dessa meddelanden. Så först måste du skapa logikapp ”batch mottagare”, som börjar med den **Batch** utlösaren. På så sätt kan när du skapar logikapp ”batch avsändare” du kan välja logikapp för batch-mottagare. Batch-mottagaren fortsätter att samla in meddelanden tills villkor uppfylls för att lansera och bearbetar dessa meddelanden. Även om batch-mottagare inte behöver veta något om batch avsändare, måste batch avsändare veta mål där de kan skicka meddelanden. 

1. I den [Azure-portalen](https://portal.azure.com) eller Visual Studio, skapa en logikapp med det här namnet: "BatchReceiver" 

2. I Logic Apps Designer, lägger du till den **Batch** utlösaren som startar logikappens arbetsflöde. I sökrutan anger du ”batch” som filter. Välj den här utlösaren: **Batcha meddelanden**

   ![Lägg till ”Batch-meddelanden” utlösare](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Ange dessa egenskaper för batch-mottagare: 

   | Egenskap  | Beskrivning | 
   |----------|-------------|
   | **Batch-läge** | - **Infogad**: För att definiera versionskriterierna i batchutlösare <br>- **Integrationskontot**: För att definiera flera versionen kriterier konfigurationer via en [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Du kan underhålla konfigurationerna alla på samma plats i stället för i separata logic apps med ett integrationskonto. | 
   | **Batch-namn** | Namn för ditt batch, som är ”TestBatch” i det här exemplet och gäller endast **infogade** batch-läge |  
   | **Versionsvillkor** | Gäller endast **infogade** batch-läge och väljer kriterier för att uppfylla innan bearbetning av varje batch: <p>- **Meddelande baserat på antal**: Släpp batch baserat på hur många meddelanden som samlas in av batch. <br>- **Storleksbaserad**: Släpp batch utifrån den totala storleken i byte för alla meddelanden som samlas in av det batch. <br>- **Schema**: Versionen batch baserat på ett upprepningsschema som anger ett intervall och frekvens. I avancerade alternativ klickar du också Välj en tidszon och ange ett startdatum och starttid. <br>- **Markera alla**: Använd de angivna kriterierna. | 
   | **Antal meddelanden** | Antal meddelanden att samla in i batchen, till exempel 10 meddelanden. En batch-gränsen är 8 000 meddelanden. | 
   | **Batch Size** | Den totala storleken i byte för att samla in i batchen, till exempel 10 MB. Gränsen för en batch är 80 MB. | 
   | **Schema** | Intervall och frekvens mellan batch-versioner, till exempel 10 minuter. Minimivärdet för upprepning är 60 sekunder eller 1 minut. Bråkdelar av minuter avrundas effektivt uppåt till 1 minut. Om du vill ange en tidszon eller ett startdatum och starttid, Välj **visa avancerade alternativ**. | 
   ||| 

   > [!NOTE]
   > 
   > Om du ändrar versionskriterierna när utlösaren fortfarande har batchar men meddelanden använder utlösaren uppdaterade versionsvillkor för hantering av meddelanden som inte skickats. 

   Det här exemplet visar alla villkor, men försök bara ett kriterium för dina egna tester:

   ![Ange information för utlösare av Batch](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Nu ska du lägga till en eller flera åtgärder som bearbetar varje batch. 

   I det här exemplet lägger du till en åtgärd som skickar ett e-postmeddelande när batch-utlösaren utlöses. 
   Utlösaren körs och skickar ett e-postmeddelande när batchen antingen har 10 meddelandena når 10 MB eller efter 10 minuter skicka.

   1. Under batch-utlösaren väljer **nytt steg**.

   2. I sökrutan anger du "send email" (skicka e-post) som filter.
   Beroende på din e-postleverantör kan välja en e-post-koppling.

      Om du har ett personligt konto, som till exempel @outlook.com eller @hotmail.com, väljer du anslutningsappen Outlook.com. 
      Om du har en Gmail-konto väljer du Gmail-anslutningsappen. 
      Det här exemplet används Office 365 Outlook. 

   3. Välj den här åtgärden: **Skicka ett e-postmeddelande - <*e-postleverantör*>**

      Exempel:

      ![Välj åtgärden ”Skicka ett e-postmeddelande” för din e-postleverantör](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Logga in på ditt e-postkonto om du uppmanas att göra det. 

6. Ange egenskaper för den åtgärd som du har lagt till.

   * Ange mottagarens e-postadress i fältet **Till**. 
   I testsyfte kan du använda din egen e-postadress.

   * I den **ämne** rutan när den dynamiska innehållslistan visas, Välj den **partitionsnamnet** fält.

     ![Från den dynamiska innehållslistan, väljer du ”partitionsnamnet”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Senare, kan du ange en unik partitionsnyckel som delar batch mål i logiska undergrupper där du kan skicka meddelanden i batch-avsändaren. 
     Varje uppsättning har ett unikt nummer som genereras av batch avsändaren logikappen. 
     Den här funktionen kan du använda en enskild batch med flera delmängder och definiera varje undergrupp med det namn som du anger.

     > [!IMPORTANT]
     > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om villkoren är uppfyllt, kan Logic Apps frigöra batch, även när definierade versionen villkoret inte uppfylls.

   * I den **brödtext** rutan när den dynamiska innehållslistan visas, Välj den **meddelande-Id** fält. 

     Logic Apps Designer lägger automatiskt till en ”för var och en” loopen omkring åtgärden Skicka e-postmeddelandet eftersom åtgärden behandlar utdata från den första åtgärden som en samling i stället för en batch. 

     ![Välj ”meddelande-Id” för ”Body”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Spara din logikapp. Nu har du skapat en batch-mottagare.

    ![Spara din logikapp](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Om du använder Visual Studio, se till att du [distribuera logikappen batch mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Du kan inte annars väljer du batch-mottagare när du skapar batch-avsändaren.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Skapa batch-avsändare

Nu ska du skapa en eller flera batch avsändaren logikappar som skickar meddelanden till logikappen batch mottagare. I varje batch-avsändaren anger du batch-mottagaren och batchnamn meddelandeinnehåll och eventuella andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp gruppen i logiska underuppsättningar för att samla in meddelanden med nyckeln. 

* Kontrollera att du har redan [skapade mottagaren som batch](#batch-receiver) så när du skapar batch-avsändaren, du kan välja befintliga batch mottagaren som mål. Även om batch-mottagare inte behöver veta något om batch avsändare, måste batch avsändare veta var du vill skicka meddelanden. 

* Kontrollera att din batch-mottagare och avsändare för batch delar samma Azure-region *och* Azure-prenumeration. Om inte, kan inte du välja batch-mottagare när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan logic app med det här namnet: "BatchSender"

   1. I sökrutan anger du ”återkommande” som filter. 
   Välj den här utlösaren: **Upprepning - schema**

      ![Lägg till ”--upprepningsschemat”-utlösare](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Anger du frekvens och intervall för att köra avsändaren logikapp varje minut.

      ![Ange frekvens och intervall för utlösare för upprepning](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Lägg till en ny åtgärd för att skicka meddelanden till en batch.

   1. Välj under upprepningsutlösaren, **nytt steg**.

   2. I sökrutan anger du ”batch” som filter. 
   Välj den **åtgärder** och sedan välja den här åtgärden: **Välj ett Logic Apps-arbetsflöde med en batchutlösare - skicka meddelanden till batchen**

      ![Välj ”Välj ett Logic Apps-arbetsflöde med en batchutlösare”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Välj din logikapp för batch-mottagare som du skapade tidigare.

      ![Välj ”batch mottagare” logikapp](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > I listan visas också andra logikappar som har batchutlösare. 
      > 
      > Om du använder Visual Studio, och du inte ser alla batch-mottagare du vill välja, kontrollerar du att du har distribuerat mottagaren som batch till Azure. Om du inte gjort det, lär du dig hur du [distribuera logikappen batch mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Välj den här åtgärden: **Batch_messages - <*your-batch-receiver*>**

      ![Välj den här åtgärden: "Batch_messages - <your-logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Ställ in batchen avsändarens egenskaper:

   | Egenskap  | Beskrivning | 
   |----------|-------------| 
   | **Batch-namn** | Batchnamn som definierats av logikappen mottagare som är ”TestBatch” i det här exemplet <p>**Viktiga**: Batch-namn hämtar verifieras vid körning och måste matcha namnet som angetts av logikappen mottagare. Ändrar namnet på batch gör att avsändaren batch misslyckas. | 
   | **Meddelandeinnehåll** | Innehållet för det meddelande du vill skicka | 
   ||| 

   I det här exemplet lägger du till det här uttrycket som infogar aktuellt datum och tid i meddelandeinnehåll som du skickar till batch:

   1. Klicka i den **meddelandeinnehållet** box. 

   2. När den dynamiska innehållslistan visas väljer du **uttryck**. 

   3. Ange uttrycket `utcnow()`, och välj sedan **OK**. 

      ![Välj ”uttryck” i ”meddelandeinnehåll”, ange ”utcnow()” och välj ”OK”.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Konfigurera nu en partition för batch. I ”BatchReceiver”-åtgärd väljer **visa avancerade alternativ** och ange dessa egenskaper:

   | Egenskap  | Beskrivning | 
   |----------|-------------| 
   | **Partitionsnamn** | En valfri unik partitionsnyckel ska användas för att dela upp batch mål i logiska delmängder och samla in meddelanden baserat på nyckeln | 
   | **Meddelande-Id** | Ett valfritt meddelande-ID som är en genererad globalt unik identifierare (GUID) när den är tom | 
   ||| 

   I det här exemplet i den **partitionsnamnet** lägger du till ett uttryck som genererar ett slumptal mellan en och fem. Lämna den **meddelande-Id** tom.
   
   1. Klicka i den **partitionsnamnet** så att den dynamiska innehållslistan visas. 

   2. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.
   
   3. Ange uttrycket `rand(1,6)`, och välj sedan **OK**.

      ![Konfigurera en partition för mål-batch](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Detta **rand** funktionen genererar ett tal mellan en och fem. 
      Du är så att dela den här batchen i fem numrerade partitioner, som det här uttrycket anger dynamiskt.

5. Spara din logikapp. Logikappen avsändare ser nu ut ungefär så det här exemplet:

   ![Spara logikappen avsändaren](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logikappar

Testa din batchbearbetningen lösning genom att lämna dina logikappar som körs under ett par minuter. Du börjar snart emot e-postmeddelanden i grupper med fem, allt med samma partitionsnyckel.

Logikappen batch avsändaren körs varje minut, genererar ett slumptal mellan en och fem och använder den här genererat nummer som partitionsnyckel för batch-mål där meddelanden skickas. Varje gång som batch har fem objekt med samma partitionsnyckel logikappen batch mottagare utlöses och skickar e-post för varje meddelande.

> [!IMPORTANT]
> När du är klar testning, se till att du inaktiverar BatchSender logic app för att sluta skicka meddelanden och undvika överbelastning av din inkorg.

## <a name="next-steps"></a>Nästa steg

* [Batchbearbeta och skicka EDI-meddelanden](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Skapa logikappsdefinitioner med hjälp av JSON](../logic-apps/logic-apps-author-definitions.md)
* [Skapa en app utan server i Visual Studio med Azure Logic Apps och Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantagshantering och felloggning för logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
