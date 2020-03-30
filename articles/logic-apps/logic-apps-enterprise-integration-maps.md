---
title: Omvandla XML med XSLT-kartor
description: Lägga till XSLT-mappningar för att omvandla XML i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979411"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformera XML med kartor i Azure Logic Apps med Enterprise-integrationspaket

Om du vill överföra XML-data mellan format för företagsintegreringsscenarier i Azure Logic Apps kan logikappen använda kartor, eller mer specifikt, XSLT-kartor (Extensible Style Sheet Language Transformations). En karta är ett XML-dokument som beskriver hur du konverterar data från ett XML-dokument till ett annat format. 

Anta till exempel att du regelbundet får B2B-order eller fakturor från en kund som använder YYYMMDD-datumformatet. Din organisation använder dock MMDDYYY-datumformatet. Du kan definiera och använda en karta som omvandlar YYYMMDD-datumformatet till MMDDYYY-formatet innan du lagrar order- eller fakturainformationen i kundaktivitetsdatabasen.

Begränsningar som är relaterade till integrationskonton och artefakter som kartor finns i [Gränser och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du lagrar dina kartor och andra artefakter för företagsintegration och B2B-lösningar (Business-to-Business).

* Om kartan refererar till en extern sammansättning måste du ladda upp *både sammansättningen och kartan* till ditt integrationskonto. Se till att du [*laddar upp sammansättningen först*](#add-assembly)och ladda sedan upp kartan som refererar till sammansättningen.

  Om din sammansättning är 2 MB eller mindre kan du lägga till din sammansättning i ditt integrationskonto *direkt* från Azure-portalen. Men om din sammansättning eller karta är större än 2 MB men inte större än [storleksgränsen för sammansättningar eller kartor](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)har du följande alternativ:

  * För sammansättningar behöver du en Azure blob-behållare där du kan ladda upp din sammansättning och behållarens plats. På så sätt kan du ange den platsen senare när du lägger till sammansättningen i ditt integrationskonto. 
  För den här uppgiften behöver du följande objekt:

    | Objekt | Beskrivning |
    |------|-------------|
    | [Azure-lagringskonto](../storage/common/storage-account-overview.md) | Skapa en Azure blob-behållare för din sammansättning i det här kontot. Läs om hur du [skapar ett lagringskonto](../storage/common/storage-account-create.md). |
    | Blobcontainer | I den här behållaren kan du ladda upp din sammansättning. Du behöver också den här behållarens plats när du lägger till sammansättningen i ditt integrationskonto. Lär dig hur du [skapar en blob-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Utforskaren för Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Det här verktyget hjälper dig att enklare hantera lagringskonton och blob-behållare. Om du vill använda Storage Explorer hämtar och installerar du [Azure Storage Explorer](https://www.storageexplorer.com/). Anslut sedan Storage Explorer till ditt lagringskonto genom att följa stegen i [Komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Mer information finns [i Snabbstart: Skapa en blob i objektlagring med Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Eller i Azure-portalen, hitta och välj ditt lagringskonto. Välj **Lagringskonto**på menyn för lagringskonto . |
    |||

  * För kartor kan du för närvarande lägga till större kartor med hjälp av [AZURE Logic Apps REST API - Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Du behöver ingen logikapp när du skapar och lägger till kartor. Om du vill använda en karta behöver logikappen länka till ett integrationskonto där du lagrar kartan. Läs om hur du [länkar logikappar till integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Om du inte har någon logikapp ännu kan du läsa om hur du [skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Lägga till refererade sammansättningar

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. Om du vill söka efter och öppna ditt integrationskonto väljer du **Alla tjänster**på Azure-menyn. 
   Skriv "integrationskonto" i sökrutan. 
   Välj **integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj det integrationskonto där du vill lägga till din sammansättning, till exempel:

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Välj panelen Sammansättningar under **Komponenter**på sidan **Översikt** för integrationskontot. **Assemblies**

   ![Välj "Sammansättningar"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. När sidan **Sammansättningar** har öppnats väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Baserat på monteringsfilens storlek följer du stegen för att ladda upp en sammansättning som antingen är [upp till 2 MB](#smaller-assembly) eller mer än [2 MB men bara upp till 8 MB](#larger-assembly).
Begränsningar för monteringskvantiteter i integrationskonton finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Om du ändrar sammansättningen måste du också uppdatera kartan oavsett om kartan har ändrats eller inte.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Lägg till sammansättningar på upp till 2 MB

1. Ange ett namn på sammansättningen under **Lägg till**sammansättning. Håll **liten fil** markerad. Välj mappikonen bredvid rutan **Montering.** Hitta och välj den sammansättning du laddar upp, till exempel:

   ![Ladda upp mindre sammansättning](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   I egenskapen **Monteringsnamn** visas sammansättningens filnamn automatiskt när du har valt sammansättningen.

1. När du är redo väljer du **OK**.

   När monteringsfilen har laddats upp visas sammansättningen i listan **Sammansättningar.**

   ![Lista över uppladdade sammansättningar](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   På sidan Översikt **för** integrationskontot visar panelen **Sammansättningar** nu antalet uppladdade sammansättningar under **Komponenter:**

   ![Uppladdade sammansättningar](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Lägga till sammansättningar mer än 2 MB

Om du vill lägga till större sammansättningar kan du ladda upp din sammansättning till en Azure-blob-behållare i ditt Azure-lagringskonto. Dina steg för att lägga till sammansättningar skiljer sig åt beroende på om din blob-behållare har offentlig läsbehörighet. Så först, kontrollera om din blob-behållare har offentlig läsåtkomst genom att följa dessa steg: [Ange offentlig åtkomstnivå för blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrollera åtkomstnivå för behållare

1. Öppna Azure Storage Explorer. Expandera din Azure-prenumeration i Utforskaren om den inte redan har expanderats.

1. Expandera **lagringskonton** > {*ditt lagringskonto*} > **Blob Containers**. Välj din blob-behållare.

1. Välj **Ange offentlig åtkomstnivå**på snabbmenyn i blob-behållaren.

   * Om blob-behållaren har åtminstone offentlig åtkomst väljer du **Avbryt**och följer dessa steg senare på den här sidan: [Ladda upp till behållare med offentlig åtkomst](#public-access-assemblies)

     ![Allmänhetens tillgång](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Om blob-behållaren inte har offentlig åtkomst väljer du **Avbryt**och följer dessa steg senare på den här sidan: [Ladda upp till behållare utan offentlig åtkomst](#no-public-access-assemblies)

     ![Ingen offentlig tillgång](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Ladda upp till behållare med offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagringskonto. 
   I det högra fönstret väljer du **Ladda upp**.

1. När du har laddat upp dig väljer du den uppladdade sammansättningen. Välj **Kopiera URL i** verktygsfältet så att du kopierar sammansättningens URL.

1. Gå tillbaka till Azure-portalen där fönstret **Lägg till sammansättning** är öppet. 
   Ange ett namn på sammansättningen. 
   Välj **Stor fil (större än 2 MB)**.

   Rutan **Innehålls-URI** visas nu i stället för rutan **Montering.**

1. Klistra in sammansättningens URL i rutan **Innehålls-URI.** 
   Slutför sammansättningen.

När sammansättningen har laddats upp visas schemat i listan **Sammansättningar.**
På sidan Översikt **för** integrationskontot visar panelen **Sammansättningar** nu antalet uppladdade sammansättningar under **Komponenter.**

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Ladda upp till behållare utan offentlig åtkomst

1. Ladda upp sammansättningen till ditt lagringskonto. 
   I det högra fönstret väljer du **Ladda upp**.

1. När du har laddat upp klart genererar du en SAS -signatur (Shared Access Signature) för din sammansättning. 
   Välj Hämta signatur för **delad åtkomst**på sammansättningens snabbmeny.

1. I fönstret Signatur för **delad åtkomst** väljer du Generera signatur för delad åtkomst **på behållarnivå URI** > **Create**. 
   När SAS-URL:en har genererats väljer du **Kopiera**bredvid **url-rutan** .

1. Gå tillbaka till Azure-portalen där fönstret **Lägg till sammansättning** är öppet. 
   Ange ett namn på sammansättningen. 
   Välj **Stor fil (större än 2 MB)**.

   Rutan **Innehålls-URI** visas nu i stället för rutan **Montering.**

1. Klistra in den SAS URI som du tidigare har genererat i rutan **Innehålls-URI.** Slutför sammansättningen.

När sammansättningen har laddats upp visas sammansättningen i listan **Scheman.** På sidan Översikt **för** integrationskontot visar panelen **Sammansättningar** nu antalet uppladdade sammansättningar under **Komponenter.**

## <a name="create-maps"></a>Skapa kartor

Om du vill skapa ett XSLT-dokument som du kan använda som karta kan du använda Visual Studio 2015 för att skapa ett BizTalk-integrationsprojekt med hjälp av [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). I det här projektet kan du skapa en integrationsmappningsfil, där du visuellt kan mappa objekt mellan två XML-schemafiler. När du har byggt det här projektet får du ett XSLT-dokument.
Begränsningar för mappningsmängder i integrationskonton finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Lägg till kartor

När du har laddat upp alla sammansättningar som kartan refererar till kan du nu ladda upp kartan.

1. Om du inte redan har loggat in loggar du in på [Azure-portalen](https://portal.azure.com) med dina Azure-kontouppgifter. 

1. Om ditt integrationskonto inte redan är öppet väljer du **Alla tjänster**på Azure-huvudmenyn . 
   Skriv "integrationskonto" i sökrutan. 
   Välj **integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Välj det integrationskonto där du vill lägga till kartan, till exempel:

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Välj panelen **Kartor** under **Komponenter**på sidan **Översikt** för integrationskontot.

   ![Välj "Kartor"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. När sidan **Kartor** har öppnats väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Lägga till kartor på upp till 2 MB

1. Under **Lägg till karta**anger du ett namn på kartan. 

1. Under **Karttyp**väljer du typen, till exempel: **Vätska**, **XSLT**, **XSLT 2.0**eller **XSLT 3.0**.

1. Håll **liten fil** markerad. Välj mappikonen bredvid rutan **Karta.** Hitta och välj den karta du laddar upp, till exempel:

   ![Ladda upp karta](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Om du lämnade egenskapen **Namn** tomt visas kartans filnamn automatiskt i den egenskapen när du har valt kartfilen. 
   Du kan dock använda vilket unikt namn som helst.

1. När du är redo väljer du **OK**. 
   När kartfilen har laddats upp visas kartan i listan **Kartor.**

   ![Lista över uppladdade kartor](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   På sidan Översikt **för** integrationskontot visar panelen **Kartor** nu antalet uppladdade kartor, till exempel: **Components**

   ![Uppladdade kartor](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Lägga till kartor mer än 2 MB

För närvarande, för att lägga till större kartor, använd [Azure Logic Apps REST API - Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

## <a name="edit-maps"></a>Redigera kartor

Om du vill uppdatera en befintlig karta måste du ladda upp en ny kartfil som har de ändringar du vill ha. Du kan dock först hämta den befintliga kartan för redigering.

1. I [Azure-portalen](https://portal.azure.com)söker du efter och öppnar ditt integrationskonto, om det inte redan är öppet.

1. På huvudmenyn i Azure väljer du **Alla tjänster**. Skriv "integrationskonto" i sökrutan. Välj **integrationskonton**.

1. Välj det integrationskonto där du vill uppdatera kartan.

1. Välj panelen **Kartor** under **Komponenter**på sidan **Översikt** för integrationskontot.

1. När sidan **Kartor** har öppnats väljer du kartan. 
   Om du vill hämta och redigera kartan först väljer du **Hämta**och sparar kartan.

1. När du är redo att ladda upp den uppdaterade kartan väljer du den karta du vill uppdatera på sidan **Kartor** och väljer **Uppdatera**.

1. Hitta och välj den uppdaterade kartan som du vill ladda upp. 
   När kartfilen har laddats upp visas den uppdaterade kartan i listan **Kartor.**

## <a name="delete-maps"></a>Ta bort kartor

1. I [Azure-portalen](https://portal.azure.com)söker du efter och öppnar ditt integrationskonto, om det inte redan är öppet.

1. På huvudmenyn i Azure väljer du **Alla tjänster**. 
   Skriv "integrationskonto" i sökrutan. 
   Välj **integrationskonton**.

1. Välj det integrationskonto där du vill ta bort kartan.

1. Välj panelen **Kartor** under **Komponenter**på sidan **Översikt** för integrationskontot.

1. När sidan **Kartor** har öppnats väljer du kartan och väljer **Ta bort**.

1. Om du vill bekräfta att du vill ta bort kartan väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Läs mer om scheman](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Läs mer om transformeringar](../logic-apps/logic-apps-enterprise-integration-transform.md)
