---
title: Azure Table Storage | Azure Marketplace
description: Konfigurera hantering av lead i Azure Table Storage.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: 21105d72ccd288faf0fed58019e67afe2e1c9d01
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825277"
---
# <a name="lead-management-instructions-for-table-storage"></a>Lead – hanterings anvisningar för Table Storage

Den här artikeln beskriver hur du konfigurerar Azure Table Storage för att hantera försäljnings leads. Med Table Storage kan du lagra och ändra kund information.

## <a name="configure-table-storage"></a>Konfigurera tabell lagring

1. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).
1. När ditt konto är aktivt loggar du in på [Azure Portal](https://portal.azure.com).
1. Följ de här stegen i Azure Portal:  
    1. Välj **+ skapa en resurs** i rutan till vänster. Det **nya** fönstret öppnas.
    1. I det **nya** fönstret väljer du **Storage**. En lista som är **aktuell** öppnas på höger sida.
    1. Välj **lagrings konto**. Följ sedan instruktionerna i [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Skapa ett Azure-lagringskonto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Mer information om lagrings konton finns i [snabb starts guider](https://docs.microsoft.com/azure/storage/). Pris information finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Vänta tills ditt lagrings konto har tillhandahållits, vilket vanligt vis tar några minuter. Öppna sedan kontot från start sidan för Azure Portal: Välj **Se alla resurser** eller **alla resurser** i navigerings fönstret.

    ![Åtkomst till ditt Azure Storage-konto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Kopiera lagrings kontots anslutnings sträng för nyckeln från fönstret lagrings konto. Klistra in det i fältet **anslutnings sträng** för lagrings kontot i Cloud Partner Portal.

    Exempel på anslutnings sträng:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure Storage-nyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Du kan använda [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) eller ett liknande verktyg för att se data i din tabell lagring. Du kan också exportera data från den.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Använd Microsoft Flow med Table Storage (*valfritt*)

Du kan använda [Microsoft Flow](https://docs.microsoft.com/flow/) för att automatiskt skicka meddelanden när ett lead läggs till i din tabell lagring. Om du inte har ett Microsoft Flow konto kan du [Registrera dig för ett kostnads fritt konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exempel på lead-avisering

Det här exemplet visar hur du skapar ett grundläggande flöde. Flödet skickar automatiskt ett e-postmeddelande varje timme när nya leads läggs till i din tabell lagring.

1. Logga in på ditt Microsoft Flow-konto.
1. I navigerings fönstret på vänster sida väljer du **mina flöden**.
1. I det övre navigerings fältet väljer du **+ ny**.  
1. I list rutan väljer du **+ skapa från Tom**.
1. Under **skapa ett flöde från tomt**väljer du **skapa från Tom**.

   ![Skapa ett nytt flöde från tomt](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. På Sök sidan anslutningar och utlösare väljer du **utlösare**.
1. Under **utlösare**väljer du **upprepning**.
1. Behåll standardinställningen **1** för **intervall**i **upprepnings** fönstret. I list rutan **frekvens** väljer du **timme**.

   >[!NOTE] 
   >I det här exemplet används ett intervall på en timme. Men du kan välja ett intervall och en frekvens som passar dina affärs behov bäst.

   ![Ange en frekvens på 1 timme för upprepning](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Välj **+ nytt steg**.
1. Sök efter **tidigare tid**och välj **Hämta tidigare tid** under **Välj en åtgärd**.

    ![Sök efter och Välj åtgärden "Hämta tidigare tid"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. I fönstret **Hämta tidigare tid** anger du **intervallet** till **1**.  I list rutan **tidsenhet** väljer du **timme**.
    >[!IMPORTANT] 
    >Kontrol lera att **intervallet** och **tidsenheten** stämmer överens med det intervall och den frekvens som du konfigurerade för upprepning (steg 8).

    ![Ange tidigare tidsintervall för hämtning](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Du kan när som helst kontrol lera ditt flöde för att kontrol lera att varje steg har kon figurer ATS korrekt: Välj **flödes kontroll** från Flow-meny raden.

I nästa uppsättning steg ansluter du till lagrings tabellen och konfigurerar bearbetnings logiken för att hantera nya leads.

1. Efter steget **Hämta föregående tid** väljer du **+ nytt steg**och söker sedan efter **Hämta entiteter**.
1. Under **åtgärder**väljer du **Hämta entiteter**och väljer sedan **Visa avancerade alternativ**.
1. Fyll i följande fält i fönstret **Hämta entiteter** :

   - **Tabell**: namnet på din tabell lagring. Följande bild visar "MarketPlaceLeads" som anges:

     ![Välj ett anpassat värde för Azure-tabellens namn](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filter fråga**: när du väljer det här fältet visas ikonen **Hämta tidigare tid** i ett popup-fönster. Välj **tidigare tid** för att använda det här värdet som en tidsstämpel för att filtrera frågan. Eller så kan du klistra in följande funktion i fältet:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Konfigurera funktionen Filtrera fråga](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Välj **nytt steg** för att lägga till ett villkor för att genomsöka din tabell lagring efter nya leads.

   ![Använd "nytt steg" för att lägga till ett villkor för att skanna tabell lagring](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. I fönstret **Välj en åtgärd** väljer du **åtgärder**och sedan **villkors kontroll**.

     ![Lägg till en villkors kontroll](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. I fönstret **villkor** väljer du **Välj ett värde**och väljer sedan **uttryck** i popup-fönstret.
1. Klistra in `length(body('Get_entities')?['value'])` i fältet ***FX*** . Välj **OK** för att lägga till den här funktionen. 



     ![Lägg till en funktion i villkoret](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Konfigurera åtgärden som ska vidtas baserat på resultatet av villkoret.

    1. Select **är större än** i den nedrullningsbara listan.
   1. Ange **0** som värde.

     ![Konfigurera en åtgärd baserat på villkors resultat](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Om villkoret matchar "om nej" gör inget.

    Om villkoret matchar "om ja" utlöses en åtgärd som ansluter ditt Office 365-konto för att skicka ett e-postmeddelande:
   1. Välj **Lägg till en åtgärd**.
   1. Välj **Skicka ett e-postmeddelande**.
   1. Ange information i följande fält i fönstret **Skicka ett e-postmeddelande** :

      - **Till**: en e-postadress för alla som får meddelandet.
      - **Subject**: ett ämne för e-postmeddelandet. Till exempel: *nya leads!*
      - **Brödtext**: den text som du vill inkludera i varje e-postmeddelande (valfritt). Klistra också in `body('Get_entities')?['value']` som en funktion för att infoga lead-information.

        >[!NOTE] 
        >Du kan infoga ytterligare statiska eller dynamiska data punkter i e-postmeddelandets brödtext.

      ![Konfigurera e-post för lead-avisering](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Spara flödet genom att välja **Spara** . Microsoft Flow testar den automatiskt för fel. Om det inte finns några fel börjar flödet att köras när det har sparats.

    Följande bild visar ett exempel på hur det slutliga flödet ska se ut.

    [![avslutande flödes ordning](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Markera bilden för att förstora den.* )

### <a name="manage-your-flow"></a>Hantera ditt flöde

Det är enkelt att hantera flödet när det har körts. Du har fullständig kontroll över ditt flöde. Du kan till exempel stoppa den, redigera den, se en körnings historik och få analys. Följande bild visar alternativen för flödes hantering.

 ![Flödes hanterings alternativ](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Flödet fortsätter att köras tills du väljer **Stäng av flöde**.

Om du inte får några e-postmeddelanden om leads har inga nya leads lagts till i din tabell lagring.
Du får ett e-postmeddelande som i följande exempel om ett flödes fel inträffar:

 ![E-postavisering om flödes problem](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
