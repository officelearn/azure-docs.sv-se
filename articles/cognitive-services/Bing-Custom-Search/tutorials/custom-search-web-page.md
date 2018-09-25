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
ms.openlocfilehash: 8bc1520325afc256ac62cc1f1dfaf24c53da4b83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980006"
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

1. Öppna en webbläsare.  
  
2. Navigera till den anpassade sökningen [portal](https://customsearch.ai).  
  
3. Logga in på portalen med ett Microsoft-konto (MSA). Om du inte har en MSA, klickar du på **skapa ett Microsoft-konto**. Om det är första gången du använder portalen får du frågan för behörighet att komma åt dina data. Klicka på **Ja**.  
  
4. När du har loggat in klickar du på **anpassad sökning i nya**. I den **skapa en ny anpassad Sökinstans** fönstret anger du ett namn som betyder något och beskriver typ av innehåll som returnerar sökningen. Du kan ändra namnet när som helst.  
  
  ![Skärmbild för att skapa en ny anpassad instans sökruta](../media/newCustomSrch.png)  
  
5. Klicka på OK, anger en URL och om du vill inkludera underordnade sidor av URL: en.  
  
  ![Skärmbild av URL: en definition av sidan](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Lägg till aktiva poster

För att inkludera resultat från vissa webbplatser eller URL: er, lägga till dem i den **Active** fliken.

1.  På den **Configuration** klickar du på den **Active** fliken och ange Webbadressen till en eller flera webbplatser som du vill ska ingå i sökningen.

    ![Skärmbild av fliken Definition Editor active](../media/customSrchEditor.png)

2.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Bing returnerar endast resultat för offentliga webbplatser som den har indexerats.

## <a name="add-blocked-entries"></a>Lägga till blockerade poster

Om du vill utesluta resultat från vissa webbplatser eller URL: er, lägga till dem i den **blockerad** fliken.

1. På den **Configuration** klickar du på den **blockerad** fliken och ange Webbadressen till en eller flera webbplatser som du vill undanta från sökningen.

    ![Skärmbild av fliken Definition Editor blockerade](../media/blockedCustomSrch.png)


2. Kontrollera att din instans inte returnera resultat från blockerade webbplatser genom att ange en fråga i förhandsgranskningsfönstret till höger. 

## <a name="add-pinned-entries"></a>Lägga till fästa poster

För att fästa en specifik webbsida längst upp i sökresultaten, lägger du till webbsidan och fråga villkoret till den **fästa** fliken. Den **fästa** fliken innehåller en lista över webbsidan och fråga termen-par som anger på webbsidan som visas i det översta resultat för en specifik fråga. Webbsidan fästs endast om användarens frågesträngen matchar den PIN-kod frågesträngen baserat på matchningsvillkor för PIN-kod. [Läs mer](../define-your-custom-view.md#pin-to-top).

1. På den **Configuration** klickar du på den **fästa** fliken och ange att webbsidan och fråga perioden för den webbsida som du vill ska returneras som ett översta resultat.  
  
2. Som standard måste användarens frågesträngen exakt matcha din PIN-kod frågesträngen för att Bing ska returneras på webbsidan som det översta resultat. Redigera PIN-koden om du vill ändra matchningsvillkor (klicka på pennikonen) klickar du på Exact i den **fråga matchningsvillkor** kolumnen och välj matchningsvillkor som passar ditt program.  
  
    ![Skärmbild av Definition Editor Fäst fliken](../media/pinnedCustomSrch.png)
  
3. Bekräfta att din instans returnerar den angivna webbsidan som det översta resultat genom att ange du frågetermen som du har fäst i förhandsgranskningsfönstret till höger.

## <a name="configure-hosted-ui"></a>Konfigurera värdbaserade UI

Anpassad sökning ger ett värdbaserade gränssnitt för att rendera JSON-svar för din anpassade Sökinstans. Definiera UI-miljön:

1. Klicka på den **finns Användargränssnittet** fliken.  
  
2. Välj en layout.  
  
  ![Skärmbild av Användargränssnittet finns Välj layout steg](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Välj ett färgtema.  
  
  ![Skärmbild av Användargränssnittet finns Välj färgtema](./media/custom-search-hosted-ui-select-color-theme.png)  

  Om du behöver att finjustera färgtema vill bättre integrera med din webbapp klickar du på **Anpassa tema**. Inte alla konfigurationer av färg gäller för alla layoutteman. Om du vill ändra en färg, ange färgens RGB HEX-värdet (till exempel #366eb8) i motsvarande textruta. Eller, klicka på färgknappen och klickar på nyans som passar dig. Tänk alltid hjälpmedel när du väljer färger.
  
  ![Skärmbild av Användargränssnittet finns anpassa färgtema](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Ange ytterligare konfigurationsalternativ.  
  
  ![Skärmbild av Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Om du vill få avancerade konfigurationer, klickar du på **visa avancerade konfigurationer**. Detta lägger till konfigurationer som *länkmål* till Web Sökalternativ, *aktivera filter* till bild och videoinnehåll alternativ, och *Sök textplatshållare* till övrigt alternativ.

  ![Skärmbild av Användargränssnittet för värdbaserade avancerade konfigurationer steg](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Välj din prenumerationsnycklar från den nedrullningsbara listan. Eller så kan du ange prenumerationsnyckeln manuellt. Information om hämtning av nycklar finns i [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![Skärmbild av Användargränssnittet finns ytterligare konfigurationer steg](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

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
  
  ![Skärmbild av det nya projektfönstret](./media/custom-search-new-project.png)  
  
4. I den **ny ASP.NET Core-Webbapp** väljer **webbprogram** och klicka på **OK**.  
  
  ![Skärmbild av det nya projektfönstret](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Redigera index.cshtml

1. I den **Solution Explorer**, expandera **sidor** och dubbelklicka på **index.cshtml** att öppna filen.  
  
  ![Skärmbild av solution explorer med sidor expanderas och index.cshtml valt](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Ta bort i index.cshtml, allt med början från rad 7 och nedan.  
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Lägga till en radbrytning linjeelement och en div ska fungera som en behållare.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. I den **finns Användargränssnittet** sida, rulla ned till avsnittet **förbrukar Användargränssnittet**. Klicka på den *slutpunkter* att få åtkomst till JavaScript-kodavsnitt. Du kan också få kodfragmentet genom att klicka på **produktion** och sedan den **finns Användargränssnittet** fliken.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
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
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. I den **Solution Explorer**, högerklicka på **wwwroot** och klicka på **visa i webbläsare**.  
  
  ![Skärmbild av solution explorer väljer Visa i webbläsare på snabbmenyn för wwwroot](./media/custom-search-webapp-view-in-browser.png)  

Den nya webbsidan för anpassad sökning i bör se ut ungefär så här:

![Skärmbild av webbsidan för anpassad sökning](./media/custom-search-webapp-browse-index.png)

Utför en sökning återges resultat så här:

![Skärmbild av anpassade sökresultat](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa anpassad sökning i Bing slutpunkt (C#)](../call-endpoint-csharp.md)