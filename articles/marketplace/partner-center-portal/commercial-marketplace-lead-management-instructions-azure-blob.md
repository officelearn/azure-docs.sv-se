---
title: Azure-tabell i kommersiellt marknadsplatsprogram| Azure Marketplace
description: Konfigurera leadhantering för Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285256"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Leadhanteringsinstruktioner för Azure Blob

>[!Caution]
>Azure Blob-alternativet för att bearbeta leads från ditt marketplace-erbjudande har inaktiverats. Om du för närvarande har ett erbjudande publicerat med leadhanteringskonfiguration för Azure Blob får du inte längre kundleads. Uppdatera leadhanteringskonfigurationen till något av de andra leadhanteringsalternativen. Läs mer om de andra alternativen på målsidan för [leadhanteringen](./commercial-marketplace-get-customer-leads.md)."

Om ditt CRM-system (Customer Relationship Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace- och AppSource-leads kan du använda en Azure Blob för att hantera dessa leads. Du kan sedan välja att exportera data och importera dem till crm-systemet. Instruktionerna i den här artikeln ger dig genom processen att skapa ett Azure Storage-konto och en Azure Blob under det kontot. Dessutom kan du skapa ett nytt flöde med Microsoft Flow för att skicka ett e-postmeddelande när ditt erbjudande får ett lead.


## <a name="how-to-configure-azure-blob"></a>Konfigurera Azure Blob

1. Om du inte har ett Azure-konto kan du [skapa ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).
1. När ditt Azure-konto är aktivt loggar du in på [Azure-portalen](https://portal.azure.com).
1. Skapa ett lagringskonto med hjälp av följande procedur i Azure-portalen.  
    1. Välj **+Skapa en resurs** i den vänstra menyraden.  Den **nya** rutan (bladet) visas till höger.
    2. Välj **Lagring** i fönstret **Nytt.**  En **utvald** lista visas till höger.
    3. Välj **det lagringskonto som** ska börja skapas för att börja skapa konton.  Följ instruktionerna i artikeln [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Steg för att skapa ett Azure-lagringskonto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Om du vill ha mer information om lagringskonton väljer du [Snabbstartsdatorkurs](https://docs.microsoft.com/azure/storage/).  Mer information om lagringspriser finns i [lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills ditt lagringskonto har etablerats, en process som vanligtvis tar några minuter.  Öppna sedan ditt lagringskonto från **Startsidan** för Azure-portalen genom att välja **Visa alla dina resurser** eller genom att välja Alla **resurser** från den vänstra navigeringsmenyraden i Azure-portalen.

    ![Få åtkomst till ditt Azure-lagringskonto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Välj **Access-nycklar** i fönstret för lagringskonto och kopiera *nyckelns anslutningssträngvärde.* Spara det här värdet eftersom det här är värdet *för anslutningssträng för lagringskonto* som du måste ange i publiceringsportalen för att ta emot leads för ditt marketplace-erbjudande.

     Ett exempel på ett anslutningsstick är:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure-lagringsnyckel](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Välj **Blobbar**på sidan för lagringskonto.

   ![Azure-lagringsnyckel](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. En gång på blobbar sidan, välj **+ Container** knappen.

8. Skriv ett **namn** på den nya behållaren. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om behållar- och blobnamn finns i [Namnge och referera till behållare, blobbar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Spara det här värdet eftersom det här är värdet *Behållarnamn* som du måste ange i publiceringsportalen för att ta emot leads för marketplace-erbjudandet.

9. Ange nivån för offentlig åtkomst till behållaren som **privat (ingen anonym åtkomst)**.

10. Klicka på **OK** för att skapa containern.

    ![Ny behållare](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurera ditt erbjudande för att skicka leads till Azure Blob

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan:

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
2. Välj **Anslut** under avsnittet LeadHantering.

    ![Anslut erbjudande](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. I popup-fönstret Anslutningsinformation väljer du **Azure Blob** för leadmålet.

    ![Anslut detalj](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Ange **den containernamn** och **lagringskontoanslutningssträngen** som du fick från att följa dessa instruktioner.

    * Exempel på behållarnamn:`marketplaceleadcontainer`
    * Exempel på anslutningssträng för lagringskonto: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Välj **Spara**.

    > [!NOTE]
    > Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet.


