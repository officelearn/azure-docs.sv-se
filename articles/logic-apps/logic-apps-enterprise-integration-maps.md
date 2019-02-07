---
title: Transformera XML med XSLT-kartor – Azure Logic Apps | Microsoft Docs
description: Lägga till XSLT-kartor för att transformera XML i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: da5b099a5574d34c3676819c930f3e89610cf4ad
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767443"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformera XML med kartor i Azure Logic Apps med Enterprise-Integrationspaket

För att överföra XML-data mellan format för scenarion för enterprise-integration i Azure Logic Apps, din logikapp kan använda maps eller mer specifikt mappar formatmall transformationer XSLT (Extensible Language). En karta är ett XML-dokument som beskriver hur du omvandlar data från ett XML-dokument till ett annat format. 

Anta exempelvis att du regelbundet ta emot B2B order eller fakturor från en kund som använder YYYMMDD datumformat. Din organisation använder dock MMDDYYY datumformat. Du kan definiera och använder en karta som omvandlar YYYMMDD datumformat till formatet MMDDYYY innan du lagrar information om ordning eller faktura i kunddatabasen för aktiviteten.

Begränsningar som rör integrationskonton och artefakter som maps, se [begränsningar och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar dina kartor och andra artefakter för enterprise-integration och lösningar för business-to-business (B2B).

* Om kartan refererar till en extern sammansättning, måste du ladda upp *både sammansättningen och kartan* till ditt integrationskonto. Se till att du *ladda upp sammansättningen först*, och överför sedan kartan som refererar till sammansättningen.

  Om sammansättningen är 2 MB eller mindre, du kan lägga till sammansättningen i ditt integrationskonto *direkt* från Azure-portalen. Men om sammansättningen eller kartan är större än 2 MB men inte större än den [storleksgräns för sammansättningar eller maps](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), finns följande alternativ:

  * För sammansättningar behöver du en Azure blob-behållare där du kan ladda upp sammansättningen och platsen för den behållaren. På så sätt kan du ange den platsen senare när du lägger till sammansättningen i ditt integrationskonto. 
  För den här uppgiften behöver du följande objekt:

    | Objekt | Beskrivning |
    |------|-------------|
    | [Azure Storage-konto](../storage/common/storage-account-overview.md) | I det här kontot, skapar du en Azure blob-behållare för dina sammansättningen. Lär dig [hur du skapar ett lagringskonto](../storage/common/storage-quickstart-create-account.md). |
    | BLOB-behållare | Du kan ladda upp sammansättningen i den här behållaren. Du måste också den här behållaren plats när du lägger till sammansättningen i ditt integrationskonto. Lär dig hur du [har en blobbehållare](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Det här verktyget kan du enkelt kan hantera lagringskonton och blob-behållare. Använda Storage Explorer antingen [ladda ned och installera Azure Storage Explorer](https://www.storageexplorer.com/). Anslut sedan Storage Explorer till ditt storage-konto genom att följa stegen i [Kom igång med Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md). Mer information finns i [Snabbstart: Skapa en blob i objektlagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Eller, i Azure-portalen, hitta och välj ditt lagringskonto. Lagringskontots meny, Välj **Lagringsutforskaren**. |
    |||

  * För kartor, du kan för närvarande lägga till större maps med hjälp av den [Azure Logic Apps REST API - mappar](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Du behöver inte en logikapp när du skapar och lägger till maps. Men om du vill använda en karta, din logikapp måste länka till ett integrationkonto där du lagrar som mappar. Lär dig [så här länkar du logikappar till integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte har en logikapp ännu kan du lära dig [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-referenced-assemblies"></a>Lägg till refererade sammansättningar

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

1. För att hitta och öppna ditt integrationskonto på Azures Huvudmeny, Välj **alla tjänster**. 
   I sökrutan anger du ”integrationskontot”. 
   Välj **integrationskonton**.

   ![Hitta integrationskontot](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj integrationskontot där du vill lägga till din sammansättning, till exempel:

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **sammansättningar** panelen.

   ![Välj ”sammansättningar”](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Efter den **sammansättningar** öppnas, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Utifrån din sammansättningsfilen storlek, följer du stegen för att ladda upp en sammansättning som antingen [upp till 2 MB](#smaller-assembly) eller [mer än 2 MB men bara upp till 8 MB](#larger-assembly).
Gränser för antalet sammansättningen i integrationskonton, se [gränser och konfigurering för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Lägg till sammansättningar upp till 2 MB

1. Under **lägga till sammansättningen**, ange ett namn för sammansättningen. Behåll **liten fil** valda. Bredvid den **sammansättningen** väljer du mappikonen. Hitta och välj den sammansättning som du laddar upp, till exempel:

   ![Ladda upp mindre sammansättningen](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   I den **sammansättningsnamn** egenskapen sammansättningsnamn filen visas automatiskt när du har valt sammansättningen.

1. När du är klar kan du välja **OK**.

   När din sammansättningsfilen har överfört, sammansättningen visas i den **sammansättningar** lista.

   ![Överförda sammansättningar lista](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   På ditt integrationskonto **översikt** sidan under **komponenter**, **sammansättningar** panel visar nu antalet uppladdade sammansättningar, till exempel:

   ![Överförda sammansättningar](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Lägg till sammansättningar som är mer än 2 MB

Om du vill lägga till större sammansättningar, kan du ladda upp sammansättningen till en Azure blobbehållare i Azure storage-kontot. Dina steg för att lägga till sammansättningar skiljer sig beroende om blob-behållare har offentlig läsbehörighet. Så först kontrollera om din blobbehållare har offentlig läsbehörighet genom att följa dessa steg: [Ange offentlig åtkomstnivå för blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrollera åtkomstnivån för behållare

1. Öppna Azure Storage Explorer. Expandera din Azure-prenumeration i Explorer-fönstret om du inte redan är expanderat.

1. Expandera **Lagringskonton** > {*your storage account*} > **Blobbehållare**. Välj din blobbehållare.

1. Från snabbmenyn för din blob-behållare och välj **ange offentlig åtkomstnivå**.

   * Om din blobbehållare har minst offentlig åtkomst, Välj **Avbryt**, och följ de här stegen senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access-assemblies)

     ![Offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om blob-behållaren inte har offentlig åtkomst, Välj **Avbryt**, och följ de här stegen senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#no-public-access-assemblies)

     ![Ingen offentlig åtkomst](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagringskonto. 
   I det högra fönstret, väljer **överför**.

1. När du är klar med att ladda upp väljer du överförda sammansättningen. I verktygsfältet, välja **Kopiera URL: en** så att du kopierar den sammansättningen URL: en.

1. Gå tillbaka till Azure portal där de **lägga till sammansättningen** fönstret är öppet. 
   Ange ett namn för din sammansättningen. 
   Välj **stor fil (större än 2 MB)**.

   Den **innehålls-URI** nu visas snarare än **sammansättningen** box.

1. I den **innehålls-URI** rutan, klistra in URL: en för din sammansättningen. 
   Har lagt till sammansättningen.

När sammansättningen har överfört, schemat visas i den **sammansättningar** lista.
På ditt integrationskonto **översikt** sidan under **komponenter**, **sammansättningar** panel visar nu antalet uppladdade sammansättningar.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagringskonto. 
   I det högra fönstret, väljer **överför**.

1. När du är klar med att ladda upp kan du generera en signatur för delad åtkomst (SAS) för sammansättningen. 
   Din sammansättningen snabbmenyn och välj **hämta signatur för delad åtkomst**.

1. I den **signatur för delad åtkomst** väljer **URI för signatur för delad åtkomst för behållare på servernivå av generera** > **skapa**. 
   När SAS URL: en hämtar genereras vid den **URL** väljer **kopiera**.

1. Gå tillbaka till Azure portal där de **lägga till sammansättningen** fönstret är öppet. 
   Ange ett namn för din sammansättningen. 
   Välj **stor fil (större än 2 MB)**.

   Den **innehålls-URI** nu visas snarare än **sammansättningen** box.

1. I den **innehålls-URI** rutan, klistra in den SAS URI som du tidigare har skapats. Har lagt till sammansättningen.

När sammansättningen har överfört, sammansättningen visas i den **scheman** lista. På ditt integrationskonto **översikt** sidan under **komponenter**, **sammansättningar** panel visar nu antalet uppladdade sammansättningar.

## <a name="create-maps"></a>Skapa kartor

Om du vill skapa ett XSLT-dokument som du kan använda som en karta, kan du använda Visual Studio 2015 för att skapa en BizTalk-integrering-projekt med hjälp av den [Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md). Du kan skapa mappningsfilen integrering som låter dig visuellt mappa objekt mellan två XML schema-filer i det här projektet. När du har skapat det här projektet kan få du ett XSLT-dokument.
Gränser på kartan antalet i integrationskonton för finns i [gränser och konfigurering för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Lägga till kartor

Du kan nu överföra kartan när du har överfört alla sammansättningar som refererar till kartan.

1. Om du inte har loggat in redan, logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> med dina Azure-autentiseringsuppgifter. 

1. Om ditt integrationskonto inte redan är öppen på Azures Huvudmeny väljer **alla tjänster**. 
   I sökrutan anger du ”integrationskontot”. 
   Välj **integrationskonton**.

   ![Hitta integrationskontot](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj integrationskontot där du vill lägga till kartan, till exempel:

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **Maps** panelen.

   ![Välj ”Maps”](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Efter den **Maps** öppnas, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Lägga till kartor upp till 2 MB

1. Under **Lägg till karta**, ange ett namn på kartan. 

1. Under **Mappningstyp**, Välj typ, till exempel: **Flytande**, **XSLT**, **XSLT 2.0**, eller **XSLT 3.0**.

1. Behåll **liten fil** valda. Bredvid den **kartan** väljer du mappikonen. Hitta och välj kartan du laddar upp, till exempel:

   ![Ladda upp karta](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Om du lämnar den **namn** egenskapen tom, namn på kartan visas automatiskt i egenskapen automatiskt när du har valt i mappningsfilen. 
   Du kan dock använda ett unikt namn.

1. När du är klar kan du välja **OK**. 
   När din mappningsfilen har överfört, kartan visas i den **Maps** lista.

   ![Överförda maps-lista](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   På ditt integrationskonto **översikt** sidan under **komponenter**, **mappar** panel visar nu antalet uppladdade kartor, till exempel:

   ![Överförda maps](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Lägg till kartor mer än 2 MB

För närvarande kan lägga till större maps, använda den [Azure Logic Apps REST API - mappar](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Redigera mappningar

Om du vill uppdatera en befintlig karta som du behöver ladda upp en ny karta-fil som innehåller de ändringar som du vill. Du kan dock först hämta karta för redigering.

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, hitta och öppna ditt integrationskonto, om inte redan är öppen.

1. Välj på Azure-huvudmenyn **alla tjänster**. I sökrutan anger du ”integrationskontot”. Välj **integrationskonton**.

1. Välj integrationskontot där du vill uppdatera kartan.

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **Maps** panelen.

1. Efter den **Maps** öppnas, Välj kartan. 
   Om du vill ladda ned och redigera kartan först, Välj **hämta**, och spara kartan.

1. När du är redo att överföra den uppdaterade kartan på den **Maps** väljer kartan som du vill uppdatera och välj **uppdatera**.

1. Hitta och välj den uppdaterade karta som du vill ladda upp. 
   När din mappningsfilen har överfört, uppdaterade kartan visas i den **Maps** lista.

## <a name="delete-maps"></a>Ta bort mappar

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, hitta och öppna ditt integrationskonto, om inte redan är öppen.

1. Välj på Azure-huvudmenyn **alla tjänster**. 
   I sökrutan anger du ”integrationskontot”. 
   Välj **integrationskonton**.

1. Välj integrationskontot där du vill ta bort kartan.

1. På ditt integrationskonto **översikt** sidan under **komponenter**väljer den **Maps** panelen.

1. Efter den **Maps** öppnas, Välj kartan och välj **ta bort**.

1. För att bekräfta att du vill ta bort kartan, Välj **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Läs mer om scheman](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)