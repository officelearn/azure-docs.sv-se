---
title: Fråga Azure Cognitive Search från Power Apps
titleSuffix: Azure Cognitive Search
description: Steg-för-steg-vägledning om hur du skapar anpassad koppling till Kognitiv sökning och hur du visualiserar den från en Power App
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385120"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Så här frågar du ett kognitivt sökindex från Power Apps

Det här dokumentet visar hur du skapar en anpassad Power Apps-anslutningsapp så att du kan hämta sökresultat från ett sökindex. Den visar också hur du utfärdar en sökfråga och visualiserar resultaten från en Power App. 

## <a name="prerequisites"></a>Krav
*    Power Apps-kontoåtkomst med möjlighet att skapa anpassade kopplingar.
*    Vi antar att du redan har skapat ett Azure Search Index.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Skapa en anpassad anslutningsapp för att fråga Azure Search

Det finns två huvudsteg för att ha en PowerApp som visar Azure Cognitive Search results. Låt oss först skapa en koppling som kan fråga sökindexet. I [nästa avsnitt](#visualize-results-from-the-custom-connector) uppdaterar vi ditt Power Apps-program för att visualisera de resultat som returneras av kopplingen.

1. Gå till [make.powerapps.com](http://make.powerapps.com) och **logga in**.

1. Sök efter**anpassade datakopplingar** **Data** > 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Anpassad anslutningsmeny" border="true":::

1. Klicka på **+ Ny anpassad koppling** och välj sedan Skapa från **tom**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Skapa från tom meny" border="true":::

1. Ge din anpassade koppling ett namn. (det vill än *AzureSearchQuery*) och klicka sedan på **Fortsätt**. Detta kommer att visa en guide för att skapa din nya koppling.

1. Ange information på den allmänna sidan.

    - Ikonbakgrundsfärg (till exempel #007ee5)
    - Beskrivning (till exempel "En anslutning till Azure Cognitive Search")
    - I värden måste du ange webbadressen till söktjänsten `<yourservicename>.search.windows.net`(till exempel)
    - För grundläggande URL, skriv bara "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Allmän informationsdialog" border="true":::

1. Ange *API-nyckel* som **autentiseringstyp**på säkerhetssidan, ange parameteretikett och parameternamnfält som *api-key*. För **Parameterplats**väljer du *Rubrik* som visas nedan.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Alternativet Autentiseringstyp" border="true":::

1. På sidan Definitioner väljer du **+ Ny åtgärd** för att skapa en åtgärd som frågar efter indexet. Ange värdet "Fråga" för sammanfattningen och namnet på operations-ID. Ange en beskrivning som *"Frågar sökindexet"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nya åtgärdsalternativ" border="true":::


1. Tryck på knappen **Importera från exempel** för att definiera parametrar och rubriker. Därefter definierar du frågebegäran.  

    * Markera verbet`GET`
    * För URL:en anger du en exempelfråga för ditt sökindex, till exempel:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-version=2019-05-06-Preview
    

    **Syntaxen** används i power apps för att extrahera parametrar från frågan. Observera att vi uttryckligen definierade sökfältet. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importera från exempel" border="false":::

1.  Klicka på **Importera** om du vill fylla i dialogrutan Begär automatiskt.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importera från exempeldialog" border="false":::


1. Slutför inställningen parametermetadata genom att klicka på **...** bredvid var och en av parametrarna.

    - För *sökning* `*` : Ange som **standardvärde**anger du **obligatoriskt** som *falskt* och anger **synligheten** till *ingen*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadata för sökparameter" border="true":::

    - För *api-version* `2019-05-06-Preview` : Ange som **standardvärde**anger du **synligheten** som intern och ställer in **obligatoriskt** för *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadata för versionsparameter" border="true":::

    - På samma sätt, för *api-nyckel*, ange det **som krävs**, med *intern* **synlighet**. Ange API-nyckeln för söktjänsten som **standardvärde**.
    
    När du har gjort dessa ändringar växlar du till **vyn Swagger Editor.** I avsnittet parametrar bör du se följande konfiguration:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. Klicka på **"Lägg till standardsvar"** i avsnittet Svar . Detta är viktigt eftersom det hjälper **Power Apps** att förstå schemat för svaret. Klistra in ett exempelsvar.

    > [!TIP] 
    > Det finns en teckengräns för det JSON-svar du kan ange, så du kanske vill förenkla JSON så att den innan du klistrar in den. Svarets viktiga aspektschema/format. De faktiska värdena i exempelsvaret är mindre viktiga och kan förenklas för att minska antalet tecken.
    

1.    Klicka på knappen **Skapa koppling** längst upp till höger på skärmen innan du kan testa den.

1.  På testsidan klickar du på **+ Ny anslutning**och anger söktjänstfrågenyckeln som värde för *api-key*.

    Det här steget kan ta dig till ut ur guiden och in på sidan Anslutningar. Du kanske vill gå tillbaka till Redigeraren för anpassade anslutningar för att faktiskt testa anslutningen. Gå till **Anpassad anslutning** > Välj den nyskapade anslutnings- > *...* > **Visa egenskaper** > **Redigera** > **4. Testa** för att komma tillbaka till testsidan.

1.    Klicka nu på **Testa för** att se till att du får resultat från indexet. Om du lyckades bör du se en 200-status, och i brödtexten i svaret bör du se JSON som beskriver dina sökresultat.




## <a name="visualize-results-from-the-custom-connector"></a>Visualisera resultat från den anpassade kopplingen
Målet med den här självstudien är inte att visa dig hur du skapar snygga användarupplevelser med power-appar, så användargränssnittets layout blir minimalistisk. Nu ska vi skapa en PowerApp med en sökruta, en sökknapp och visa resultaten i en gallerikontroll.  PowerApp ansluter till vår nyligen skapade anpassade anslutningsapp för att hämta data från Azure Search.

1. Skapa en ny Power App. Gå till avsnittet **Appar,** klicka på **+ Ny app**och välj **Canvas**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Skapa app på arbetsytan" border="true":::

1. Välj den typ av program du vill ha. För den här självstudien skapa en **tom app** med **telefonlayouten**. **Power Apps Studio** visas.

1. En gång i studion väljer du fliken **Datakällor** och klickar på den nya anslutningsappen som du just har skapat. I vårt fall kallas det *AzureSearchQuery*. Klicka på **Lägg till en anslutning**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="ansluta kontakten" border="true":::

    Nu är *AzureSearchQuery* en datakälla som är tillgänglig för användning från ditt program.
    
1. Navigera till **fliken Infoga**, så att vi kan lägga till några kontroller i vårt formulär.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Infoga kontroller" border="true":::

1.  Infoga följande element:
    -   En textetikett med värdet "Fråga:"
    -   Ett textinmatningselement (kalla det *txtQuery*, standardvärde: "*")
    -   En knapp med texten "Sök" 
    -   Ett lodrätt galleri som heter (kalla det *galleriResultat)*
    
    Formuläret ska se ut ungefär så här:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layouten Kontroller" border="true":::

1. Om du vill att **sökknappen** ska utfärda en fråga markerar du knappen och Klistra in följande åtgärd för att ta **på OnSelect:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Knappen OnSelect" border="true":::
 
    Den här åtgärden gör att knappen uppdaterar en ny samling som kallas *azResult* med resultatet av sökfrågan, med hjälp av texten i textrutan *txtQuery* som frågeterm.
    
1.  Som ett nästa steg kommer vi att länka det vertikala galleriet vi skapade till *azResult-samlingen.* Markera gallerikontrollen och utför följande åtgärder i egenskapsfönstret.

    -  Ange **DataSource** till *azResult*.
    
    -  Välj en **layout** som fungerar för dig baserat på typen av data i indexet. I det här fallet använde vi *titel, underrubrik och* karosslayout.
    
    -  **Redigera fält**och markera de fält som du vill visualisera.

    Eftersom vi gav ett exempelresultat när vi definierade kopplingen är appen medveten om de fält som är tillgängliga i ditt index.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Gallerifält" border="true":::   
 
1.  Tryck på **F5** för att förhandsgranska appen.  

    Kom ihåg att fälten kan ställas in på beräknade värden.      
    Om du till exempel anger layouten *"Bild, Titel och Undertext"* och anger *bildfunktionen* som sammanfogning av rotsökvägen för data och filnamnet (till `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`exempel) kommer resultatet nedan att ge resultatet.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Appen är klar" border="true":::        

## <a name="next-steps"></a>Nästa steg

Mer information och onlineutbildning finns i [Power Apps Learning Catalog](https://docs.microsoft.com/powerapps/learning-catalog/get-started).

