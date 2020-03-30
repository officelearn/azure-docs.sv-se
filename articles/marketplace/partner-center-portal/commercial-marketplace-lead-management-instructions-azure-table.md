---
title: Azure-tabell | Azure Marketplace
description: Konfigurera leadhantering för Azure Table.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288843"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurera leadhantering med hjälp av en Azure-tabell

Om ditt CRM-system (Customer Relationship Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace- och AppSource-leads kan du använda en Azure-tabell för att hantera dessa leads. Du kan sedan välja att exportera data och importera dem till crm-systemet. Instruktionerna i den här artikeln hjälper dig att skapa ett Azure Storage-konto och en Azure-tabell under det kontot. Dessutom kan du skapa ett nytt flöde med Microsoft Flow för att skicka ett e-postmeddelande när ditt erbjudande får ett lead.

## <a name="configure-azure-table"></a>Konfigurera Azure-tabell

1. Om du inte har ett Azure-konto kan du [skapa ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).
2. När ditt Azure-konto är aktivt loggar du in på [Azure-portalen](https://portal.azure.com).
3. Skapa ett lagringskonto med hjälp av följande procedur i Azure-portalen.  
    1. Välj **+Skapa en resurs** i den vänstra menyraden.  Den **nya** rutan (bladet) visas till höger.
    2. Välj **Lagring** i fönstret **Nytt.**  En **utvald** lista visas till höger.
    3. Välj **Lagringskonto** för att börja skapa konton.  Följ instruktionerna i artikeln [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Steg för att skapa ett Azure-lagringskonto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Om du vill ha mer information om lagringskonton väljer du [Snabbstartsdatorkurs](https://docs.microsoft.com/azure/storage/).  Mer information om lagringspriser finns i [lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills ditt lagringskonto har etablerats, en process som vanligtvis tar några minuter.  Öppna sedan ditt lagringskonto från **Startsidan** för Azure-portalen genom att välja **Visa alla dina resurser** eller genom att välja Alla **resurser** från den vänstra navigeringsmenyraden i Azure-portalen.

    ![Få åtkomst till ditt Azure-lagringskonto](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. Välj **Access-nycklar** i fönstret för lagringskonto och kopiera *nyckelns anslutningssträngvärde.* Spara det här värdet eftersom det här är värdet *för anslutningssträng för lagringskonto* som du måste ange i publiceringsportalen för att ta emot leads för ditt marketplace-erbjudande. 

    Ett exempel på ett anslutningsstick är:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure-lagringsnyckel](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. I fönstret för lagringskonto väljer du **Tabeller** och väljer **+Tabell** för att skapa en tabell. Ange ett namn på tabellen och välj **OK**. Spara det här värdet som du behöver det om du vill konfigurera ett MS Flow för att ta emot e-postmeddelanden när leads tas emot.

    ![Azure-tabeller](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Du kan använda [Azure storage explorer](https://archive.codeplex.com/?p=azurestorageexplorer) eller något annat verktyg för att se data i din lagringstabell. Du kan också exportera data i Azure-tabellen. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Valfritt) Använda Microsoft Flow med en Azure-tabell  

Du kan använda [Microsoft Flow](https://docs.microsoft.com/flow/) för att automatisera meddelanden varje gång ett lead läggs till i Azure-tabellen. Om du inte har ett konto kan du [registrera dig för ett kostnadsfritt konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exempel på leadmeddelande

Använd det här exemplet som en guide för att skapa ett enkelt flöde som automatiskt skickar ett e-postmeddelande när ett nytt lead läggs till i en Azure-tabell. I det här exemplet ställs en upprepning in för att skicka leadinformation varje timme om tabelllagringen uppdateras.

1. Logga in på ditt Microsoft Flow-konto.
2. Välj Mina flöden i det vänstra **navigeringsfältet**.
3. Välj **+ Nytt**i det övre navigeringsfältet .  
4. I listrutan väljer du **+ Schemalagd - från tom**

   ![Mina flöden **+ Schemalagda - från tomma**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  På *fönstret Skapa ett schemalagt flöde* under *Upprepa varje* val "1" för intervall och "timme" för frekvens. Ge också flödet ett namn om du vill. Välj **Skapa**.

    >[!Note]
    >Även om det här exemplet använder ett intervall på 1 timme kan du välja det intervall och den frekvens som är bäst för dina affärsbehov.

    ![Skapa ett schemalagt flöde.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Välj **+ Nytt steg**.
7. I fönstret *Välj ett åtgärdsfönster* söker du efter "komma förbi tid" och väljer sedan **Hämta tidigare tid** under Åtgärder.

   ![Välj en åtgärd.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. I fönstret **Hämta tidigare tid** ställer du in **intervallet** till 1. Välj **Timme** i listrutan **Hour**Time unit .

    >[!Important]
    >Kontrollera att den här intervall- och tidsenheten matchar det intervall och den frekvens som du har konfigurerat för Återkommande i steg 5.

    ![Ställ in få förbi tidsintervall](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Du kan kontrollera flödet när som helst för att kontrollera att varje steg är korrekt konfigurerat. Om du vill kontrollera flödet väljer du **Flödeskontroll** i menyraden flöde.

I nästa uppsättning steg ansluter du till din Azure-tabell och ställer in bearbetningslogiken för att hantera nya leads.

9. När steget Hämta tidigare tid väljer du **+ Nytt steg**och söker sedan efter "Hämta entiteter" i fönstret Välj en *åtgärd.*
10. Under **Åtgärder**väljer du **Hämta entiteter (Azure Table Storage)**.
11. I fönstret **Azure Table Storage** anger du information för följande fält och väljer **Skapa:**

    * *Anslutningsnamn* - ange ett meningsfullt namn för anslutningen som du upprättar mellan det här flödet och Azure-tabellen.
    * *Namn på lagringskonto* – ange namnet på lagringskontot för din Azure-tabell. Du hittar detta på sidan **Åtkomstnycklar** för lagringskontot.
    * *Delad lagringsnyckel* – ange nyckelvärdet för ditt butikskonto för din Azure-tabell. Du hittar detta på sidan **Åtkomstnycklar** för lagringskontot.

        ![Azure-tabelllagring.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    När du har klickat på Skapa visas fönstret *Hämta entiteter.* Här väljer du **Visa avancerade alternativ** och ger information för följande fält:

       * *Tabell* - Välj namnet på din Azure Table Storage (från steg 6 med instruktioner om hur du konfigurerar en Azure-tabell). Nästa skärminspelning visar prompten när tabellen "marketplaceleads" är markerad för det här exemplet.

            ![Azure Table hämta entiteter.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Filterfråga* - Välj det här fältet och klistra in den här funktionen i fältet:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table get entiteter - Filtrera Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Nu när du har konfigurerat anslutningen till Azure-tabellen väljer du **Nytt steg** för att lägga till ett villkor för att söka igenom Azure-tabellen efter nya leads. 

13. I fönstret **Välj en åtgärd** väljer du **Åtgärder**och väljer sedan **villkorskontrollen**.

    ![Azure-tabell - Välj en åtgärd.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. I fönstret **Villkor** markerar du fältet **Välj ett värde** och väljer sedan **Uttryck** i popup-fönstret.

15. Klistra `length(body('Get_entities')?['value'])` in i ***fx-fältet.*** Välj **OK** om du vill lägga till den här funktionen. 

16. Så här slutför du inrättandet av villkoret:
    1. Välj "är större än" i listrutan.
    2. Ange 0 som värde

        ![Azure-tabell - Villkor.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

I de närmaste stegen kommer du att ställa in åtgärden att vidta baserat på resultatet av villkoret.

* Om villkoret går till **Om nej**, gör ingenting.
* Om villkoret går att lösa **om ja**utlöser du en åtgärd som ansluter ditt Office 365-konto för att skicka ett e-postmeddelande. 

17. Välj **Lägg till en åtgärd** under Om **ja**.

    ![Azure-tabell - Villkor, **Om ja**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Välj **Skicka ett e-postmeddelande (Office 365 Outlook)**.

    ![Azure Table - Condition, **If yes**, send email.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Om du vill använda en annan e-postleverantör söka efter och välj Skicka ett e-postmeddelande (Mail) som åtgärd i stället. Instruktionerna visar hur du konfigurerar med Office 365 Outlook, men instruktionerna är liknande för en annan e-postleverantör.

19. I **Fönstret Office 365 Outlook** finns information om följande fält:

    1. **Till** - Ange en e-postadress för alla som kommer att få denna avisering.
    1. **Ämne** - Ange ett ämne för e-postmeddelandet. Till exempel: Nya leads!
    1. **Brödtext** - Lägg till den text som du vill ta `body('Get_entities')?['value']`med i varje e-postmeddelande (valfritt) och klistra sedan in i brödtext .

    >[!Note]
    >Du kan infoga ytterligare statiska eller dynamiska datapunkter i brödtexten i det här e-postmeddelandet.

    ![Azure Table - Condition, **If yes**, Office 365 Outlook-fönstret.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Spara flödet genom att klicka på **Spara**. Microsoft Flow testar automatiskt flödet efter fel. Om det inte finns några fel börjar flödet köras när det har sparats.

Nästa skärmdump visar ett exempel på hur det slutliga flödet ska se ut.

![Ett exempel på det slutliga flödet.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Hantera ditt flöde

Hantera ditt flöde när det är igång är lätt. Du har fullständig kontroll över ditt flöde. Du kan till exempel stoppa den, redigera den, se en körningshistorik och hämta analyser. Nästa skärminspelning visar de alternativ som är tillgängliga för att hantera ett flöde. 

 ![Hantera ett flöde](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Flödet fortsätter att köras tills du stoppar det med alternativet **Stäng av flödet.**

Om du inte får några lead-e-postmeddelanden betyder det att nya leads inte har lagts till i Azure-tabellen. Om det finns några flödesfel får du ett e-postmeddelande som exemplet i nästa skärmdump.

 ![E-postmeddelande om flödesfel](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan:

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
2. Välj **Anslut** under avsnittet LeadHantering.
3. I popup-fönstret Anslutningsinformation väljer du **Azure-tabell** för **leadmålet**och klistrar in anslutningssträngen från Azure-lagringskontot som du skapade genom att följa tidigare steg i **strängfältet Lagringskontoanslutning.**
4. Välj **Spara**. 

>[!Note]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet.

När leads genereras skickar Microsoft leads till Azure-tabellen. Om du har konfigurerat ett flöde skickas även ett e-postmeddelande till den e-postadress som du har konfigurerat.

![Leadhantering](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Leadhantering - anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Leadhantering - lagringskonto för anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

