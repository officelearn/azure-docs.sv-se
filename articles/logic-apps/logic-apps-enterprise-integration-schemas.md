---
title: Validera XML med scheman
description: Lägga till scheman för att validera XML-dokument i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979366"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validera XML med scheman i Azure Logic Apps med Enterprise-integrationspaket

Om du vill kontrollera att dokument använder giltig XML och har förväntade data i det fördefinierade formatet för företagsintegrationsscenarier i Azure Logic Apps kan logikappen använda scheman. Ett schema kan också validera meddelanden som logikappar utbyter i B2B-scenarier (Business-to-Business).

Begränsningar som är relaterade till integrationskonton och artefakter, till exempel scheman, se [Gränser och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar dina scheman och andra artefakter för företagsintegrering och B2B-lösningar (Business-to-Business). 

  Om schemat är [2 MB eller mindre](#smaller-schema)kan du lägga till schemat i ditt integrationskonto direkt från Azure-portalen. Men om ditt schema är större än 2 MB men inte större än [schemastorleksgränsen](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)kan du ladda upp schemat till ett Azure-lagringskonto. 
  Om du vill lägga till schemat i ditt integrationskonto kan du sedan länka till ditt lagringskonto från ditt integrationskonto. 
  För den här uppgiften, här är de objekt du behöver: 

  * [Azure-lagringskonto](../storage/common/storage-account-overview.md) där du skapar en blob-behållare för ditt schema. Läs om hur du [skapar ett lagringskonto](../storage/common/storage-account-create.md). 

  * Blob-behållare för lagring av schemat. Lär dig hur du [skapar en blob-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Du behöver behållarens innehåll URI senare när du lägger till schemat i ditt integrationskonto.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), som du kan använda för att hantera lagringskonton och blob-behållare. 
  Om du vill använda Storage Explorer väljer du något av alternativen här:
  
    * Leta reda på och välj ditt lagringskonto i Azure-portalen. 
    Välj **Lagringskonto**på menyn för lagringskonto .

    * Hämta [och installera Azure Storage Explorer](https://www.storageexplorer.com/)för skrivbordsversionen . 
    Anslut sedan Storage Explorer till ditt lagringskonto genom att följa stegen i [Komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Mer information finns [i Snabbstart: Skapa en blob i objektlagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Du behöver ingen logikapp när du skapar och lägger till scheman. Om du vill använda ett schema behöver logikappen länka till ett integrationskonto där du lagrar schemat. Läs om hur du [länkar logikappar till integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte har någon logikapp ännu kan du läsa om hur du [skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Lägga till scheman

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med dina Azure-kontoautentiseringsuppgifter.

1. Om du vill söka efter och öppna ditt integrationskonto väljer du **Alla tjänster**på Azure-menyn. Skriv "integrationskonto" i sökrutan. Välj **integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Välj det integrationskonto där du vill lägga till schemat, till exempel:

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Välj panelen Schemas under **Komponenter**på sidan **Översikt** för **integrationskontot.**

   ![Välj "Scheman"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. När sidan Scheman har **öppnats** väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Baserat på storleken på schemafilen (.xsd) följer du stegen för att ladda upp ett schema som antingen är [upp till 2 MB](#smaller-schema) eller mer än [2 MB, upp till 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Lägga till scheman på upp till 2 MB

1. Ange ett namn för schemat under **Lägg till**schema. 
   Håll **liten fil** markerad. Välj mappikonen bredvid rutan **Schema.** Hitta och välj det schema du laddar upp, till exempel:

   ![Ladda upp mindre schema](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. När du är redo väljer du **OK**.

   När schemat har laddats upp visas schemat i listan **Scheman.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Lägga till scheman mer än 2 MB

Om du vill lägga till större scheman kan du ladda upp schemat till en Azure-blob-behållare i ditt Azure-lagringskonto. Dina steg för att lägga till scheman skiljer sig åt beroende på om blob-behållaren har offentlig läsbehörighet. Så först, kontrollera om din blob-behållare har offentlig läsåtkomst genom att följa dessa steg: [Ange offentlig åtkomstnivå för blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrollera åtkomstnivå för behållare

1. Öppna Azure Storage Explorer. Expandera din Azure-prenumeration i Utforskaren om den inte redan har expanderats.

1. Expandera **lagringskonton** > {*ditt lagringskonto*} > **Blob Containers**. Välj din blob-behållare.

1. Välj **Ange offentlig åtkomstnivå**på snabbmenyn i blob-behållaren.

   * Om blob-behållaren har åtminstone offentlig åtkomst väljer du **Avbryt**och följer dessa steg senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access)

     ![Allmänhetens tillgång](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om blob-behållaren inte har offentlig åtkomst väljer du **Avbryt**och följer dessa steg senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#public-access)

     ![Ingen offentlig tillgång](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Ladda upp schemat till ditt lagringskonto. 
   I det högra fönstret väljer du **Ladda upp**.

1. När du har laddat upp det väljer du ditt uppladdade schema. Välj **Kopiera URL i** verktygsfältet så att du kopierar schemats URL.

1. Gå tillbaka till Azure-portalen där fönstret **Lägg till schema** är öppet. 
   Ange ett namn på sammansättningen. 
   Välj **Stor fil (större än 2 MB)**. 

   Rutan **Innehålls-URI** visas nu i stället **för rutan Schema.**

1. Klistra in schemats URL i rutan **Innehålls-URI.** 
   Slutför att lägga till schemat.

När schemat har laddats upp visas schemat i listan **Scheman.** På sidan Översikt **för** integrationskontot visar panelen **Schemas** nu antalet uppladdade scheman under **Komponenter.**

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Ladda upp schemat till ditt lagringskonto. 
   I det högra fönstret väljer du **Ladda upp**.

1. När du har laddat upp klart genererar du en SAS -signatur (Shared Access- för ditt schema. 
   På snabbmenyn i schemat väljer du **Hämta signatur för delad åtkomst**.

1. I fönstret Signatur för **delad åtkomst** väljer du Generera signatur för delad åtkomst **på behållarnivå URI** > **Create**. 
   När SAS-URL:en har genererats väljer du **Kopiera**bredvid **url-rutan** .

1. Gå tillbaka till Azure-portalen där fönstret **Lägg till schema** är öppet. Välj **Stor fil**.

   Rutan **Innehålls-URI** visas nu i stället **för rutan Schema.**

1. Klistra in den SAS URI som du tidigare har genererat i rutan **Innehålls-URI.** Slutför att lägga till schemat.

När schemat har laddats upp visas schemat i listan **Scheman.** På sidan Översikt **för** integrationskontot visar panelen **Schemas** nu antalet uppladdade scheman under **Komponenter.**

## <a name="edit-schemas"></a>Redigera scheman

Om du vill uppdatera ett befintligt schema måste du ladda upp en ny schemafil som har de ändringar du vill ha. Du kan dock först hämta det befintliga schemat för redigering.

1. I <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>söker du efter och öppnar ditt integrationskonto, om det inte redan är öppet.

1. På huvudmenyn i Azure väljer du **Alla tjänster**. 
   Skriv "integrationskonto" i sökrutan. 
   Välj **integrationskonton**.

1. Välj det integrationskonto där du vill uppdatera schemat.

1. Välj panelen Schemas under **Komponenter**på sidan **Översikt** för **integrationskontot.**

1. När sidan Scheman har **öppnats** väljer du ditt schema. 
   Om du vill hämta och redigera schemat först väljer du **Hämta**och sparar schemat.

1. När du är redo att ladda upp det uppdaterade schemat väljer du det schema som du vill uppdatera på sidan **Scheman** och väljer **Uppdatera**.

1. Sök efter och välj det uppdaterade schema som du vill ladda upp. 
   När schemafilen har laddats upp visas det uppdaterade schemat i **listan Scheman.**

## <a name="delete-schemas"></a>Ta bort scheman

1. I <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>söker du efter och öppnar ditt integrationskonto, om det inte redan är öppet.

1. På huvudmenyn i Azure väljer du **Alla tjänster**. 
   Skriv "integrationskonto" i sökrutan. 
   Välj **integrationskonton**.

1. Välj det integrationskonto där du vill ta bort schemat.

1. Välj panelen Schemas under **Komponenter**på sidan **Översikt** för **integrationskontot.**

1. När sidan Scheman har **öppnats** markerar du schemat och väljer **Ta bort**.

1. Om du vill bekräfta att du vill ta bort schemat väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Läs mer om kartor](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)
