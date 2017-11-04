---
title: Batch-bearbeta meddelanden som en grupp eller samling - Azure Logic Apps | Microsoft Docs
description: "Skicka och ta emot meddelanden för batchbearbetning i logikappar"
keywords: batch batchprocess
author: jonfancey
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
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: e0b7292f25a145c699dbafaf4e31e3f9d072b957
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2017
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Skicka, ta emot och batch-bearbeta meddelanden i logikappar

Om du vill bearbeta meddelanden tillsammans i grupper du kan skicka dataobjekt eller meddelanden, till en *batch*, och sedan bearbeta de objekt som en batch. Den här metoden är användbar när du vill kontrollera dataobjekt grupperas på ett visst sätt och behandlas tillsammans. 

Du kan skapa logikappar som tar emot objekt som en batch med hjälp av den **Batch** utlösare. Du kan sedan skapa logikappar som skickar objekt till en batch med hjälp av den **Batch** åtgärd.

Det här avsnittet visar hur du kan skapa en lösning för batching genom att utföra dessa uppgifter: 

* [Skapa en logikapp som tar emot och samlar in objekt som en batch](#batch-receiver). Den här ”batch mottagare” logikapp anger batch namn och version villkor att uppfylla innan logikappen mottagare släpper och behandlar objekt. 

* [Skapa en logikapp som skickar objekt till en batch](#batch-sender). Den här ”batch avsändaren” logikapp anger var att skicka objekt som måste vara en befintlig logikapp för batch-mottagare. Du kan också ange en unik nyckel som ett kundnummer ”partition” eller dela upp mål batch i delmängder baserat på nyckeln. På så sätt kan alla artiklar med nyckeln samlas in och bearbetas tillsammans. 

## <a name="requirements"></a>Krav

Om du vill följa det här exemplet är vad du behöver:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/logic-apps-create-a-logic-app.md) 

* Ett e-postkonto med alla [e-providern stöds av Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Skapa logikappar som tar emot meddelanden som en batch

Innan du kan skicka meddelanden till en grupp, måste du först skapa en logikapp för ”batch mottagare” med den **Batch** utlösare. På så sätt kan du välja den här mottagaren logikappen när du skapar avsändaren logikappen. För mottagaren anger du batch-namn, version villkor och andra inställningar. 

Avsändaren logikappar måste veta var att skicka objekt när mottagaren logic apps inte behöver känna till något om avsändare.

1. I den [Azure-portalen](https://portal.azure.com), skapa en logikapp med detta namn: ”BatchReceiver” 

2. Lägg till i Logic Apps Designer i **Batch** utlösaren som startar arbetsflödet logik app. I sökrutan anger du ”batch” som filter. Välj den här utlösaren: **Batch – Batch-meddelanden**

   ![Lägga till Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Ange ett namn för gruppen och ange villkor för att frisläppa batchen, till exempel:

   * **Batch-namnet**: namnet som används för att identifiera gruppen, som är ”TestBatch” i det här exemplet.
   * **Släpp kriterier**: batch release villkor kan baseras på antalet meddelanden, schema eller båda.
   
     ![Ange information för Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Antalet meddelanden**: antal meddelanden till håller som en batch innan du släpper för bearbetning, vilket är ”5” i det här exemplet.

     ![Ange information för Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Schemat**: versionsschema batch för bearbetning, vilket är ”var femte minut” i det här exemplet.

     ![Ange information för Batch-utlösare](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Lägg till en annan åtgärd som skickar ett e-postmeddelande när batch-utlösaren utlöses. Varje gång som gruppen har fem objekt eller dess senaste 5 minuter skickar logikappen ett e-postmeddelande.

   1. Välj under batch-utlösaren **+ nytt steg** > **lägga till en åtgärd**.

   2. I sökrutan anger du ”e-post” som filter.
   Baserat på din e-leverantör, Välj en e-anslutning.
   
      Om du har ett arbets- eller skolkonto konto väljer du exempelvis Office 365 Outlook connector. 
      Om du har en Gmail-konto, Välj den Gmail-anslutningen.

   3. Väljer den här åtgärden för din connector:  **{*e-providern*} – skicka ett e-post **

      Exempel:

      ![Välj ”Skicka ett e-postmeddelande” åtgärd för din e-provider](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Om du inte tidigare skapade en anslutning för e-post-providern, ange din e-post-autentiseringsuppgifter för autentisering när du uppmanas. Lär dig mer om [autentisera dina autentiseringsuppgifter för e-](../logic-apps/logic-apps-create-a-logic-app.md).

6. Ange egenskaper för den åtgärd som du just lagt till.

   * Ange mottagarens e-postadress i fältet **Till**. 
   I testsyfte kan du använda din egen e-postadress.

   * I den **ämne** rutan när den **dynamiskt innehåll** lista visas, väljer du den **partitionsnamnet** fältet.

     ![Välj i listan ”dynamiskt innehåll” ”partitionsnamnet”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Du kan ange en unik partitionsnyckel som dividerar mål batch i logiska grupper där du kan skicka meddelanden i ett senare avsnitt. 
     Varje uppsättning har ett unikt nummer som genereras av avsändaren logikappen. 
     Den här funktionen kan du använda en enda grupp med flera delar och definiera varje uppsättning med det namn som du anger.

   * I den **brödtext** rutan när den **dynamiskt innehåll** lista visas, väljer du den **meddelande-Id** fältet.

     ![Välj för ”text”, ”meddelande-Id”](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Eftersom indata för åtgärden Skicka e-post är en matris med designer lägger automatiskt till en **för varje** Slinga runt den **skickar ett e-** åtgärd. 
     Denna loop utför åtgärden inre på varje objekt i gruppen. 
     Så med batch-utlösare är inställd på fem artiklar får du gång fem e-post varje utlösare utlöses.

7.  Nu när du har skapat en batch mottagaren logikapp spara din logikapp.

    ![Spara din logikapp](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om antingen är uppfyllt, kan batchen frigöras, även när den användardefinierade inte är uppfyllt.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Skapa logikappar som skickar meddelanden till en grupp

Nu ska du skapa en eller flera logikappar som skickar objekt i gruppen som definieras av logikappen mottagare. För avsändaren anger du logikappen mottagare och batch-namn, meddelandeinnehåll och andra inställningar. Alternativt kan du ange en unik partitionsnyckel om du vill dela upp gruppen i delmängder att samla in objekt med nyckeln.

Avsändaren logikappar måste veta var att skicka objekt när mottagaren logic apps inte behöver känna till något om avsändare.

1. Skapa en annan logikapp med detta namn: ”BatchSender”

   1. I sökrutan anger du ”återkommande” som filter. 
   Välj den här utlösaren: **schema - upprepning**

      ![Lägga till ”Schemalägg återkommande”-utlösare](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Ange frekvensen och intervall för att köra avsändaren logikapp varje minut.

      ![Ange frekvensen och intervall för upprepning utlösare](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Lägg till ett nytt steg för att skicka meddelanden till en batch.

   1. Välj under utlösaren återkommande **+ nytt steg** > **lägga till en åtgärd**.

   2. I sökrutan anger du ”batch” som filter. 

   3. Välj den här åtgärden: **skicka meddelanden till batch – Välj ett arbetsflöde för Logic Apps med batch-utlösare**

      ![Välj ”Skicka meddelanden till batch”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Nu välja ”BatchReceiver” logikappen som du skapade tidigare, som nu visas som en åtgärd.

      ![Välj ”batch mottagare” logikapp](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > I listan visas också andra logikappar som har batch-utlösare.

3. Ange Batchegenskaper.

   * **Batch-namnet**: batch-namn som definierats av logikappen mottagare som är ”TestBatch” i det här exemplet och verifieras vid körning.

     > [!IMPORTANT]
     > Kontrollera att du inte ändra namnet, vilket måste matcha namnet som anges av logikappen mottagare.
     > Ändra namnet på batch gör att avsändaren logikapp misslyckas.

   * **Meddelande innehåll**: meddelandeinnehåll som du vill skicka. 
   I det här exemplet lägger du till det här uttrycket som infogar aktuellt datum och tid i meddelandeinnehåll som du skickar till gruppen:

     1. När den **dynamiskt innehåll** lista visas, väljer **uttryck**. 
     2. Anger uttrycket **utcnow()**, och välj **OK**. 

        ![Välj ”uttryck” i ”meddelande innehåll”. Ange ”utcnow()”.](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Nu ställa in en partition för gruppen. I ”BatchReceiver”-åtgärd väljer **visa avancerade alternativ**.

   * **Partitionera namnet**: ett valfritt unikt partitionsnyckel ska användas för att dela mål batch. Lägg till ett uttryck som genererar ett slumptal mellan en och fem i det här exemplet.
   
     1. När den **dynamiskt innehåll** lista visas, väljer **uttryck**.
     2. Ange det här uttrycket: **rand(1,6)**

        ![Skapa en partition för mål-batch](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Detta **SLUMP** funktionen genererar ett tal mellan en och fem. 
        Du är så att dela den här batchen i fem numrerade partitioner, som det här uttrycket anger dynamiskt.

   * **Meddelande-Id**: ett valfritt meddelande-ID och ett GUID som genererats när den är tom. 
   I det här exemplet lämnar du rutan tom.

5. Spara din logikapp. Logikappen avsändaren nu ser ut ungefär så här:

   ![Spara logikappen avsändaren](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logic apps

Lämna dina logic apps kör några minuter för att testa din batching lösning. Du startar snart få e-post i grupper med fem, alla med samma partitionsnyckel.

Logikappen BatchSender körs varje minut, genererar ett slumptal mellan en och fem och använder den här genererat nummer som partitionsnyckel för mål-batch där meddelanden skickas. Varje gång som gruppen har fem objekt med samma partitionsnyckel logikappen BatchReceiver utlöses och skickar e-post för varje meddelande.

> [!IMPORTANT]
> När du är klar testning, se till att du inaktiverar BatchSender logikappen så att stoppa meddelanden och undvika överbelastning av din inkorg.

## <a name="next-steps"></a>Nästa steg

* [Skapa på definitioner för logic appen med hjälp av JSON](../logic-apps/logic-apps-author-definitions.md)
* [Skapa en serverlösa app i Visual Studio med Azure Logikappar och funktioner](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantagshantering och felloggning för logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
