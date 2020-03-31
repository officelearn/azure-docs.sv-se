---
title: Batchprocessmeddelanden som grupp
description: Skicka och ta emot meddelanden i grupper mellan dina arbetsflöden med hjälp av batchbearbetning i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666762"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Skicka, ta emot och batchprocessmeddelanden i Azure Logic Apps

Om du vill skicka och bearbeta meddelanden tillsammans på ett visst sätt som grupper kan du skapa en batchlösning som samlar in meddelanden i en *batch* tills de angivna villkoren är uppfyllda för att släppa och bearbeta de batchade meddelandena. Batchbearbetning kan minska hur ofta logikappen bearbetar meddelanden. Den här artikeln visar hur du skapar en batchlösning genom att skapa två logikappar inom samma Azure-prenumeration, Azure-region och följa den här specifika ordern: 

* Logikappen ["batch receiver"](#batch-receiver) som accepterar och samlar in meddelanden i en batch tills de angivna villkoren är uppfyllda för att släppa och bearbeta dessa meddelanden.

  Se till att du först skapar batchmottagaren så att du senare kan välja batchmålet när du skapar batchavsändaren.

* En eller flera [logikappar för "batchavsändare",](#batch-sender) som skickar meddelandena till den tidigare skapade batchmottagaren. 

   Du kan också ange en unik nyckel, till exempel ett kundnummer, som *partitionerar* eller delar upp målbatchen i logiska delmängder baserat på den nyckeln. På så sätt kan mottagarappen samla in alla objekt med samma nyckel och bearbeta dem tillsammans.

Kontrollera att batchmottagaren och batchavsändaren delar samma Azure-prenumeration *och* Azure-region. Om de inte gör det kan du inte välja batchmottagaren när du skapar batchavsändaren eftersom de inte är synliga för varandra.

## <a name="prerequisites"></a>Krav

För att följa det här exemplet behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Ett e-postkonto med alla [e-postleverantörer som stöds av Azure Logic Apps](../connectors/apis-list.md)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Om du vill använda Visual Studio i stället för Azure-portalen kontrollerar du att du [konfigurerar Visual Studio för att arbeta med Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Skapa batchmottagare

Innan du kan skicka meddelanden till en batch måste den batchen först finnas som det mål där du skickar dessa meddelanden. Så först måste du skapa logikappen "batch receiver", som börjar med **batchutlösaren.** På så sätt kan du välja logikappen för batchmottagare när du skapar logikappen "batchavsändare". Batchmottagaren fortsätter att samla in meddelanden tills de angivna villkoren är uppfyllda för att släppa och bearbeta dessa meddelanden. Även batch mottagare behöver inte veta något om batch avsändare måste känna till målet där de skickar meddelanden. 

1. Skapa en logikapp med det här namnet i [Azure-portalen](https://portal.azure.com) eller Visual Studio: "BatchReceiver" 

2. I Logic Apps Designer lägger du till **batchutlösaren,** som startar arbetsflödet för logikappen. Ange "batch" som filter i sökrutan. Välj den här **utlösaren: Batch-meddelanden**

   ![Lägga till utlösare för batchmeddelanden](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Ange dessa egenskaper för batchmottagaren: 

   | Egenskap | Beskrivning | 
   |----------|-------------|
   | **Batchläge** | - **Infogad**: För att definiera frisläppningskriterier inuti batchutlösaren <br>- **Integrationskonto**: För att definiera konfigurationer för flera utgivningsvillkor via ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Med ett integrationskonto kan du underhålla dessa konfigurationer på ett ställe i stället för i separata logikappar. | 
   | **Batchnamn** | Namnet på din batch, som är "TestBatch" i det här exemplet, och gäller endast **för infogat** batchläge |  
   | **Villkor för utsättning** | Gäller endast **infogat** batchläge och väljer de kriterier som ska uppfyllas innan varje batch bearbetas: <p>- **Baserat på meddelandeantal:** Släpp batchen baserat på antalet meddelanden som samlas in av batchen. <br>- **Storleksbaserad:** Släpp batchen baserat på den totala storleken i byte för alla meddelanden som samlas in av den batchen. <br>- **Schema**: Släpp batchen baserat på ett återkommande schema, som anger ett intervall och en frekvens. I de avancerade alternativen kan du också välja en tidszon och ange ett startdatum och en starttid. <br>- **Markera alla**: Använd alla angivna villkor. | 
   | **Antal meddelanden** | Antalet meddelanden som ska samlas in i batchen, till exempel 10 meddelanden. En batch gräns är 8.000 meddelanden. | 
   | **Batchstorlek** | Den totala storleken i byte som ska samlas in i batchen, till exempel 10 MB. En batch storleksgräns är 80 MB. | 
   | **Schema** | Intervallet och frekvensen mellan batchversioner, till exempel 10 minuter. Minsta upprepning är 60 sekunder eller 1 minut. Fraktionerad minut avrundas effektivt upp till 1 minut. Om du vill ange en tidszon eller ett startdatum och en starttid väljer du **Visa avancerade alternativ**. | 
   ||| 

   > [!NOTE]
   > 
   > Om du ändrar utgivningsvillkoren medan utlösaren fortfarande har batch-skickat men meddelanden som inte har skickats, använder utlösaren de uppdaterade utgivningskriterierna för att hantera de meddelanden som inte har skickats. 

   I det här exemplet visas alla kriterier, men för din egen testning kan du bara prova ett kriterium:

   ![Ange information om batchutlösare](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Lägg nu till en eller flera åtgärder som bearbetar varje batch. 

   I det här exemplet lägger du till en åtgärd som skickar ett e-postmeddelande när batchutlösaren utlöses. 
   Utlösaren körs och skickar ett e-postmeddelande när batchen antingen har 10 meddelanden, når 10 MB eller efter 10 minuters passning.

   1. Välj **Nytt steg**under batchutlösaren .

   2. I sökrutan anger du "send email" (skicka e-post) som filter.
   Välj en e-postanslutning baserat på din e-postleverantör.

      Om du till exempel har ett @outlook.com personligt @hotmail.comkonto, till exempel eller väljer du den Outlook.com kopplingen. 
      Om du har ett Gmail-konto väljer du Gmail-anslutningen. 
      I det här exemplet används Office 365 Outlook. 

   3. Välj den här åtgärden: **Skicka ett e-postmeddelande - <*e-postleverantör* > **

      Ett exempel:

      ![Välj Åtgärden Skicka ett e-postmeddelande för din e-postleverantör](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Logga in på ditt e-postkonto om du uppmanas att göra det. 

6. Ange egenskaper för åtgärden som du har lagt till.

   * Ange mottagarens e-postadress i fältet **Till**. 
   I testsyfte kan du använda din egen e-postadress.

   * Välj fältet **Partitionnamn** när listan med dynamiskt innehåll visas i rutan **Ämne.**

     ![Välj "Partitionnamn" i listan med dynamiskt innehåll](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Senare, i batchavsändaren, kan du ange en unik partitionsnyckel som delar upp målbatchen i logiska delmängder där du kan skicka meddelanden. 
     Varje uppsättning har ett unikt nummer som genereras av logikappen för batchavsändare. 
     Med den här funktionen kan du använda en enskild batch med flera delmängder och definiera varje delmängd med det namn som du anger.

     > [!IMPORTANT]
     > En partition har en gräns på 5 000 meddelanden eller 80 MB. Om något av villkoren uppfylls kan Logic Apps släppa batchen, även när det definierade publiceringsvillkoret inte uppfylls.

   * Markera fältet **Meddelande-ID** när listan Med dynamiskt innehåll visas i rutan **Brödtext.** 

     Logic Apps Designer lägger automatiskt till en "För varje" loop runt skicka e-poståtgärden eftersom den åtgärden behandlar utdata från föregående åtgärd som en samling i stället för en batch. 

     ![För "Brödtext" väljer du "Meddelande-ID"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Spara din logikapp. Du har nu skapat en batchmottagare.

    ![Spara din logikapp](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Om du använder Visual Studio kontrollerar du att du [distribuerar logikappen för batchmottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Annars kan du inte välja batchmottagaren när du skapar batchavsändaren.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Skapa batchavsändare

Nu skapa en eller flera batch avsändare logikappar som skickar meddelanden till batch mottagare logikapp. I varje batchavsändare anger du batchmottagaren och batchnamnet, meddelandeinnehållet och alla andra inställningar. Du kan också ange en unik partitionsnyckel för att dela upp batchen i logiska delmängder för att samla in meddelanden med den nyckeln. 

* Kontrollera att du redan har [skapat batchmottagaren](#batch-receiver) så att du kan välja den befintliga batchmottagaren som målbatch när du skapar batchavsändaren. Även batch mottagare behöver inte veta något om batch avsändare måste veta var du ska skicka meddelanden. 

* Kontrollera att batchmottagaren och batchavsändaren delar samma Azure-region *och* Azure-prenumeration. Om de inte gör det kan du inte välja batchmottagaren när du skapar batchavsändaren eftersom de inte är synliga för varandra.

1. Skapa en annan logikapp med det här namnet: "BatchSender"

   1. Skriv "återkommande" som filter i sökrutan. 
   Välj den här utlösaren: **Återkommande - Schema**

      ![Lägga till utlösaren "Återkommande - schema"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Ställ in frekvensen och intervallet för att köra avsändarens logikapp varje minut.

      ![Ange frekvens och intervall för upprepningsutlösare](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Lägg till en ny åtgärd för att skicka meddelanden till en batch.

   1. Välj **Nytt steg**under upprepningsutlösaren .

   2. Ange "batch" som filter i sökrutan. 
   Markera listan **Åtgärder** och välj sedan den här åtgärden: **Välj ett Logic Apps-arbetsflöde med batchutlösare - Skicka meddelanden till batch**

      ![Välj "Välj ett Logic Apps-arbetsflöde med batchutlösare"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Välj logikappen för batchmottagare som du tidigare har skapat.

      ![Välj logikapp för "batchmottagare"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Listan visar också alla andra logikappar som har batchutlösare. 
      > 
      > Om du använder Visual Studio och du inte ser några batchmottagare att välja kontrollerar du att du har distribuerat batchmottagaren till Azure. Om du inte har gjort det kan du läsa om hur du [distribuerar logikappen för batchmottagare till Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Välj den här åtgärden: **Batch_messages - <din *batch-mottagare* > **

      ![Välj den här åtgärden: "Batch_messages - <din logik-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Ange batchavsändarens egenskaper:

   | Egenskap | Beskrivning | 
   |----------|-------------| 
   | **Batchnamn** | Batchnamnet som definieras av mottagarens logikapp, som är "TestBatch" i det här exemplet <p>**Viktigt**: Batchnamnet valideras vid körning och måste matcha det namn som anges av mottagarens logikapp. Om du ändrar batchnamnet misslyckas batchavsändaren. | 
   | **Meddelandeinnehåll** | Innehållet för meddelandet som du vill skicka | 
   ||| 

   I det här exemplet lägger du till det här uttrycket, som infogar aktuellt datum och aktuell tid i meddelandeinnehållet som du skickar till batchen:

   1. Klicka i rutan **Meddelandeinnehåll.** 

   2. När listan med dynamiskt innehåll visas väljer du **Uttryck**. 

   3. Ange uttrycket `utcnow()`och välj sedan **OK**. 

      ![I "Meddelandeinnehåll" väljer du "Expression", skriver "utcnow()" och väljer "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Nu ställa in en partition för batchen. I åtgärden "BatchReceiver" väljer du **Visa avancerade alternativ** och anger följande egenskaper:

   | Egenskap | Beskrivning | 
   |----------|-------------| 
   | **Partitionsnamn** | En unik partitionsnyckel som tillval används för att dela upp målbatchen i logiska delmängder och samla in meddelanden baserat på den nyckeln | 
   | **Meddelande-ID** | En valfri meddelandeidentifierare som är en genererad globalt unik identifierare (GUID) när den är tom | 
   ||| 

   I det här exemplet lägger du till ett uttryck som genererar ett slumptal mellan ett och fem i rutan **Partitionsnamn.** Lämna rutan **Meddelande-ID** tom.
   
   1. Klicka i rutan **Partitionsnamn** så att listan med dynamiskt innehåll visas. 

   2. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.
   
   3. Ange uttrycket `rand(1,6)`och välj sedan **OK**.

      ![Konfigurera en partition för målbatchen](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Denna **rand** funktion genererar ett nummer mellan ett och fem. 
      Så du delar upp den här batchen i fem numrerade partitioner, som det här uttrycket dynamiskt anger.

5. Spara din logikapp. Logikappen för avsändare ser nu ut ungefär som det här exemplet:

   ![Spara logikappen för avsändaren](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testa dina logikappar

Om du vill testa batchlösningen låter du logikapparna vara igång i några minuter. Snart börjar du få e-post i grupper om fem, alla med samma partitionsnyckel.

Logikappen för batchavsändare körs varje minut, genererar ett slumptal mellan ett och fem och använder det genererade numret som partitionsnyckel för målbatchen där meddelanden skickas. Varje gång batchen har fem objekt med samma partitionsnyckel utlöses logikappen för batchmottagare och skickar e-post för varje meddelande.

> [!IMPORTANT]
> När du är klar med testningen ska du se till att inaktivera logikappen BatchSender för att sluta skicka meddelanden och undvika att överbelasta inkorgen.

## <a name="next-steps"></a>Nästa steg

* [Batchbearbeta och skicka EDI-meddelanden](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Skapa logikappdefinitioner med JSON](../logic-apps/logic-apps-author-definitions.md)
* [Skapa en serverlös app i Visual Studio med Azure Logic Apps and Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Undantagshantering och felloggning för logikappar](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
