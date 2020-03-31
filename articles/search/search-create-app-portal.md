---
title: Skapa en sökapp i Azure Portal
titleSuffix: Azure Cognitive Search
description: Kör guiden Skapa app (förhandsversion) för att generera HTML-sidor och skript för en fungerande webbapp. Sidan innehåller ett sökfält, ett resultatområde, sidofält och typhuvudstöd.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369715"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Snabbstart: Skapa en sökapp i portalen (Azure Cognitive Search)

Använd portalens guiden **Skapa sökapp** för att generera en webbapp med hämtningsbar, "localhost"-stil som körs i en webbläsare. Beroende på dess konfiguration fungerar den genererade appen vid första användningen, med en direktanslutning till ett fjärrindex. En standardapp kan innehålla ett sökfält, resultatområde, sidofältsfilter och typhuvudstöd.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="prerequisites"></a>Krav

Uppgradera till den [senaste versionen av Microsoft Edge](https://www.microsoft.com/edge) eller använd Googles Chrome-webbläsare för den här snabbstarten.

[Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. 

[Skapa ett index](search-create-index-portal.md) som du kan använda som grund för ditt program. 

Den här snabbstarten använder de inbyggda exempeldata och indexet för fastigheter eftersom de har miniatyrbilder (guiden stöder att lägga till bilder på resultatsidan). Om du vill skapa det index som används i den här övningen kör du guiden **Importera data** och väljer datakällan *för realestate-us-sample.*

![datakällsida för exempeldata](media/search-create-app-portal/import-data-realestate.png)

När indexet är klart att använda går du vidare till nästa steg.

## <a name="start-the-wizard"></a>Starta guiden

1. Logga in på [Azure-portalen](https://portal.azure.com) och [hitta din söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Välj **Index**i mitten av sidan på sidan Översikt . 

1. Välj *realestate-us-sample-index* från listan över befintliga index.

1. På indexsidan väljer du **Skapa sökapp (förhandsgranskning)** högst upp för att starta guiden.

1. På den första guidesidan väljer du **Aktivera KORSBESKÄRNINGSRESURSDELNING (CORS)** om du vill lägga till CORS-stöd i indexdefinitionen. Det här steget är valfritt, men din lokala webbapp ansluter inte till fjärrindexet utan det.

## <a name="configure-search-results"></a>Konfigurera sökresultat

Guiden innehåller en grundläggande layout för renderade sökresultat som innehåller utrymme för en miniatyrbild, en rubrik och en beskrivning. Att stödja vart och ett av dessa element är ett fält i indexet som tillhandahåller data. 

1. Välj *miniatyrfältet* i *realestate-us-sample-indexet* i Miniatyr. Det här exemplet innehåller miniatyrer i form av URL-adresserade bilder som lagras i ett fält som kallas *miniatyr*. Om indexet inte innehåller bilder lämnar du det här fältet tomt.

1. I Rubrik väljer du ett fält som förmedlar det unika i varje dokument. I det här exemplet är list-ID:et ett rimligt urval.

1. I Beskrivning väljer du ett fält som innehåller information som kan hjälpa någon att bestämma om han eller hon vill klicka sig vidare till det aktuella dokumentet.

![datakällsida för exempeldata](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Lägga till ett sidofält

Söktjänsten stöder fasterad navigering, som ofta återges som ett sidofält. Faser baseras på filterbara och facetable fält, som uttrycks i indexschemat.

I Azure Cognitive Search är fakterad navigering en kumulativ filtreringsupplevelse. Inom en kategori utökar valet av flera filter resultaten (till exempel att välja Seattle och Bellevue inom City). Om du väljer flera filter begränsas resultatet genom att välja flera filter.

> [!TIP]
> Du kan visa hela indexschemat i portalen. Leta efter länken **Index definition (JSON)** på varje index översiktssida. Fält som uppfyller kraven för fasterad navigering har attributen "filterable: true" och "facetable: true".

Acceptera det aktuella valet av aspekter och fortsätt till nästa sida.


## <a name="add-typeahead"></a>Lägg till typeahead

Typeahead-funktioner är tillgängliga i form av automatisk komplettering och frågeförslag. Guiden stöder frågeförslag. Baserat på tangenttryckningsindata som tillhandahålls av användaren returnerar söktjänsten en lista med "slutförda" frågesträngar som kan väljas som indata.

Förslag är aktiverade för specifika fältdefinitioner. Guiden ger dig alternativ för att konfigurera hur mycket information som ingår i ett förslag. 

Följande skärmbild visar alternativ i guiden, tillsammans med en renderad sida i appen. Du kan se hur fältval används och hur "Visa fältnamn" används för att inkludera eller utesluta märkning i förslaget.

![Konfiguration av frågeförslag](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Skapa, hämta och kör

1. Välj **Skapa sökapp** om du vill generera HTML-filen.

1. När du uppmanas till det väljer du **Hämta appen** för att hämta filen.

1. Öppna filen. Du bör se en sida som liknar följande skärmdump. Ange en term och använd filter för att begränsa resultaten. 

Det underliggande indexet består av fiktiva, genererade data som har duplicerats över dokument och beskrivningar matchar ibland inte bilden. Du kan förvänta dig en mer sammanhängande upplevelse när du skapar en app baserat på dina egna index.

![Kör appen](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Standardappen är användbar för inledande utforskning och små uppgifter, men om du granskar API:erna tidigt kan du förstå begreppen och arbetsflödet på en djupare nivå:

> [!div class="nextstepaction"]
> [Skapa ett index med .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)