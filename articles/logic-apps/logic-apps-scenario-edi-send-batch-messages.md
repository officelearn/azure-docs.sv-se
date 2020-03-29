---
title: Batchprocess EDI-meddelanden som en grupp
description: Skicka och ta emot EDI-meddelanden som batchar, grupper eller samlingar med hjälp av batchbearbetning i Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666677"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Utbyta EDI-meddelanden som batchar eller grupper mellan handelspartner i Azure Logic Apps

I B2B-scenarier (Business to business) utbyter partner ofta meddelanden i grupper eller *batchar.* När du skapar en batchlösning med Logic Apps kan du skicka meddelanden till handelspartner och bearbeta dessa meddelanden tillsammans i batchar. Den här artikeln visar hur du kan batchprocess EDI-meddelanden, med X12 som ett exempel, genom att skapa en "batch avsändare" logikapp och en "batch mottagare" logikapp. 

Batchbearbetning av X12-meddelanden fungerar som att batcha andra meddelanden. Du använder en batchutlösare som samlar in meddelanden i en batch och en batchåtgärd som skickar meddelanden till batchen. Dessutom innehåller X12-batchbearbetning ett X12-kodningssteg innan meddelandena går till handelspartnern eller någon annan destination. Mer information om batchutlösaren och åtgärden finns i [Batch-processmeddelanden](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

I den här artikeln ska du skapa en batchlösning genom att skapa två logikappar inom samma Azure-prenumeration, Azure-region och följa den här specifika ordningen:

* En [logikapp för "batchmottagare"](#receiver) som accepterar och samlar in meddelanden i en batch tills de angivna villkoren är uppfyllda för att släppa och bearbeta dessa meddelanden. I det här fallet kodar batchmottagaren också meddelandena i batchen med hjälp av det angivna X12-avtalet eller partneridentiteterna.

  Se till att du först skapar batchmottagaren så att du senare kan välja batchmålet när du skapar batchavsändaren.

* En [logikapp för "batchavsändare"](#sender) som skickar meddelandena till den tidigare skapade batchmottagaren. 

Kontrollera att batchmottagaren och batchavsändaren delar samma Azure-prenumeration *och* Azure-region. Om de inte gör det kan du inte välja batchmottagaren när du skapar batchavsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Krav

För att följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ett befintligt [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är kopplat till din Azure-prenumeration och som är länkat till dina logikappar

* Minst två befintliga [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) i ditt integrationskonto. Varje partner måste använda kvalificeringen X12 (Standard Carrier Alpha Code) som en affärsidentitet i partnerns egenskaper.

* Ett befintligt [X12-avtal](../logic-apps/logic-apps-enterprise-integration-x12.md) i ditt integrationskonto

* Om du vill använda Visual Studio i stället för Azure-portalen kontrollerar du att du [konfigurerar Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Skapa X12-batchmottagare

Innan du kan skicka meddelanden till en batch måste den batchen först finnas som det mål där du skickar dessa meddelanden. Så först måste du skapa logikappen "batch receiver", som börjar med **batchutlösaren.** På så sätt kan du välja logikappen för batchmottagare när du skapar logikappen "batchavsändare". Batchmottagaren fortsätter att samla in meddelanden tills de angivna villkoren är uppfyllda för att släppa och bearbeta dessa meddelanden. Även batch mottagare behöver inte veta något om batch avsändare måste känna till målet där de skickar meddelanden. 

För den här batchmottagaren anger du batchläge, namn, utgivningsvillkor, X12-avtal och andra inställningar. 

1. Skapa en logikapp med det här namnet i [Azure-portalen](https://portal.azure.com) eller Visual Studio: "BatchX12Messages"

2. [Länka logikappen till ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. I Logic Apps Designer lägger du till **batchutlösaren,** som startar arbetsflödet för logikappen. Ange "batch" som filter i sökrutan. Välj den här **utlösaren: Batch-meddelanden**

   ![Lägg till batchutlösare](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Ange egenskaperna för batchmottagaren: 

   | Egenskap | Värde | Anteckningar | 
   |----------|-------|-------|
   | **Batchläge** | Infogad |  |  
   | **Batchnamn** | TestBatch (testbatch) | Endast tillgängligt med **infogat** batchläge | 
   | **Villkor för utsättning** | Baserat på meddelandeantal, Schemabaserad | Endast tillgängligt med **infogat** batchläge | 
   | **Antal meddelanden** | 10 | Endast tillgängligt med **publiceringsvillkor för meddelanden** | 
   | **Intervall** | 10 | Endast tillgängligt med **schemabaserade** utgivningsvillkor | 
   | **Frekvens** | minut | Endast tillgängligt med **schemabaserade** utgivningsvillkor | 
   ||| 

   ![Ange information om batchutlösare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Det här exemplet konfigurerar inte en partition för batchen, så varje batch använder samma partitionsnyckel. Mer information om partitioner finns i [Batch-processmeddelanden](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Lägg nu till en åtgärd som kodar varje batch: 

   1. Välj **Nytt steg**under batchutlösaren .

   2. I sökrutan anger du "X12 batch" som filter och väljer den här åtgärden (valfri version): **Batch koda <*version*> - X12** 

      ![Välj X12 Batch-kodningsåtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Om du inte tidigare har anslutit till ditt integrationskonto skapar du anslutningen nu. Ange ett namn för anslutningen, välj önskat integrationskonto och välj sedan **Skapa**.

      ![Skapa anslutning mellan batchkodare och integrationskonto](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Ange dessa egenskaper för batchkodaråtgärden:

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Namn på X12-avtalet** | Öppna listan och välj ditt befintliga avtal. <p>Om listan är tom kontrollerar du att du [länkar logikappen till det integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) som har det avtal du vill ha. | 
      | **Batchnamn** | Klicka i den här rutan och när listan med dynamiskt innehåll visas väljer du token **För batchnamn.** | 
      | **PartitionName (PartitionName)** | Klicka i den här rutan och när listan med dynamiskt innehåll visas väljer du token **för partitionsnamn.** | 
      | **Objekt** | Stäng rutan objektinformation och klicka sedan i den här rutan. När listan med dynamiskt innehåll har visas väljer du token **Batched Items.** | 
      ||| 

      ![Åtgärdsinformation för batchkod](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      För rutan **Artiklar:**

      ![Åtgärdsobjekt för batchkod](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Spara din logikapp. 

7. Om du använder Visual Studio kontrollerar du att du [distribuerar logikappen för batchmottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Annars kan du inte välja batchmottagaren när du skapar batchavsändaren.

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill vara säker på att batchmottagaren fungerar som förväntat kan du lägga till en HTTP-åtgärd för testning och skicka ett batchat meddelande till [tjänsten Begäran lagerplats](https://requestbin.com/). 

1. Välj Nytt **steg**under åtgärden X12-kod . 

2. Skriv "http" som filter i sökrutan. Markera den här åtgärden: **HTTP - HTTP**
    
   ![Välj HTTP-åtgärd](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Ange egenskaper för HTTP-åtgärden:

   | Egenskap | Beskrivning | 
   |----------|-------------|
   | **Metod** | Välj **POST**i den här listan . | 
   | **Uri** | Generera en URI för din begäran bin och ange sedan att URI i den här rutan. | 
   | **Brödtext** | Klicka i den här rutan och när listan med dynamiskt innehåll har öppnats väljer du **brödtexttoken,** som visas i avsnittet **Batch-kod efter avtalsnamn**. <p>Om du inte ser **body-token** väljer du **Visa mer** **bredvid Batchkod efter avtalsnamn**. | 
   ||| 

   ![Ange information om HTTP-åtgärder](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Spara din logikapp. 

   Logikappen för batchmottagare ser ut så här: 

   ![Spara logikappen för batchmottagare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Skapa batchavsändare för X12

Skapa nu en eller flera logikappar som skickar meddelanden till logikappen för batchmottagaren. I varje batchavsändare anger du logikappen för batchmottagare och batchnamn, meddelandeinnehåll och andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp batchen i delmängder för att samla in meddelanden med den nyckeln. 

* Kontrollera att du redan har [skapat batchmottagaren](#receiver) så att du kan välja den befintliga batchmottagaren som målbatch när du skapar batchavsändaren. Även batch mottagare behöver inte veta något om batch avsändare måste veta var du ska skicka meddelanden. 

* Kontrollera att batchmottagaren och batchavsändaren delar samma Azure-region *och* Azure-prenumeration. Om de inte gör det kan du inte välja batchmottagaren när du skapar batchavsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan logikapp med det här namnet: "SendX12MessagesToBatch" 

2. Skriv "när en http-begäran" som filter i sökrutan. Välj den här utlösaren: **När en HTTP-begäran tas emot** 
   
   ![Lägga till utlösaren för begäran](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Lägg till en åtgärd för att skicka meddelanden till en batch.

   1. Välj **Nytt steg**under åtgärden HTTP-begäran .

   2. Ange "batch" som filter i sökrutan. 
   Markera listan **Åtgärder** och välj sedan den här åtgärden: **Välj ett Logic Apps-arbetsflöde med batchutlösare - Skicka meddelanden till batch**

      ![Välj "Välj ett Logic Apps-arbetsflöde med batchutlösare"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Välj nu logikappen "BatchX12Messages" som du tidigare har skapat.

      ![Välj logikapp för "batchmottagare"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Välj den här åtgärden: **Batch_messages - <din *batch-mottagare* > **

      ![Välj åtgärden "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Ange batchavsändarens egenskaper.

   | Egenskap | Beskrivning | 
   |----------|-------------| 
   | **Batchnamn** | Batchnamnet som definieras av mottagarens logikapp, som är "TestBatch" i det här exemplet <p>**Viktigt**: Batchnamnet valideras vid körning och måste matcha det namn som anges av mottagarens logikapp. Om du ändrar batchnamnet misslyckas batchavsändaren. | 
   | **Meddelandeinnehåll** | Innehållet för meddelandet som du vill skicka, vilket är **body-token** i det här exemplet | 
   ||| 
   
   ![Ange batchegenskaper](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Spara din logikapp. 

   Logikappen för batchavsändare ser ut så här:

   ![Spara logikappen för batchavsändare](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logikappar

Om du vill testa batchlösningen skickar du X12-meddelanden till logikappen för batchavsändaren från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Snart börjar du få X12-meddelanden i din begäran bin, antingen var 10 minuter eller i omgångar om 10, alla med samma partitionsnyckel.

## <a name="next-steps"></a>Nästa steg

* [Bearbeta meddelanden som batchar](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
