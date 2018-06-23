---
title: 'Bing anpassad sökning: Skapa en anpassad sökning webbsida | Microsoft Docs'
description: Beskriver hur du konfigurerar en anpassad sökning-instans och integrera den med en webbsida
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352896"
---
# <a name="build-a-custom-search-web-page"></a>Skapa en anpassad sökning webbsida
Bing anpassad sökning kan du skapa en anpassad sökning upplevelser efter avsnitt som intresserar dig. Till exempel om du har en Kampsport webbplats som ger en sökinställningar kan kan du ange domäner, underplatser och webbsidor som Bing söker. Användarna ser sökresultat som är skräddarsydda för det innehåll som de är intresserad av i stället för bläddring genom allmänna sökresultat som kan innehålla irrelevanta innehåll. 

Den här kursen visar hur du konfigurerar en anpassad sökning-instans och integrera den med en ny webbsida.

Uppgifter som beskrivs är:

> [!div class="checklist"]
> - Skapa en anpassad sökning-instans
> - Lägg till aktiva poster
> - Lägga till blockerade poster
> - Lägga till fästa poster
> - Integrera anpassad sökning i en webbsida

## <a name="prerequisites"></a>Förutsättningar
- Om du vill följa kursen behöver du en prenumeration för Bing anpassad sökning-API: t.  Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**.

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad sökning-instans
Skapa en anpassad sökning Bing-instans:

1.  Öppna en webbläsare.
2.  Navigera till den anpassade sökningen [portal](https://customsearch.ai).
3.  Logga in på portalen med ett Microsoft-konto (MSA). Om du inte har en MSA, klickar du på **skapa ett Microsoft-konto**. Om det är första gången du använder portalen, begär den behörighet att komma åt dina data. Klicka på **Ja**.
4.  När du har loggat in klickar du på **ny anpassad sökning**. I den **skapa en ny anpassad sökning instans** och ange ett namn som är meningsfullt och beskriver typ av innehåll som returnerar sökningen. Du kan ändra namnet när som helst.
 
    ![Skärmbild för att skapa en ny anpassad instans sökrutan](../media/newCustomSrch.png)

5.  Klicka på OK och anger en URL och om du vill inkludera underordnade sidor på bassidan:

    ![Skärmbild av sidan för URL-definition](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Lägg till aktiva poster
Om du vill inkludera resultat från specifika platser eller URL: er, lägga till dem i den **Active** fliken.

1.  I den **Definition Editor**, klicka på den **Active** och ange Webbadressen till en eller flera platser som du vill inkludera i din sökning.

    ![Skärmbild av fliken Definition Editor active](../media/customSrchEditor.png)

2.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Bing returnerar resultat endast för offentliga platser som den har indexerats.

## <a name="add-blocked-entries"></a>Lägga till blockerade poster
Om du vill utesluta resultat från specifika platser eller URL: er, lägga till dem i den **blockerad** fliken.

1. I den **Definition Editor**, klicka på den **blockerad** och ange Webbadressen till en eller flera platser som du vill undanta från sökningen.

    ![Skärmbild av Definition Editor blockeras fliken](../media/blockedCustomSrch.png)


2. Ange en fråga för att bekräfta att din-instans inte returnera resultat från blockerade platser, i förhandsgranskningsfönstret till höger. 

## <a name="add-pinned-entries"></a>Lägga till fästa poster
Lägg till på webbsidan och fråga sikt till för att fästa en specifik webbsida överst i sökresultaten den **fästa** fliken.  Den **fästa** fliken innehåller en lista över webbsidan och fråga term-par som anger på webbsidan som visas som det översta resultatet för en specifik fråga. Användarens frågeterm måste exakt matcha Fäst frågeterm för webbsidan till fäst på upp.

1. I den **Definition Editor**, klicka på den **fästa** och ange den webbsida som ska returnera översta resultatet webbsidan och fråga period.

    ![Skärmbild av Definition Editor Fäst fliken](../media/pinnedCustomSrch.png)

2. För att bekräfta att din instans returnerar den angivna webbsidan som övre resultatet in frågeterm som du har fäst i förhandsgranskningsfönstret till höger.

## <a name="configure-hosted-ui"></a>Konfigurera värdbaserade UI
Anpassad sökning ger en värdbaserad gränssnitt för att återge JSON-svar för anpassad sökning-instansen.  Definiera ditt användargränssnitt:

1. Klicka på den **finns UI** fliken.
2. Välj en layout.

    ![Skärmbild som visar Användargränssnittet finns Välj layout steg](./media/custom-search-hosted-ui-select-layout.png)

3. Välj ett färgtema.

    ![Skärmbild som visar Användargränssnittet finns Välj layout steg](./media/custom-search-hosted-ui-select-color-theme.png)

4. Ange ytterligare konfigurationsalternativ.

    ![Skärmbild av Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-additional-configurations.png)

5. Klistra in din **prenumeration nyckeln**. Se [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Skärmbild av Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Använda värdbaserad UI
Det finns två sätt att använda värdbaserade Användargränssnittet.  

- Alternativ 1: Integrera angivna JavaScript-kodfragment i ditt program.
- Alternativ 2: Använda HTML-slutpunkten anges.

Resten av den här kursen visar **alternativ 1: Javascript-kodfragment**.  

## <a name="set-up-your-visual-studio-solution"></a>Konfigurera Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I den **nytt projekt** väljer **Visual C# / webb-/ webbprogram för ASP.NET Core**namnge projektet och klicka sedan på **OK**.
   
    ![Skärmbild av nytt projektfönster](./media/custom-search-new-project.png)

4. I den **nytt webbprogram för ASP.NET Core** väljer **webbprogram** och på **OK**.
    
    ![Skärmbild av nytt projektfönster](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Redigera index.cshtml
1. I den **Solution Explorer**, expandera **sidor** och dubbelklicka på **index.cshtml** att öppna filen.

    ![Skärmdump av solution explorer med expanderas-sidor och index.cshtml valt](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. Ta bort allt med början från rad 7 och nedan i index.cshtml.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Lägga till en rad break-element och en div som fungerar som en behållare.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Från den **finns UI** och rulla ned till avsnittet **förbrukar Användargränssnittet**. Kopiera JavaScript-kodfragment.

    ![Skärmbild som visar Användargränssnittet finns spara knapp](./media/custom-search-hosted-ui-consuming-ui.png)

5. Klistra in script-element i behållaren som du har lagt till.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. I den **Solution Explorer**, högerklickar du på **wwwroot** och på **vyn i webbläsaren**.

    ![Skärmdump av solution explorer att välja Visa i webbläsaren på snabbmenyn wwwroot](./media/custom-search-webapp-view-in-browser.png)

Den nya webbsidan för anpassad sökning bör se ut ungefär så här:

![Skärmbild av sidan anpassad sökning](./media/custom-search-webapp-browse-index.png)

Utför en sökning återger resultat så här.

![Skärmbild av anpassade sökresultat](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du gjort följande:

> [!div class="checklist"]
> - Skapa en anpassad sökning-instans
> - Aktiva poster som lagts till
> - Tillagda blockerade poster
> - Tillagda fästa poster
> - Integrerad anpassad sökning på en webbsida

Du kan nu fortsätta att anropa Bing anpassad sökning slutpunkter programmässigt.

> [!div class="nextstepaction"]
> [Anropa Bing anpassad sökning slutpunkt (C#)](../call-endpoint-csharp.md)