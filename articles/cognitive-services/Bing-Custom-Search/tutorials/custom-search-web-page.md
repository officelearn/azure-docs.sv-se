---
title: 'Självstudie: Skapa en webbsida för anpassad sökning – Anpassad sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar en anpassad söknings instans i Bing och integrerar den på en webb sida med den här självstudien.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: a789cb3fde05d12a8793196043f1c246bbab6559
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342421"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Självstudie: Skapa en webbsida för anpassad sökning

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Med anpassad sökning i Bing kan du skapa skräddarsydda sökningar om ämnen som intresserar dig. Om du till exempel äger en kamp-webbplats som tillhandahåller en Sök funktion kan du ange domäner, underordnade platser och webb sidor som Bing-sökningar. Användarna ser sökresultat som skräddarsytts efter det innehåll som intresserar dem, i stället för att behöva bläddra bland sökresultat som kan vara irrelevant. 

I den här självstudien beskrivs hur du konfigurerar en anpassad sökinstans och integrerar den i en webbsida.

Här är några av uppgifterna:

> [!div class="checklist"]
> - Skapa en anpassad sökinstans
> - Lägga till aktiva poster
> - Lägga till blockerade poster
> - Lägga till fästa poster
> - Integrera anpassad sökning i en webbsida

## <a name="prerequisites"></a>Förutsättningar

- Om du vill följa självstudiekursen behöver du en prenumerationsnyckel för API för anpassad Bing-sökning.  Om du vill hämta en nyckel [skapar du en anpassad sökning i Bing resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) i Azure Portal.
- Om du inte redan har installerat Visual Studio 2017 eller senare kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad sökinstans

Så här skapar du en instans för anpassad sökning i Bing:

1. Öppna en webbläsare.  
  
