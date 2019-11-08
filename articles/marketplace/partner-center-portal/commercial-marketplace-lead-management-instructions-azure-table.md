---
title: Azure-tabell | Azure Marketplace
description: Konfigurera lead-hantering för Azure-tabell.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 9b24e6eb714c531b49ba08591bf4ed33d0f10101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812344"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurera ledar hantering med hjälp av en Azure-tabell

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace-och AppSource-leads, kan du använda en Azure-tabell för att hantera dessa leads. Du kan sedan välja att exportera data och importera dem till ditt CRM-system. Anvisningarna i den här artikeln beskriver hur du skapar ett Azure Storage-konto och en Azure-tabell under det kontot. Dessutom kan du skapa ett nytt flöde med hjälp av Microsoft Flow för att skicka ett e-postmeddelande när ditt erbjudande tar emot ett lead.

## <a name="configure-azure-table"></a>Konfigurera Azure-tabell

1. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).
2. När ditt Azure-konto är aktivt loggar du in på [Azure Portal](https://portal.azure.com).
3. I Azure Portal skapar du ett lagrings konto med hjälp av följande procedur.  
    1. Välj **+ skapa en resurs** i den vänstra meny raden.  Det **nya** fönstret (bladet) kommer att visas till höger.
    2. Välj **lagring** i det **nya** fönstret.  En **aktuell** lista visas till höger.
    3. Välj **lagrings konto** för att börja skapa konto.  Följ anvisningarna i artikeln [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Steg för att skapa ett Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Om du vill ha mer information om lagrings konton väljer du [snabb starts guiden](https://docs.microsoft.com/azure/storage/).  Mer information om lagrings priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills ditt lagrings konto har tillhandahållits, en process som vanligt vis tar några minuter.  Kom sedan åt ditt lagrings konto från **Start** sidan för Azure Portal genom att välja **Visa alla resurser** eller genom att välja **alla resurser** i det vänstra navigerings meny fältet i Azure Portal.

    ![Åtkomst till ditt Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. I fönstret lagrings konto väljer du **åtkomst nycklar** och kopierar värdet för *anslutnings strängen* för nyckeln. Spara det här värdet eftersom det är det *lagrings kontots anslutnings sträng* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande. 

    Ett exempel på en anslutning STING är:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure Storage-nyckel](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. I fönstret lagrings konto väljer du **tabeller** och väljer **+ tabell** för att skapa en tabell. Ange ett namn för tabellen och välj **OK**. Spara det här värdet eftersom du behöver det om du vill konfigurera ett MS Flow för att ta emot e-postaviseringar när leads tas emot.

    ![Azure-tabeller](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Du kan använda [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) eller andra verktyg för att se data i lagrings tabellen. Du kan också exportera data i Azure-tabellen. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Valfritt Använda Microsoft Flow med en Azure-tabell  

Du kan använda [Microsoft Flow](https://docs.microsoft.com/flow/) för att automatisera meddelanden varje gång ett lead läggs till i Azure-tabellen. Om du inte har ett konto kan du [Registrera dig för ett kostnads fritt konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exempel på lead-avisering

Använd det här exemplet som en guide för att skapa ett enkelt flöde som automatiskt skickar ett e-postmeddelande när ett nytt lead läggs till i en Azure-tabell. Det här exemplet ställer in en upprepning för att skicka lead-information varje timme om tabell lagringen uppdateras.

1. Logga in på ditt Microsoft Flow-konto.
2. I det vänstra navigerings fältet väljer du **mina flöden**.
3. I det övre navigerings fältet väljer du **+ ny**.  
4. I list rutan väljer du **+ schemalagd-från Tom**

   ![Mina flöden * * + schemalagda – från tomt * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  I fönstret *skapa ett schemalagt flöde* under *Upprepa varje* väljer du "1" för intervall och "timme" som frekvens. Ge också flödet ett namn om du vill. Välj **Skapa**.

    >[!Note]
    >Även om det här exemplet använder ett intervall på 1 timme, kan du välja det intervall och den frekvens som passar dina affärs behov bäst.

    ![Bygg ett schemalagt flöde.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Välj **+ nytt steg**.
7. I fönstret *Välj en åtgärd* söker du efter "Hämta tidigare tid" och väljer **Hämta tidigare tid** under åtgärder.

   ![Välj en åtgärd.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. I fönstret **Hämta tidigare tid** anger du **intervallet** till 1. Välj **timme**från List rutan **tidsenhet** .

    >[!Important]
    >Kontrol lera att intervallet och tidsenheten matchar intervallet och frekvensen som du konfigurerade för upprepning i steg 5.

    ![Set hämta tidigare tidsintervall](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Du kan när som helst kontrol lera ditt flöde för att kontrol lera att varje steg har kon figurer ATS korrekt. Om du vill kontrol lera flödet väljer du **flödes kontroll** från Flow-meny raden.

I nästa uppsättning steg ansluter du till Azure-tabellen och konfigurerar bearbetnings logiken för att hantera nya leads.

9. Efter steget Hämta tidigare tid väljer du **+ nytt steg**och söker sedan efter "Hämta entiteter" i fönstret *Välj en åtgärd* .
10. Under **åtgärder**väljer du **Hämta entiteter (Azure Table Storage)** .
11. I fönstret **Azure Table Storage** anger du information för följande fält och väljer **skapa**:

    * *Anslutnings namn* – ange ett beskrivande namn för anslutningen som du etablerar mellan det här flödet och Azure-tabellen.
    * *Lagrings konto namn* – ange namnet på lagrings kontot för Azure-tabellen. Du hittar det här på sidan **åtkomst nycklar** för lagrings kontot.
    * *Delad lagrings nyckel* – ange nyckelvärdet för ditt lagrings konto för Azure-tabellen. Du hittar det här på sidan **åtkomst nycklar** för lagrings kontot.

        ![Azure Table Storage.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    När du klickar på Skapa visas ett fönster för att *Hämta entiteter* . Välj **Visa avancerade alternativ** och ange information för följande fält:

       * *Tabell* – Välj namnet på din Azure-Table Storage (från steg 6 av instruktioner om hur du konfigurerar en Azure-tabell). I nästa skärm bild visas prompten när tabellen "marketplaceleads" är markerad för det här exemplet.

            ![Azure Table Hämta entiteter.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filter fråga* – Välj det här fältet och klistra in funktionen i fältet: `Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table get entities – filter Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Nu när du har slutfört konfigurationen av anslutningen till Azure-tabellen väljer du **nytt steg** för att lägga till ett villkor för att söka igenom Azure-tabellen efter nya leads. 

13. I fönstret **Välj en åtgärd** väljer du **åtgärder**och sedan **villkors kontrollen**.

    ![Azure-tabell – Välj en åtgärd.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. I fönstret **villkor** väljer du fältet **Välj ett värde** och väljer sedan **uttryck** i popup-fönstret.

15. Klistra in `length(body('Get_entities')?['value'])` i fältet ***FX*** . Välj **OK** för att lägga till den här funktionen. 

16. Så här slutför du konfigurationen av villkoret:
    1. Välj "är större än" i list rutan.
    2. Ange 0 som värde

        ![Azure Table-villkor.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

I följande steg ska du ställa in vilken åtgärd som ska vidtas baserat på resultatet av villkoret.

* Om villkoret matchar **om nej**, gör ingenting.
* Om villkoret löses med **Ja**utlöses en åtgärd som ansluter ditt Office 365-konto för att skicka ett e-postmeddelande. 

17. Välj **Lägg till en åtgärd** under **om ja**.

    ![Azure Table-Condition, * * om ja * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Välj **Skicka ett e-postmeddelande (Office 365 Outlook)** .

    ![Azure Table-Condition, * * om ja * *, skicka e-post.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Om du vill använda en annan e-postprovider söker du efter och väljer Skicka ett e-postmeddelande (e-post) som åtgärd i stället. Anvisningarna visar hur du konfigurerar med Office 365 Outlook men instruktionerna liknar en annan e-postprovider.

19. Ange information för följande fält i **Office 365 Outlook** -fönstret:

    1. Ange en **e-postadress** för alla som får det här meddelandet.
    1. **Subject** – ange ett ämne för e-postmeddelandet. Till exempel: nya leads!
    1. **Brödtext** – Lägg till den text som du vill inkludera i varje e-postmeddelande (valfritt) och klistra in i brödtext `body('Get_entities')?['value']`.

    >[!Note]
    >Du kan infoga ytterligare statiska eller dynamiska data punkter i bröd texten i det här e-postmeddelandet.

    ![Azure Table-Condition, * * om ja * *, Office 365 Outlook-fönstret.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Spara flödet genom att välja **Spara** . Microsoft Flow kommer automatiskt att testa flödet för fel. Om det inte finns några fel börjar flödet att köras när det har sparats.

I nästa skärm bild visas ett exempel på hur det slutliga flödet ska se ut.

![Ett exempel på det slutliga flödet.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Hantera ditt flöde

Det är enkelt att hantera flödet när det är igång. Du har fullständig kontroll över ditt flöde. Du kan till exempel stoppa den, redigera den, se en körnings historik och få analys. I nästa skärm bild visas de alternativ som är tillgängliga för att hantera ett flöde. 

 ![Hantera ett flöde](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Flödet fortsätter att köras tills du avbryter det genom att använda alternativet **inaktivera flöde** av.

Om du inte får några e-postaviseringar om leads innebär det att nya leads inte har lagts till i Azure-tabellen. Om det uppstår flödes haverier får du ett e-postmeddelande som exemplet i nästa skärmdump.

 ![E-postavisering om flödes problem](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
2. Välj **Anslut** under avsnittet ledar hantering.
3. I popup-fönstret anslutnings information väljer du Azure- **tabell** för lead- **målet**och klistrar in anslutnings strängen från det Azure Storage-konto som du skapade genom att följa tidigare steg i **anslutnings strängen för lagrings kontot** sidfält.
4. Välj **Spara**. 

>[!Note]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

När leads skapas skickar Microsoft leads till Azure-tabellen. Om du har konfigurerat ett flöde skickas även ett e-postmeddelande till den e-postadress som du har konfigurerat.

![Leadhantering](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Hantering av lead-anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Lead management-anslutnings information lagrings konto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

