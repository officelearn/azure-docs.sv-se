---
title: 'Anpassad sökning i Bing: Skapa en anpassad sökning i webbsida | Microsoft Docs'
description: Beskriver hur du konfigurerar en anpassad Sökinstans och integrerar dem i en webbsida
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 1f9b689ac6127bc2f7d1e810356ae9a23b8e0996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162401"
---
# <a name="build-a-custom-search-web-page"></a>Skapa en webbsida för anpassad sökning
Bing Custom Search kan du skapa skräddarsydda sökmöjligheter för ämnen som intresserar dig. Till exempel, om du äger en Kampsport-webbplats som ger en sökfunktion kan kan du ange de domäner och underplatser webbsidor som söker i Bing. Användarna ser sökresultat som skräddarsys för det innehåll som de är i stället för bläddring genom allmänna sökresultat som kan innehålla inte relevant innehåll. 

Den här kursen visar hur du konfigurerar en anpassad Sökinstans och integrerar dem i en ny webbsida.

De funktioner som beskrivs är:

> [!div class="checklist"]
> - Skapa en anpassad Sökinstans
> - Lägg till aktiva poster
> - Lägga till blockerade poster
> - Lägga till fästa poster
> - Integrera anpassad sökning i en webbsida

## <a name="prerequisites"></a>Förutsättningar
- Om du vill följa självstudiekursen behöver du en prenumerationsnyckel för API: et för Bing Custom Search.  För att få en nyckel kan se [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**.

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad Sökinstans
Skapa en anpassad sökning i Bing-instans:

1.  Öppna en webbläsare.
2.  Navigera till den anpassade sökningen [portal](https://customsearch.ai).
3.  Logga in på portalen med ett Microsoft-konto (MSA). Om du inte har en MSA, klickar du på **skapa ett Microsoft-konto**. Om det är första gången du använder portalen får du frågan för behörighet att komma åt dina data. Klicka på **Ja**.
4.  När du har loggat in klickar du på **anpassad sökning i nya**. I den **skapa en ny anpassad Sökinstans** fönstret anger du ett namn som betyder något och beskriver typ av innehåll som returnerar sökningen. Du kan ändra namnet när som helst.
 
    ![Skärmbild för att skapa en ny anpassad instans sökruta](../media/newCustomSrch.png)

5.  Klicka på OK, anger en URL och om du vill inkludera underordnade sidor på bassidan:

    ![Skärmbild av sidan för URL-definition](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Lägg till aktiva poster
För att inkludera resultat från specifika platser eller URL: er, lägga till dem i den **Active** fliken.

1.  I den **Definition Editor**, klickar du på den **Active** fliken och ange Webbadressen till en eller flera platser som du vill ska ingå i sökningen.

    ![Skärmbild som visar fliken Definition Editor active](../media/customSrchEditor.png)

2.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Bing returnerar resultat endast för offentliga webbplatser som den har indexerats.

## <a name="add-blocked-entries"></a>Lägga till blockerade poster
Om du vill utesluta resultat från specifika platser eller URL: er, lägga till dem i den **blockerad** fliken.

1. I den **Definition Editor**, klickar du på den **blockerad** fliken och ange Webbadressen till en eller flera platser som du vill undanta från sökningen.

    ![Skärmbild av Definition Editor blockeras fliken](../media/blockedCustomSrch.png)


2. Ange en fråga för att bekräfta att din instans inte returnera resultat från blockerade platser, i förhandsgranskningsfönstret till höger. 

## <a name="add-pinned-entries"></a>Lägga till fästa poster
För att fästa en specifik webbsida längst upp i sökresultaten, lägger du till webbsidan och fråga villkoret till den **fästa** fliken.  Den **fästa** fliken innehåller en lista över webbsidan och fråga termen-par som anger på webbsidan som visas i det översta resultat för en specifik fråga. Användarens frågetermen måste exakt matcha fästa frågetermen för webbsidan till att fästa högst upp.

1. I den **Definition Editor**, klickar du på den **fästa** fliken och ange att webbsidan och fråga perioden för den webbsida som ska returnera översta resultatet.

    ![Skärmbild av Definition Editor Fäst fliken](../media/pinnedCustomSrch.png)

2. Bekräfta att din instans returnerar den angivna webbsidan som det översta resultat genom att ange du frågetermen som du har fäst i förhandsgranskningsfönstret till höger.

## <a name="configure-hosted-ui"></a>Konfigurera värdbaserade UI
Anpassad sökning ger ett värdbaserade gränssnitt för att rendera JSON-svar för din anpassade Sökinstans.  Definiera UI-miljön:

1. Klicka på den **finns Användargränssnittet** fliken.
2. Välj en layout.

    ![Skärmbild som visar Användargränssnittet finns Välj layout steg](./media/custom-search-hosted-ui-select-layout.png)

3. Välj ett färgtema.

    ![Skärmbild som visar Användargränssnittet finns Välj layout steg](./media/custom-search-hosted-ui-select-color-theme.png)

4. Ange ytterligare konfigurationsalternativ.

    ![Skärmbild som visar Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-additional-configurations.png)

5. Klistra in din **prenumerationsnyckel**. Se [testa kognitiva tjänster](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Skärmbild som visar Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Använda värdbaserad UI
Det finns två sätt att använda värdbaserade Användargränssnittet.  

- Alternativ 1: Integrera angivna JavaScript-kodavsnitt i ditt program.
- Alternativ 2: Använda HTML-slutpunkten, förutsatt att.

Resten av den här självstudien visar **alternativ 1: Javascript-kodavsnitt**.  

## <a name="set-up-your-visual-studio-solution"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I den **nytt projekt** väljer **Visual C# / webb-/ ASP.NET Core-Webbapp**, namnge projektet och klicka sedan på **OK**.
   
    ![Skärmbild som visar det nya projektfönstret](./media/custom-search-new-project.png)

4. I den **ny ASP.NET Core-Webbapp** väljer **webbprogram** och klicka på **OK**.
    
    ![Skärmbild som visar det nya projektfönstret](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Redigera index.cshtml
1. I den **Solution Explorer**, expandera **sidor** och dubbelklicka på **index.cshtml** att öppna filen.

    ![Skärmbild av solution explorer med sidor expanderas och index.cshtml valt](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. Ta bort i index.cshtml, allt med början från rad 7 och nedan.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Lägga till en radbrytning linjeelement och en div ska fungera som en behållare.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Från den **finns Användargränssnittet** fliken, rulla ned till avsnittet **förbrukar Användargränssnittet**. Kopiera JavaScript-kodavsnitt.

    ![Skärmbild som visar Användargränssnittet finns spara knapp](./media/custom-search-hosted-ui-consuming-ui.png)

5. Klistra in script-elementet i den behållare som du har lagt till.

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

6. I den **Solution Explorer**, högerklicka på **wwwroot** och klicka på **visa i webbläsare**.

    ![Skärmbild av solution explorer väljer Visa i webbläsare på snabbmenyn för wwwroot](./media/custom-search-webapp-view-in-browser.png)

Den nya webbsidan för anpassad sökning i bör se ut ungefär så här:

![Skärmbild av webbsidan för anpassad sökning](./media/custom-search-webapp-browse-index.png)

Utför en sökning återger resultat så här.

![Skärmbild som visar anpassade sökresultat](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du gjort följande:

> [!div class="checklist"]
> - Skapa en anpassad Sökinstans
> - Tillagda active poster
> - Blockerade poster
> - Fästa poster
> - Integrerad anpassad sökning i en webbsida

Du kan nu fortsätta att anropa Bing Custom Search-slutpunkter via programmering.

> [!div class="nextstepaction"]
> [Anropa anpassad sökning i Bing slutpunkt (C#)](../call-endpoint-csharp.md)