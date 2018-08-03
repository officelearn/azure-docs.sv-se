---
title: Batch bearbetar EDI-meddelanden som en grupp eller samling – Azure Logic Apps | Microsoft Docs
description: Skicka EDI-meddelanden för satsvis bearbetning i logic apps
keywords: batch-och batch-bearbeta, batchkoda
author: divswa
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432141"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Skicka X12 meddelanden i batch till handelspartner

Scenarier för företag (B2B) utbyta partner ofta meddelanden i grupper eller batchar. För att skicka meddelanden i grupper eller batchar för handelspartners, kan du skapa en batch med flera objekt och sedan använda X12 batch åtgärd att bearbeta de objekt som en batch.


Batchbearbetning för X12 meddelanden som andra meddelanden, använder en batchutlösare och åtgärd. Även för X12, batch går igenom en X12 koda steg innan du fortsätter till partnern eller annat mål. Läs mer om batchutlösare och åtgärd [batchprocess](logic-apps-batch-process-send-receive-messages.md).

Det här avsnittet visar hur du kan bearbeta X12 meddelanden som en batch genom att utföra dessa uppgifter:
* [Skapa en logikapp som tar emot objekt och skapar en batch](#receiver). Den här logikappen ”mottagare” utför dessa åtgärder:
 
   * Anger batch namn och version kriterier att uppfylla innan du publicerar objekt som en batch.

   * Processer eller kodar objekten i batch med hjälp av den angivna X12 avtal eller partner identiteter.

* [Skapa en logikapp som skickar objekt till en batch](#sender). Den här logikappen ”avsändaren” anger att skicka objekt för batchbearbetning, vilket måste vara en befintlig mottagare logic app.


## <a name="prerequisites"></a>Förutsättningar

Om du vill följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration

* Minst två [partner](logic-apps-enterprise-integration-partners.md) som du har definierat i ditt integrationskonto. Se till att varje partner använder X12 (Standard Alpha prefix) kvalificerare i egenskaperna för den partner som en företagsidentitet.

* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrationskonto

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Skapa en logic app som tar emot X12 meddelanden och skapar en batch

Innan du kan skicka meddelanden till en batch, måste du först skapa en ”mottagare” logic app med den **Batch** utlösaren. På så sätt kan du kan välja den här logikappen mottagare när du skapar avsändaren logikappen. För mottagaren du ange namnet på batch, versionsvillkor, X12 avtalet och andra inställningar. 


1. I den [Azure-portalen](https://portal.azure.com), skapa en logikapp med detta namn: ”BatchX12Messages”.

1. I Logic Apps Designer, lägger du till den **Batch** utlösaren som startar logikappens arbetsflöde. I sökrutan anger du ”batch” som filter. Välj den här utlösaren: **Batch – batchbearbetning av meddelanden**

   ![Lägg till utlösare av Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. Ange ett namn för gruppen och ange kriterier för att frisläppa batch, till exempel:

   * **Batch-namnet**: namnet som används för att identifiera batch, som är ”TestBatch” i det här exemplet.

   * **Versionsvillkor**: batch-släppningskriterierna som kan baseras på meddelandeantalet, schema eller båda.
   
     ![Ange information för utlösare av Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Antal meddelanden**: antalet meddelanden att lagra som en batch innan du publicerar för bearbetning, vilket är ”5” i det här exemplet.

     ![Ange information för utlösare av Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Schema**: versionsschema batch för bearbetning, vilket är ”var tionde minut” i det här exemplet.

     ![Ange information för utlösare av Batch](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. Lägg till en annan åtgärd som kodar den grupperade eller slår ihop meddelanden och skapar en X12 batchar meddelande. 

   a. Välj **+ nytt steg** > **Lägg till en åtgärd**.

   b. I sökrutan anger du ”X 12 batch” som filter och välj en åtgärd för **X12-Batchkoda**. Som X12 koda connector, det finns flera varianter av batch kodning åtgärd. Du kan välja någon av dessa.

   ![Välj X12 Batchkoda åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. Ange egenskaper för den åtgärd som du just lade till.

   * I den **namnet på X12 avtal** väljer avtalet från den nedrullningsbara listan. Om listan är tom, kontrollerar du att du har skapat en anslutning till ditt integrationskonto.

   * I den **BatchName** väljer den **batchnamn** från listan med dynamiskt innehåll.
   
   * I den **%{PartitionName/** väljer den **partitionsnamnet** från listan med dynamiskt innehåll.

   * I den **objekt** väljer den **Batchbearbetas objekt** från listan med dynamiskt innehåll.

   ![Information om batch koda åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. Lägg till en HTTP-åtgärd för att skicka batch meddelandet till i testsyfte [begär Bin service](https://requestbin.fullcontact.com/). 

   1. Ange ”HTTP” som filter i sökrutan. Välj den här åtgärden: **HTTP - HTTP**
    
      ![Välj HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. Från den **metoden** väljer **POST**. För den **Uri** , generera en URI för din begäran bin och anger denna URI. I den **brödtext** rutan när du öppnar listan med dynamiskt väljer den **brödtext** fältet under den **batchkoda med avtalsnamn** avsnittet. Om du inte ser **brödtext**, Välj **mer** bredvid **batchkoda med avtalsnamn**.

      ![Ange information om HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  Nu när du har skapat en logikapp för mottagaren kan spara din logikapp.

    ![Spara din logikapp](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om villkoren är uppfyllt, kan batch släppas, även när den användardefinierade inte är uppfyllt.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Skapa en logikapp som skickar X12 meddelanden till en batch

Nu ska du skapa en eller flera logikappar som skickar objekt i gruppen som definieras av logikappen mottagare. För avsändaren anger du logikappen mottagare och batchnamn meddelandeinnehåll och eventuella andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp gruppen i underuppsättningar att samla in objekt med nyckeln.

Avsändaren logikappar behöver veta vart du ska skicka objekt, även om mottagaren logikappar inte behöver veta något om avsändare.


1. Skapa en annan logikapp med detta namn: ”X12MessageSender”. Lägg till den här utlösaren i din logikapp: **begäran / svar - begäran** 
   
   ![Lägg till begäran-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. Lägg till ett nytt steg för att skicka meddelanden till en batch.

   1. Välj **+ nytt steg** > **Lägg till en åtgärd**.

   1. I sökrutan anger du ”batch” som filter. 

1. Välj den här åtgärden: **skicka meddelanden till batch – Välj ett Logic Apps-arbetsflöde med en batchutlösare**

   ![Välj ”Skicka meddelanden till batchen”](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. Nu ska du välja ”BatchX12Messages” logikappen som du skapade tidigare, som nu visas som en åtgärd.

   ![Välj ”batch mottagare” logikapp](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > I listan visas också andra logikappar som har batchutlösare.

1. Ange Batchegenskaper.

   * **Batch-namnet**: batchnamn som definierats av logikappen mottagare som är ”TestBatch” i det här exemplet och verifieras vid körning.

     > [!IMPORTANT]
     > Se till att du inte ändrar batch-namn som måste matcha namnet som anges av logikappen mottagare.
     > Ändrar namnet på batch gör att avsändaren logikapp misslyckas.

   * **Meddelandet innehåll**: meddelandeinnehåll som du vill skicka till batchen
   
   ![Ange egenskaper för batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. Spara din logikapp. Logikappen avsändare ser nu ut ungefär så det här exemplet:

   ![Spara logikappen avsändaren](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logikappar

Om du vill testa din batchbearbetningen lösning efter X12 meddelanden till din logikapp för avsändaren från [Postman](https://www.getpostman.com/postman) eller liknande verktyg. Snart, bör du börja hämta X12 meddelanden, antingen som en batch med fem objekt eller var tionde minut i din begäran bin - allt med samma partitionsnyckel.

## <a name="next-steps"></a>Nästa steg

* [Bearbetar meddelanden som batchar](logic-apps-batch-process-send-receive-messages.md) 
* [Skapa en app utan server i Visual Studio med Azure Logic Apps och Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantagshantering och felloggning för logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