2. Navigera till den anpassade sökningens [portal](https://customsearch.ai).  
  
3. Logga in på portalen med ett Microsoft-konto (MSA). Om du inte har en MSA klickar du på **skapa en Microsoft-konto**. Om det är första gången du använder portalen uppmanas du att ha behörighet att komma åt dina data. Klicka på **Ja**.  
  
4. När du har loggat in klickar du på **Ny anpassad sökning**. I fönstret **skapa en ny anpassad Sök instans** anger du ett namn som är meningsfullt och beskriver vilken typ av innehåll sökningen returnerar. Du kan ändra namnet när som helst.  
  
   ![Skärmbild för rutan Skapa en ny anpassad sökinstans](../media/newCustomSrch.png)  
  
5. Klicka på OK, ange en webbadress och om du vill inkludera underordnade sidor av webbadressen.  
  
   ![Skärmbild av URL-definitionssidan](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Lägga till aktiva poster

Om du vill utesluta resultat från vissa webbplatser eller webbadresser kan du lägga till dem på fliken **Aktiv**.

1. På sidan **Konfiguration** klickar du på fliken **Aktiv** och anger webbadressen till en eller flera webbplatser som du vill inkludera i sökningen.

    ![Skärmbild av aktiv Definition Editor-flik](../media/customSrchEditor.png)

2. Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Bing returnerar endast resultat för offentliga webbplatser som den har indexerat.

## <a name="add-blocked-entries"></a>Lägga till blockerade poster

Om du vill utesluta resultat från vissa webbplatser eller webbadresser kan du lägga till dem på fliken **Blockerad**.

1. På sidan **Konfiguration** klickar du på fliken **Blockerad** och anger webbadressen till en eller flera webbplatser som du vill undanta från sökningen.

    ![Skärmbild av blockerad Definition Editor-flik](../media/blockedCustomSrch.png)


2. Kontrollera att din instans inte returnerar resultat från blockerade webbplatser genom att ange en fråga i förhandsgranskningsfönstret till höger. 

## <a name="add-pinned-entries"></a>Lägga till fästa poster

Lägg till webb sidan och fråge termen till den **fästa** fliken om du vill fästa en speciell webb sida överst i Sök resultaten. Den **fästa** fliken innehåller en lista över webb sidor och frågetermer som anger den webb sida som visas som det främsta resultatet för en speciell fråga. Webb sidan är endast fixerad om användarens frågesträng matchar PIN-kodens frågesträng baserat på PIN-koden match. Endast indexerade webbplatser visas i sökningar. Mer information finns i [definiera din anpassade vy](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results).

1. På sidan **Konfiguration** klickar du på den **fästa** fliken och anger webbsidan och frågetermen för den webbsida som du vill ska returneras som ett översta resultat.  
  
2. Som standard måste användarens frågesträng exakt matcha din fästa frågesträng för att Bing ska returneras på webbsidan som det översta resultatet. För att ändra matchningsvillkoret redigerar du nålen (klicka på pennikonen), klickar på Exakt i kolumnen **Query match condition** (Matchningsvillkor för fråga) och väljer det matchningsvillkor som passar ditt program.  
  
    ![Skärmbild av fäst Definition Editor-flik](../media/pinnedCustomSrch.png)
  
3. Bekräfta att din instans returnerar den angivna webbsidan som det översta resultat genom att ange frågetermen som du har fäst i förhandsgranskningsfönstret till höger.

## <a name="configure-hosted-ui"></a>Konfigurera värdbaserat användargränssnitt

Anpassad sökning ger ett värdbaserat gränssnitt för att rendera JSON-svar för din anpassade sökinstans. Så här definierar du UI-miljön:

1. Klicka på fliken för **värdbaserat användargränssnitt**.  
  
2. Välj en layout.  
  
   ![Skärmbild av steget för att välja layout för värdbaserat användargränssnitt](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Välj ett färgtema.  
  
   ![Skärmbild av val av färgtema för värdbaserat användargränssnitt](./media/custom-search-hosted-ui-select-color-theme.png)  

   Om du behöver finjustera färgtemat så att det bättre integrerar med din webbapp klickar du på **Anpassa tema**. Alla färgkonfigurationer finns inte för alla layoutteman. Om du vill ändra en färg anger du färgens RGB HEX-värde (till exempel #366eb8) i motsvarande textruta. Eller så kan du klicka på färgknappen och klicka på nyansen som passar dig. Tänk alltid på tillgänglighet när du väljer färger.
  
   ![Skärmbild av anpassa tema för värdbaserat användargränssnitt](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Ange ytterligare konfigurationsalternativ.  
  
   ![Skärmbild av ytterligare konfigurationssteg för värdbaserat användargränssnitt](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Om du vill få avancerade konfigurationer klickar du på **Visa avancerade konfigurationer**. Detta lägger till konfigurationer som *Länkmål* till webbsökningsalternativ, *Aktivera filter* för bild- och videoalternativ och *Textplatshållare för sökruta* till övriga alternativ.

   ![Skärmbild av avancerade konfigurationssteg för värdbaserat användargränssnitt](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Välj dina prenumerationsnycklar från listrutorna. Eller så kan du ange prenumerationsnyckeln manuellt.
  
   ![Skärm bild av prenumerations nyckeln för värd gränssnitt](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Använda värdbaserat användargränssnitt

Det finns två sätt att använda det värdbaserade användargränssnittet.  

- Alternativ 1: Integrera medföljande JavaScript-kodfragment i programmet.
- Alternativ 2: Använda HTML-slutpunkten som medföljer.

Resten av den här självstudien illustrerar **alternativ 1: JavaScript-kodfragment**.  

## <a name="set-up-your-visual-studio-solution"></a>Konfigurera din Visual Studio-lösning

1. Öppna **Visual Studio** på datorn.  
  
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.  
  
3. I fönstret **Nytt projekt** väljer du **Visual C# / Webb / ASP.NET Core-webbprogram**, namnger ditt projekt och klickar på **OK**.  
  
   ![Skärmbild av fönstret Nytt projekt](./media/custom-search-new-project.png)  
  
4. I fönstret **Nytt ASP.NET-webbprogram** väljer du **Webbprogram** och klickar på **OK**.  
  
   ![Skärm bild av nytt webapp-fönster](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Redigera index.cshtml

1. I **Solution Explorer** expanderar du **Sidor** och öppnar filen genom att dubbelklicka på **index.cshtml**.  
  
   ![Skärmbild av Solution Explorer med Sidor expanderat och index.cshtml vald](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. I index.cshtml tar du bort allt från rad 7 och nedan.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Lägg till ett radbrytningselement och en div som ska fungera som en container.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. På sidan för **värdbaserat användargränssnitt** rullar du ned till avsnittet om att **använda användargränssnittet**. Klicka på *Slutpunkter* för att få åtkomst till JavaScript-kodavsnittet. Du kan också få kodfragmentet genom att klicka på **Produktion** och sedan på fliken för **värdbaserat användargränssnitt**.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Klistra in skriptelementet i den container som du har lagt till.  
  
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
  
6. I **Solution Explorer** högerklickar du på **wwwroot** och sedan på **Visa i webbläsare**.  
  
   ![Skärmbild av när Solution Explorer väljer Visa i webbläsare på snabbmenyn wwwroot](./media/custom-search-webapp-view-in-browser.png)  

Den nya webbsidan för anpassad sökning i bör se ut ungefär så här:

![Skärmbild av webbsida för anpassad sökning](./media/custom-search-webapp-browse-index.png)

När du utför en sökning återges resultat så här:

![Skärmbild av anpassade sökresultat](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa Slutpunkt för Anpassad sökning i Bing (C#)](../call-endpoint-csharp.md)