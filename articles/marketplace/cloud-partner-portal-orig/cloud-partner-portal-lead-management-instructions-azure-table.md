---
title: Lagring av Azure-tabell | Azure Marketplace
description: Konfigurera leadhantering i Azure Table Storage.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280362"
---
# <a name="lead-management-instructions-for-table-storage"></a>Lead-hanteringsinstruktioner för tabelllagring

I den här artikeln beskrivs hur du konfigurerar Azure Table Storage för att hantera försäljningsleads. Bordslagring hjälper dig att lagra och ändra kundinformation.

## <a name="configure-table-storage"></a>Konfigurera tabelllagring

1. Om du inte har ett Azure-konto [skapar du ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).
1. När ditt konto är aktivt loggar du in på [Azure-portalen](https://portal.azure.com).
1. Gör så här i Azure-portalen:  
    1. Välj **+Skapa en resurs** i fönstret till vänster. Fönstret **Nytt** öppnas.
    1. Välj **Lagring**i fönstret **Nytt** . En **aktuell** lista öppnas på höger sida.
    1. Välj **lagringskonto**. Följ sedan instruktionerna på [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Skapa ett Azure-lagringskonto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Mer information om lagringskonton finns i [Snabbstartsjälvstudier](https://docs.microsoft.com/azure/storage/). Prisinformation finns i [Azure-lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

1. Vänta tills ditt lagringskonto har etablerats, vilket vanligtvis tar några minuter. Öppna sedan kontot från startsidan för Azure-portalen: Välj **Visa alla dina resurser** eller Alla **resurser** i navigeringsfönstret.

    ![Få åtkomst till ditt Azure-lagringskonto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Kopiera anslutningssträngen för lagringskontot för nyckeln från lagringskontofönstret. Klistra in den i fältet **Anslutningssträng** för lagringskontot i Molnpartnerportalen.

    Exempel på anslutningssträng:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure-lagringsnyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Du kan använda [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) eller ett liknande verktyg för att se data i tabelllagringen. Du kan också exportera data från den.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Använda Microsoft Flow med tabelllagring *(tillval)*

Du kan använda [Microsoft Flow](https://docs.microsoft.com/flow/) för att automatiskt skicka meddelanden när ett lead läggs till i tabelllagringen. Om du inte har ett Microsoft Flow-konto [registrerar du dig för ett kostnadsfritt konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exempel på leadmeddelande

Det här exemplet visar hur du skapar ett grundläggande flöde. Flödet skickar automatiskt ett e-postmeddelande varje timme när nya leads läggs till i tabelllagringen.

1. Logga in på ditt Microsoft Flow-konto.
1. Välj **Mina flöden**i navigeringsfönstret till vänster .
1. Välj **+Nytt**i det övre navigeringsfältet .  
1. Välj **+Skapa från tom**.
1. Under **Skapa ett flöde från tomt**väljer du Skapa från **tomt**.

   ![Skapa ett nytt flöde från tomt](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. På söksidan för kopplingar och utlösare väljer du **Utlösare**.
1. Under **Utlösare**väljer du **Återkommande**.
1. Behåll standardinställningen **1** för **Intervall**i fönstret **Återkommande** . Välj **Timme**i listrutan **Frekvens** .

   >[!NOTE] 
   >I det här exemplet används ett intervall på en timme. Men du kan välja ett intervall och en frekvens som bäst passar dina affärsbehov.

   ![Ange en 1-timmarsfrekvens för upprepning](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Välj **+Nytt steg**.
1. Sök efter **Hämta tidigare tid**och välj sedan Hämta tidigare **tid** under Välj **en åtgärd**.

    ![Hitta och välj åtgärden "komma förbi tid"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. I fönstret **Hämta tidigare tid** ställer du in **intervallet** till **1**.  Välj **Timme i** listrutan Time **Hour**unit .
    >[!IMPORTANT] 
    >Kontrollera att **intervall-** och **tidsenheten** matchar det intervall och den frekvens som du har konfigurerat för upprepning (steg 8).

    ![Ställ in intervallet hämta förbi tid](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Du kan kontrollera flödet när som helst för att kontrollera att varje steg är korrekt konfigurerat: Välj **flödeskontroll** i flödesmenyraden.

I nästa steg ansluter du till lagringstabellen och ställer in bearbetningslogiken för att hantera nya leads.

1. När steget **Hämta tidigare tid** väljer du **+Nytt steg**och söker sedan efter Hämta **entiteter**.
1. Under **Åtgärder**väljer du **Hämta entiteter**och väljer sedan **Visa avancerade alternativ**.
1. Fyll i följande fält i fönstret **Hämta entiteter:**

   - **Tabell**: namnet på ditt bordsförvaring. Följande bild visar "MarketPlaceLeads" in:

     ![Välj ett anpassat värde för Azure-tabellnamn](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filterfråga:** När du väljer det här fältet visas ikonen **Hämta tidigare tid** i ett popup-fönster. Välj **Tidigare tid** om du vill använda det här värdet som tidsstämpel för att filtrera frågan. Du kan också klistra in följande funktion i fältet:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Konfigurera filterfrågafunktionen](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Välj **Nytt steg** om du vill lägga till ett villkor för att söka igenom tabelllagringen efter nya leads.

   ![Använd "Nytt steg" för att lägga till ett villkor för att skanna tabelllagring](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. I fönstret **Välj en åtgärd** väljer du **Åtgärder**och väljer sedan **Villkorskontroll**.

     ![Lägga till en tillståndskontroll](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Välj **ett värde**i fönstret **Villkor** och välj sedan **Uttryck** i popup-fönstret.
1. Klistra `length(body('Get_entities')?['value'])` in i ***fx-fältet.*** Välj **OK** om du vill lägga till den här funktionen. 



     ![Lägga till en funktion i villkoret](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Ställ in åtgärden som ska vidtas baserat på resultatet av villkoret.

    1. Välj **är större än** från listrutan.
   1. Ange **0** som värde.

     ![Ställ in en åtgärd baserat på villkorsresultat](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Om villkoret går att lösa till "Om nej", gör ingenting.

    Om villkoret går att lösa till "Om ja" utlöser du en åtgärd som ansluter ditt Office 365-konto för att skicka ett e-postmeddelande:
   1. Välj **lägg till en åtgärd**.
   1. Välj **Skicka ett e-postmeddelande**.
   1. Ange information i följande fält i fönstret **Skicka ett e-postmeddelande:**

      - **Till**: en e-postadress för alla som kommer att få meddelandet.
      - **Ämne:** ett ämne för e-post. Till exempel: *Nya leads!*
      - **Brödtext**: den text som du vill ta med i varje e-postmeddelande (valfritt). Klistra också `body('Get_entities')?['value']` in som en funktion för att infoga leadinformation.

        >[!NOTE] 
        >Du kan infoga ytterligare statiska eller dynamiska datapunkter i brödtexten i e-postmeddelandet.

      ![Konfigurera e-post för leadavisering](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Spara flödet genom att klicka på **Spara**. Microsoft Flow testar det automatiskt för fel. Om det inte finns några fel börjar flödet köras när det har sparats.

    Följande bild visar ett exempel på hur det slutliga flödet ska se ut.

    [![Slutlig flödessekvens](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (Markera*bilden för att förstora den.*)

### <a name="manage-your-flow"></a>Hantera ditt flöde

Det är lätt att hantera flödet när det är igång. Du har fullständig kontroll över ditt flöde. Du kan till exempel stoppa den, redigera den, se en körningshistorik och hämta analyser. Följande bild visar alternativen för flödeshantering.

 ![Alternativ för flödeshantering](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Flödet fortsätter att köras tills du väljer **Stäng av flödet**.

Om du inte får några lead-e-postmeddelanden har inga nya leads lagts till i tabelllagringen.
Du får ett e-postmeddelande som följande exempel om ett flödesfel inträffar:

 ![E-postmeddelande om flödesfel](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
