---
title: Azure-tabell i programmet för kommersiella marknads platser | Azure Marketplace
description: Konfigurera ledar hantering för Azure-Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285256"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instruktioner för ledar hantering för Azure-Blob

>[!Caution]
>Azure Blob-alternativet för att bearbeta leads från Marketplace-erbjudandet är inaktuellt. Om du för närvarande har ett erbjudande publicerat med konfiguration av lead-hantering för Azure Blob tar du inte längre emot kund leads. Uppdatera konfigurationen för hantering av lead till något av de andra alternativen för hantering av lead. Lär dig mer om de andra alternativen på [sidan för introduktions hanteringens landning](./commercial-marketplace-get-customer-leads.md). "

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace-och AppSource-leads, kan du använda en Azure-Blob för att hantera dessa leads. Du kan sedan välja att exportera data och importera dem till ditt CRM-system. I instruktionerna i den här artikeln får du lära dig hur du skapar ett Azure Storage-konto och en Azure-Blob under det kontot. Dessutom kan du skapa ett nytt flöde med hjälp av Microsoft Flow för att skicka ett e-postmeddelande när ditt erbjudande tar emot ett lead.


## <a name="how-to-configure-azure-blob"></a>Så här konfigurerar du Azure Blob

1. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).
1. När ditt Azure-konto är aktivt loggar du in på [Azure Portal](https://portal.azure.com).
1. I Azure Portal skapar du ett lagrings konto med hjälp av följande procedur.  
    1. Välj **+ skapa en resurs** i den vänstra meny raden.  Det **nya** fönstret (bladet) kommer att visas till höger.
    2. Välj **lagring** i det **nya** fönstret.  En **aktuell** lista visas till höger.
    3. Välj **lagrings kontot** för att börja skapa konto.  Följ anvisningarna i artikeln [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Steg för att skapa ett Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Om du vill ha mer information om lagrings konton väljer du [snabb starts guiden](https://docs.microsoft.com/azure/storage/).  Mer information om lagrings priser finns i [Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills ditt lagrings konto har tillhandahållits, en process som vanligt vis tar några minuter.  Kom sedan åt ditt lagrings konto från **Start** sidan för Azure Portal genom att välja **Visa alla resurser** eller genom att välja **alla resurser** i den vänstra navigerings meny raden i Azure Portal.

    ![Åtkomst till ditt Azure Storage-konto](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. I fönstret lagrings konto väljer du **åtkomst nycklar** och kopierar värdet för *anslutnings strängen* för nyckeln. Spara det här värdet eftersom det är det *lagrings kontots anslutnings sträng* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

     Ett exempel på en anslutning STING är:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure Storage-nyckel](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. På sidan lagrings konto väljer du **blobbar**.

   ![Azure Storage-nyckel](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. På sidan blobbar väljer du knappen **+ container** .

8. Ange ett **namn** för den nya behållaren. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Spara det här värdet eftersom det är det *behållar namns* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

9. Ange nivån för offentlig åtkomst till behållaren som **privat (ingen anonym åtkomst)**.

10. Klicka på **OK** för att skapa containern.

    ![Ny behållare](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurera ditt erbjudande för att skicka leads till Azure-blobben

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
2. Välj **Anslut** under avsnittet ledar hantering.

    ![Connect-erbjudande](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. I popup-fönstret anslutnings information väljer du Azure- **BLOB** för lead-målet.

    ![Anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Ange **behållar namnet** och **lagrings kontots anslutnings sträng** som du fick från att följa dessa anvisningar.

    * Exempel på container namn:`marketplaceleadcontainer`
    * Exempel på anslutnings sträng för lagrings konto: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![anslutnings information](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Välj **Spara**.

    > [!NOTE]
    > Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.


