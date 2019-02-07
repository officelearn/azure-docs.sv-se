---
title: Validera XML med scheman – Azure Logic Apps | Microsoft Docs
description: Lägga till scheman för att validera XML-dokument i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 03ac2e0f42ff05165aa2313d823710a71c7dffec
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768333"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validera XML med scheman i Azure Logic Apps med Enterprise-Integrationspaket

Om du vill kontrollera att dokument använder giltig XML och har förväntade data i det fördefinierade formatet för scenarion för enterprise-integration i Azure Logic Apps, kan din logikapp använda scheman. Ett schema kan du verifiera att meddelanden som logikappar exchange i business-to-business (B2B)-scenarier.

Gränser som rör integrationskonton och artefakter, till exempel scheman för finns i [begränsningar och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar dina scheman och andra artefakter för enterprise-integration och lösningar för business-to-business (B2B). 

  Om ditt schema är [2 MB eller mindre](#smaller-schema), du kan lägga till ditt schema ditt integrationskonto direkt från Azure-portalen. Men om ditt schema är större än 2 MB, men inte överstiger den [schemat storleksgränsen](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), du kan ladda upp schemat till ett Azure storage-konto. 
  Om du vill lägga till schemat i ditt integrationskonto, kan sedan du länka till ditt lagringskonto från din integrationskontot. 
  För den här uppgiften är här de objekt som du behöver: 

  * [Azure storage-konto](../storage/common/storage-account-overview.md) där du kan skapa en blob-behållare för ditt schema. Lär dig hur du [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md). 

  * BLOB-behållare för att lagra ditt schema. Lär dig hur du [har en blobbehållare](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Du behöver din behållare innehålls-URI senare när du lägger till schemat i ditt integrationskonto.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), som du kan använda för att hantera lagringskonton och blob-behållare. 
  Om du vill använda Storage Explorer väljer du något av alternativen här:
  
    * Hitta och välj ditt lagringskonto i Azure-portalen. 
    Lagringskontots meny, Välj **Lagringsutforskaren**.

    * Skrivbordsversionen, [ladda ned och installera Azure Storage Explorer](https://www.storageexplorer.com/). 
    Anslut sedan Storage Explorer till ditt storage-konto genom att följa stegen i [Kom igång med Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Mer information finns i [Snabbstart: Skapa en blob i objektlagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Du behöver inte en logikapp när du skapar och lägga till scheman. Men om du vill använda ett schema, din logikapp måste länka till ett integrationkonto där du lagrar schemat. Lär dig [så här länkar du logikappar till integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte har en logikapp ännu kan du lära dig [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Lägga till scheman

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

1. För att hitta och öppna ditt integrationskonto på Azures Huvudmeny, Välj **alla tjänster**. I sökrutan anger du ”integrationskontot”. Välj **integrationskonton**.

   ![Hitta integrationskontot](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Välj integrationskontot där du vill lägga till ditt schema, till exempel:

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **scheman** panelen.

   ![Välj ”scheman”](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Efter den **scheman** öppnas, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Baserat på schemat (.xsd)-filens storlek, följer du stegen för att ladda upp ett schema som antingen [upp till 2 MB](#smaller-schema) eller [mer än 2 MB, upp till 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Lägga till scheman upp till 2 MB

1. Under **Lägg till Schema**, ange ett namn för ditt schema. 
   Behåll **liten fil** valda. Bredvid den **schemat** väljer du mappikonen. Hitta och välj det schema som du laddar upp, till exempel:

   ![Ladda upp mindre schema](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. När du är klar kan du välja **OK**.

   När ditt schema har överfört, schemat visas i den **scheman** lista.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Lägga till scheman som är mer än 2 MB

Om du vill lägga till större scheman, kan du ladda upp ditt schema till en Azure blobbehållare i Azure storage-kontot. Dina steg för att lägga till kartor variera beroende på om din blobbehållare har offentlig läsbehörighet. Så först kontrollera om din blobbehållare har offentlig läsbehörighet genom att följa dessa steg: [Ange offentlig åtkomstnivå för blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrollera åtkomstnivån för behållare

1. Öppna Azure Storage Explorer. Expandera din Azure-prenumeration i Explorer-fönstret om du inte redan är expanderat.

1. Expandera **Lagringskonton** > {*your storage account*} > **Blobbehållare**. Välj din blobbehållare.

1. Från snabbmenyn för din blob-behållare och välj **ange offentlig åtkomstnivå**.

   * Om din blobbehållare har minst offentlig åtkomst, Välj **Avbryt**, och följ de här stegen senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access)

     ![Offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om blob-behållaren inte har offentlig åtkomst, Välj **Avbryt**, och följ de här stegen senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#public-access)

     ![Ingen offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Ladda upp schemat till ditt lagringskonto. 
   I det högra fönstret, väljer **överför**.

1. När du är klar med att ladda upp väljer du ditt överförda schema. I verktygsfältet, välja **Kopiera URL: en** så att du kopiera URL: en i schemat.

1. Gå tillbaka till Azure portal där de **Lägg till Schema** fönstret är öppet. 
   Ange ett namn för din sammansättningen. 
   Välj **stor fil (större än 2 MB)**. 

   Den **innehålls-URI** nu visas snarare än **schemat** box.

1. I den **innehålls-URI** rutan, klistra in URL: en för ditt schema. 
   Har lagt till ditt schema.

När ditt schema har överfört, schemat visas i den **scheman** lista. På ditt integrationskonto **översikt** sidan under **komponenter**, **scheman** panel visar nu antalet uppladdade scheman.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Ladda upp schemat till ditt lagringskonto. 
   I det högra fönstret, väljer **överför**.

1. När du är klar med att ladda upp Generera en signatur för delad åtkomst (SAS) för ditt schema. 
   Välj snabbmenyn för ditt schema och **hämta signatur för delad åtkomst**.

1. I den **signatur för delad åtkomst** väljer **URI för signatur för delad åtkomst för behållare på servernivå av generera** > **skapa**. 
   När SAS URL: en hämtar genereras vid den **URL** väljer **kopiera**.

1. Gå tillbaka till Azure portal där de **Lägg till Schema** fönstret är öppet. Välj **stora filer**.

   Den **innehålls-URI** nu visas snarare än **schemat** box.

1. I den **innehålls-URI** rutan, klistra in den SAS URI som du tidigare har skapats. Har lagt till ditt schema.

När ditt schema har överfört, schemat visas i den **scheman** lista. På ditt integrationskonto **översikt** sidan under **komponenter**, **scheman** panel visar nu antalet uppladdade scheman.

## <a name="edit-schemas"></a>Redigera scheman

Du måste ladda upp en ny schemafil som innehåller de ändringar som du vill för att uppdatera ett befintligt schema. Du kan dock först hämta det befintliga schemat för redigering.

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, hitta och öppna ditt integrationskonto, om inte redan är öppen.

1. Välj på Azure-huvudmenyn **alla tjänster**. 
   I sökrutan anger du ”integrationskontot”. 
   Välj **integrationskonton**.

1. Välj integrationskontot där du vill uppdatera ditt schema.

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **scheman** panelen.

1. Efter den **scheman** öppnas, Välj ditt schema. 
   Om du vill ladda ned och redigera schemat först, Välj **hämta**, och spara schemat.

1. När du är redo att överföra det uppdaterade schemat på den **scheman** väljer du det schema du vill uppdatera och välj **uppdatera**.

1. Hitta och välja det uppdaterade schemat som du vill ladda upp. 
   När din schemafilen har överfört, det uppdaterade schemat visas i den **scheman** lista.

## <a name="delete-schemas"></a>Ta bort scheman

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, hitta och öppna ditt integrationskonto, om inte redan är öppen.

1. Välj på Azure-huvudmenyn **alla tjänster**. 
   I sökrutan anger du ”integrationskontot”. 
   Välj **integrationskonton**.

1. Välj integrationskontot där du vill ta bort ditt schema.

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **scheman** panelen.

1. Efter den **scheman** öppnas, Välj ditt schema och välj **ta bort**.

1. För att bekräfta att du vill ta bort schemat, Välj **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md)
* [Läs mer om maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)