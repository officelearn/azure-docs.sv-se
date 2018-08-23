---
title: Batch bearbetar EDI-meddelanden som en grupp eller samling – Azure Logic Apps | Microsoft Docs
description: Skicka EDI-meddelanden för satsvis bearbetning i logic apps
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.topic: article
ms.date: 08/19/2018
ms.reviewer: estfan, LADocs
ms.openlocfilehash: 77965e20e7d42d12b34bcb2f7cc6c8680ba34b3a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056225"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Skicka EDI-meddelanden i batchar till handelspartner med Azure Logic Apps

I scenarier för företag (B2B), partner ofta utbyta meddelanden i grupper eller *batchar*. När du skapar en batchbearbetningen lösning med Logic Apps kan du skicka meddelanden till affärspartner och bearbeta meddelandena tillsammans i batchar. Den här artikeln visar hur du kan batch bearbetar EDI-meddelanden med hjälp av X12 kan t.ex, genom att skapa en logikapp för ”batch avsändare” och en ”batch mottagare” logikapp. 

Batchbearbetning X12 meddelanden fungerar som batchbearbetning andra meddelanden. du använder en batchutlösare som samlar in meddelanden i en batch- och en batch-åtgärd som skickar meddelanden till batchen. Dessutom X12 batchbearbetning innehåller en X12 kodning steg innan meddelandena skickas till handelspartner eller annat mål. Läs mer om batchutlösare och åtgärder i [batchprocess](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

I den här artikeln ska du skapa en batchbearbetningen lösning genom att skapa två logikappar i samma Azure-prenumeration, Azure-region och följande den här ordningen:

* En [”batch mottagare”](#receiver) logikapp som accepterar och samlar in meddelanden i en batch tills villkor har uppfyllts för lanserar och bearbetar dessa meddelanden. I det här scenariot batch mottagaren också kodar meddelanden i batchen med hjälp av den angivna X12 avtal eller partner identiteter.

  Kontrollera att du först skapa batch-mottagaren så du kan senare välja batch målet när du skapar batch-avsändaren.

* En [”batch avsändare”](#sender) logikapp som skickar meddelanden till den tidigare skapade batch-mottagaren. 

Kontrollera att din batch-mottagare och avsändare för batch delar samma Azure-prenumeration *och* Azure-region. Om inte, kan inte du välja batch-mottagare när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller, [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* En befintlig [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och länkas till dina logikappar

* Minst två befintliga [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) i ditt integrationskonto. Varje partner måste använda X12 (Standard Alpha prefix) kvalificerare som en företagsidentitet i egenskaperna för den partner.

* En befintlig [X12 avtal](../logic-apps/logic-apps-enterprise-integration-x12.md) i ditt integrationskonto

* Om du vill använda Visual Studio i stället för Azure-portalen, se till att du [ställa in Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Skapa X12 batch mottagare

Innan du kan skicka meddelanden till en batch, måste den batch först finnas som målet dit du skickar dessa meddelanden. Så först måste du skapa logikapp ”batch mottagare”, som börjar med den **Batch** utlösaren. På så sätt kan när du skapar logikapp ”batch avsändare” du kan välja logikapp för batch-mottagare. Batch-mottagaren fortsätter att samla in meddelanden tills villkor uppfylls för att lansera och bearbetar dessa meddelanden. Även om batch-mottagare inte behöver veta något om batch avsändare, måste batch avsändare veta mål där de kan skicka meddelanden. 

För det här batch-mottagare, anger du batch-läge, namn, version kriterier X12 avtalet och andra inställningar. 

1. I den [Azure-portalen](https://portal.azure.com) eller Visual Studio, skapa en logikapp med detta namn: ”BatchX12Messages”

2. [Länka din logikapp till ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. I Logic Apps Designer, lägger du till den **Batch** utlösaren som startar logikappens arbetsflöde. I sökrutan anger du ”batch” som filter. Välj den här utlösaren: **Batch-meddelanden**

   ![Lägg till utlösare av Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ställ in batchen mottagare egenskaper: 

   | Egenskap  | Värde | Anteckningar | 
   |----------|-------|-------|
   | **Batch-läge** | Infogad |  |  
   | **Batch-namn** | TestBatch | Endast tillgängligt med **infogade** batch-läge | 
   | **Versionsvillkor** | Baserat på antal meddelanden, schema baserat | Endast tillgängligt med **infogade** batch-läge | 
   | **Antal meddelanden** | 10 | Endast tillgängligt med **meddelande baserat på antal** versionsvillkor | 
   | **Intervall** | 10 | Endast tillgängligt med **Schemabaserat** versionsvillkor | 
   | **Frekvens** | minut | Endast tillgängligt med **Schemabaserat** versionsvillkor | 
   ||| 

   ![Ange information för utlösare av batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Det här exemplet ställa inte in en partition för batch, så varje batch använder samma partitionsnyckel. Läs mer om partitioner i [batchprocess](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Lägg nu till en åtgärd som kodar varje batch: 

   1. Under batch-utlösaren väljer **nytt steg**.

   2. I sökrutan anger du ”X 12 batch” som filter och välj den här åtgärden (alla versioner): **batchkoda <*version*>-X12** 

      ![Välj X12 Batchkoda åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Om du inte tidigare ansluter till ditt integrationskonto kan du skapa anslutningen nu. Ange ett namn för anslutningen, Välj integrationskontot och sedan välja **skapa**.

      ![Skapa anslutning mellan batch-konto för kodare och integrering](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ange dessa egenskaper för batch-kodare åtgärden:

      | Egenskap  | Beskrivning |
      |----------|-------------|
      | **Namnet på X12 avtal** | Öppna listan och välj din befintliga avtal. <p>Om listan är tom, se till att du [länka din logikapp till integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) som har det avtal som du vill. | 
      | **BatchName** | Klicka i den här rutan och när den dynamiska innehållslistan visas, väljer du den **batchnamn** token. | 
      | **Partitionsnamn** | Klicka i den här rutan och när den dynamiska innehållslistan visas, väljer du den **partitionsnamnet** token. | 
      | **objekt** | Stäng rutan objekt information och klickar sedan i den här rutan. När den dynamiska innehållslistan visas, väljer du den **Batchbearbetas objekt** token. | 
      ||| 

      ![Batch-koda åtgärdsinformation](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      För den **objekt** box:

      ![Batch-koda åtgärdsobjekt](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Spara din logikapp. 

7. Om du använder Visual Studio, se till att du [distribuera logikappen batch mottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Du kan inte annars väljer du batch-mottagare när du skapar batch-avsändaren.

### <a name="test-your-logic-app"></a>Testa din logikapp

För att säkerställa din batch mottagare fungerar som förväntat, kan du lägga till en HTTP-åtgärd för testning och skicka gruppbaserade meddelanden till den [begär Bin service](https://requestbin.fullcontact.com/). 

1. Under X12 koda åtgärd, Välj **nytt steg**. 

2. I sökrutan anger du ”http” som filter. Välj den här åtgärden: **HTTP - HTTP**
    
   ![Välj HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ange egenskaper för HTTP-åtgärd:

   | Egenskap  | Beskrivning | 
   |----------|-------------|
   | **Metoden** | Välj den här listan **POST**. | 
   | **URI: N** | Generera en URI för din begäran bin och sedan ange denna URI i den här rutan. | 
   | **Brödtext** | Klicka i den här rutan och när det öppnas den dynamiska innehållslistan, väljer du den **brödtext** token, som visas i avsnittet **batchkoda med avtalsnamn**. <p>Om du inte ser den **brödtext** token bredvid **batchkoda med avtalsnamn**väljer **mer**. | 
   ||| 

   ![Ange information om HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Spara din logikapp. 

   Logikappen batch mottagare ser ut som i följande exempel: 

   ![Spara din logikapp för batch-mottagare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Skapa X12 batch-avsändare

Nu ska du skapa en eller flera logikappar som skickar meddelanden till logikappen batch mottagare. I varje batch-avsändaren anger du logikapp för batch-mottagare och batchnamn meddelandeinnehåll och eventuella andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp gruppen i underuppsättningar att samla in meddelanden med nyckeln. 

* Kontrollera att du har redan [skapade mottagaren som batch](#receiver) så när du skapar batch-avsändaren, du kan välja befintliga batch mottagaren som mål. Även om batch-mottagare inte behöver veta något om batch avsändare, måste batch avsändare veta var du vill skicka meddelanden. 

* Kontrollera att din batch-mottagare och avsändare för batch delar samma Azure-region *och* Azure-prenumeration. Om inte, kan inte du välja batch-mottagare när du skapar batch-avsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan logikapp med detta namn: ”SendX12MessagesToBatch” 

2. Ange ”när en http-begäran” i sökrutan som filter. Välj den här utlösaren: **när en HTTP-begäran tas emot** 
   
   ![Lägg till begäran-utlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Lägg till en åtgärd för att skicka meddelanden till en batch.

   1. Under den HTTP-begärd-åtgärden, väljer **nytt steg**.

   2. I sökrutan anger du ”batch” som filter. 
   Välj den **åtgärder** och sedan välja den här åtgärden: **Välj ett Logic Apps-arbetsflöde med en batchutlösare - skicka meddelanden till batchen**

      ![Välj ”Välj ett Logic Apps-arbetsflöde med en batchutlösare”](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Välj nu logikappen ”BatchX12Messages” som du skapade tidigare.

      ![Välj ”batch mottagare” logikapp](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Välj den här åtgärden: **Batch_messages - <*your-batch-mottagare*>**

      ![Välj ”Batch_messages”-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ange batch avsändarens egenskaper.

   | Egenskap  | Beskrivning | 
   |----------|-------------| 
   | **Batch-namn** | Batchnamn som definierats av logikappen mottagare som är ”TestBatch” i det här exemplet <p>**Viktiga**: batch-namn hämtar verifieras vid körning och måste matcha namnet som angetts av logikappen mottagare. Ändrar namnet på batch gör att avsändaren batch misslyckas. | 
   | **Meddelandeinnehåll** | Innehållet för meddelandet som du vill skicka, vilket är den **brödtext** token i det här exemplet | 
   ||| 
   
   ![Ange egenskaper för batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Spara din logikapp. 

   Logikappen batch avsändare ser ut som i följande exempel:

   ![Spara din logikapp för batch-avsändare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logikappar

Om du vill testa din batchbearbetningen lösning efter X12 meddelanden till din logikapp för batch-avsändaren från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Snart du börja hämta X12 meddelanden i din begäran bin var tionde minut eller i batchar med 10, allt med samma partitionsnyckel.

## <a name="next-steps"></a>Nästa steg

* [Bearbetar meddelanden som batchar](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
