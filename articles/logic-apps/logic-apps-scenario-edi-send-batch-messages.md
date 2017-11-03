---
title: Batch-processen EDI-meddelanden som en grupp eller samling - Azure Logic Apps | Microsoft Docs
description: "Skicka EDI-meddelanden för batchbearbetning i logikappar"
keywords: batch, batch-bearbeta, koda batch
author: divswa
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Skicka X12 meddelanden i batchen till handelspartner

För företag (B2B) scenarier utbyta partners ofta meddelanden i grupper eller batchar. För att skicka meddelanden i grupper eller batchar för handelspartners, kan du skapa en batch med flera objekt och sedan använda X12 batch att behandla de objekt som en batch.


Batchbearbetning för X12 meddelanden, precis som andra meddelanden, använder en batch trigger och action. Även för X12, batchen genomgår en X12 koda steg innan du fortsätter till partnern eller annat mål. Mer information om batch-utlösare och åtgärder finns [Batch bearbeta meddelanden](logic-apps-batch-process-send-receive-messages.md).

Det här avsnittet beskrivs hur du kan bearbeta X12 meddelanden som en batch genom att utföra dessa uppgifter:
* [Skapa en logikapp som tar emot objekt och skapar en batch](#receiver). Den här ”mottagaren” logikapp utför dessa åtgärder:
 
   * Anger batch namn och version villkor att uppfylla innan du släpper objekten som en batch.

   * Bearbetar eller kodar objekten i gruppen med hjälp av angivna X12 avtal eller partner identiteter.

* [Skapa en logikapp som skickar objekt till en batch](#sender). Den här ”avsändare” logikapp anger var att skicka objekt för batchbearbetning, vilket måste vara en befintlig mottagare logikapp.


## <a name="prerequisites"></a>Krav

Om du vill följa det här exemplet är vad du behöver:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration

* Minst två [partners](logic-apps-enterprise-integration-partners.md) som du har definierat i ditt konto för integrering. Se till att varje partner använder X12 (Standard alfanumeriska prefix) kvalificerare i egenskaperna för den partner som en identitet för företag.

* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt konto för integrering

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Skapa en logik app som tar emot X12 meddelanden och skapar en grupp

Innan du kan skicka meddelanden till en grupp, måste du först skapa en logikapp för ”mottagaren” med den **Batch** utlösare. På så sätt kan du välja den här mottagaren logikappen när du skapar avsändaren logikappen. För mottagaren, ange batch-namn, släpper kriterier, X12 avtalet och andra inställningar. 


1. I den [Azure-portalen](https://portal.azure.com), skapa en logikapp med detta namn: ”BatchX12Messages”.

2. Lägg till i Logic Apps Designer i **Batch** utlösaren som startar arbetsflödet logik app. I sökrutan anger du ”batch” som filter. Välj den här utlösaren: **Batch – Batch-meddelanden**

   ![Lägga till Batch-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Ange ett namn för gruppen och ange villkor för att frisläppa batchen, till exempel:

   * **Batch-namnet**: namnet som används för att identifiera gruppen, som är ”TestBatch” i det här exemplet.

   * **Släpp kriterier**: batch release villkor kan baseras på antalet meddelanden, schema eller båda.
   
     ![Ange information för Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Antalet meddelanden**: antal meddelanden till håller som en batch innan du släpper för bearbetning, vilket är ”5” i det här exemplet.

     ![Ange information för Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Schemat**: versionsschema batch för bearbetning, vilket är ”var tionde minut” i det här exemplet.

     ![Ange information för Batch-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Lägga till en annan åtgärd som kodar den grupperade eller batchar meddelanden och skapar en X12 batchar meddelandet. 

   a. Välj **+ nytt steg** > **lägga till en åtgärd**.

   b. I sökrutan anger du ”X 12 batch” som filter och välj en åtgärd för **X12-Batch koda**. Som X12 koda koppling, finns det flera variationer för batch-kodning åtgärd. Du kan välja någon av dem.

   ![Välj X12 Batch koda åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Ange egenskaper för den åtgärd som du just lagt till.

   * I den **namnet på X12 avtal** väljer avtalet från den nedrullningsbara listan. Kontrollera att du har skapat en anslutning till ditt konto för integrering om listan är tom.

   * I den **BatchName** markerar den **Batch namn** fältet från listan över dynamiskt innehåll.
   
   * I den **Sammanslagningspartitionernas** markerar den **partitionsnamnet** fältet från listan över dynamiskt innehåll.

   * I den **objekt** markerar den **batchar objekt** från listan över dynamiskt innehåll.

   ![Batch koda åtgärdsinformation](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Lägg till en HTTP-åtgärd för att skicka meddelandet gruppbaserad till för testning, [begära Bin tjänsten](https://requestbin.fullcontact.com/). 

   1. Ange ”HTTP” som filter i sökrutan. Välj den här åtgärden: **HTTP - HTTP**
    
      ![Välj HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Från den **metoden** väljer **efter**. För den **Uri** , generera en URI för begäran-bin och anger den URI. I den **brödtext** rutan när dynamisk lista öppnas, Välj den **brödtext** fältet under den **Batch koda med avtalsnamn** avsnittet. Om du inte ser **brödtext**, Välj **se mer** bredvid **Batch koda med avtalsnamn**.

      ![Ange information för HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Nu när du har skapat en mottagare logikapp spara din logikapp.

    ![Spara din logikapp](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om antingen är uppfyllt, kan batchen frigöras, även när den användardefinierade inte är uppfyllt.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Skapa en logikapp som skickar X12 meddelanden till en grupp

Nu ska du skapa en eller flera logikappar som skickar objekt i gruppen som definieras av logikappen mottagare. För avsändaren anger du logikappen mottagare och batch-namn, meddelandeinnehåll och andra inställningar. Alternativt kan du ange en unik partitionsnyckel om du vill dela upp gruppen i delmängder att samla in objekt med nyckeln.

Avsändaren logic apps behöver veta var att skicka objekt när mottagaren logic apps inte behöver känna till något om avsändare.


1. Skapa en annan logikapp med detta namn: ”X12MessageSender”. Lägg till den här utlösaren i din logikapp: **begäran / svar - begäran** 
   
   ![Lägga till begäran-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Lägg till ett nytt steg för att skicka meddelanden till en batch.

   1. Välj **+ nytt steg** > **lägga till en åtgärd**.

   2. I sökrutan anger du ”batch” som filter. 

3. Välj den här åtgärden: **skicka meddelanden till batch – Välj ett arbetsflöde för Logic Apps med batch-utlösare**

   ![Välj ”Skicka meddelanden till batch”](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Nu välja ”BatchX12Messages” logikappen som du skapade tidigare, som nu visas som en åtgärd.

   ![Välj ”batch mottagare” logikapp](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > I listan visas också andra logikappar som har batch-utlösare.

5. Ange Batchegenskaper.

   * **Batch-namnet**: batch-namn som definierats av logikappen mottagare som är ”TestBatch” i det här exemplet och verifieras vid körning.

     > [!IMPORTANT]
     > Kontrollera att du inte ändra namnet, vilket måste matcha namnet som anges av logikappen mottagare.
     > Ändra namnet på batch gör att avsändaren logikapp misslyckas.

   * **Meddelande innehåll**: meddelandeinnehåll som du vill skicka till batch
   
   ![Ange egenskaper för batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Spara din logikapp. Logikappen avsändaren nu ser ut ungefär så här:

   ![Spara logikappen avsändaren](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logic apps

Om du vill testa din lösning för batching post X12 meddelanden till logikappen avsändaren från [Postman](https://www.getpostman.com/postman) eller liknande verktyg. Du bör snart börja få X12 meddelanden, antingen som en batch med fem objekt eller var 10: e minut i din begäran bin - med samma partitionsnyckel.

## <a name="next-steps"></a>Nästa steg

* [Bearbeta meddelanden som batchar](logic-apps-batch-process-send-receive-messages.md) 
* [Skapa en serverlösa app i Visual Studio med Azure Logikappar och funktioner](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantagshantering och felloggning för logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
