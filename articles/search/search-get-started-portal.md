---
title: Azure Search-självstudiekurs om indexering, frågor och filtrering med hjälp av portalen | Microsoft Docs
description: Använd fördefinierade exempeldata i Azure Portal för att generera ett index i Azure Search. Utforska fulltextsökning, filter, fasetter, fuzzy-sökning, geosearch och mycket annat.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9ee88b254131b40fdf1e01b771afa92127734e18
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190043"
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>Skapa, fråga och filtrera ett Azure Search-index i portalen

Gå till Azure Portal och utgå från en fördefinierad exempeldatauppsättning för att snabbt skapa ett Azure Search-index med hjälp av guiden **Importera data**. Utforska fulltextsökning, filter, fasetter, fuzzy-sökning och geosearch med **Sökutforskaren**.  

Det här är en introduktion helt utan kodning, så att du kan komma igång med att skriva intressanta frågor direkt utifrån fördefinierade data. Portalverktygen kan aldrig bli en fullgod ersättning för kodning, men de är mycket användbara till följande uppgifter:

+ Praktisk utbildning med minimal startsträcka
+ Skapa ett prototypindex innan du skriver kod i **Importera data**
+ Testa frågor och parsa syntax i **Sökutforskaren**
+ Visa ett befintligt index som redan finns publicerat för din tjänst och söka efter attribut

**Tidsuppskattning:** Ungefär 15 minuter, eventuellt längre om det krävs registrering till kontot eller tjänsten. 

Alternativt kan du starta med en [kodbaserad introduktion för att programmera Azure Search i .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Självstudiekursen förutsätter en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och tillgång till [Azure Search-tjänsten](search-create-service-portal.md). 

Om du inte vill etablera en tjänst omedelbart kan du titta på en sex minuter lång demonstration av stegen i den här självstudiekursen. Demonstrationen finns cirka tre minuter in i denna [översiktsvideo över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Hitta din tjänst
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Om du inte har fäst tjänstepanelen på instrumentpanelen kan du hitta din tjänst på det här sättet: 
   
   * Klicka på **Alla tjänster** i det vänstra navigeringsfönstret.
   * Skriv *sök* i sökrutan för att hämta en lista över söktjänster till din prenumeration. Din tjänst ska finnas med i listan. 

## <a name="check-for-space"></a>Kontrollera utrymmet
Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort. 

> [!TIP] 
> Panelerna på instrumentpanelen med tjänster visar hur många index, indexerare och datakällor du redan har. Panelen Indexerare visar lyckade och misslyckade operationer. Klicka på panelen för att visa antalet indexerare. 
>
> ![Paneler för indexerare och datakällor][1]
>

## <a name="create-index"></a> Skapa ett index och läsa in data
Sökfrågor iterera över ett *index* med sökbara data, metadata och konstruktioner som används för att optimera vissa sökbeteenden.

För att den här uppgiften ska förbli portalbaserad använder vi en inbyggd exampeldatauppsättning som kan crawlas med en indexerare via guiden **Importera data**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Steg 1: Starta guiden Importera data
1. Klicka på **Importera data** i kommandofältet på instrumentpanelen för Azure Search-tjänsten för att starta en guide som hjälper dig att skapa och fylla ett index.
   
    ![Kommandot Importera data][2]

2. I guiden klickar du på **Datakälla** > **Exempel** > **realestate-us-sample**. Den här datakällan är förkonfigurerad med namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

    ![Välj exempeldatauppsättning][9]

3. Klicka på **OK** att använda den.

#### <a name="step-2-define-the-index"></a>Steg 2: Definiera indexet
Att skapa ett index är vanligtvis en manuell och kodbaserad process, men guiden kan skapa ett index för alla datakällor som den kan crawla. Ett index kräver minst ett namn och en samling fält, där ett fält är markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används. 

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som ska utelämnas från sökresultat genom att avmarkera den här kryssrutan, till exempel om fält endast används i filteruttryck. 
* **Filtrerbar**, **Sorterbar** och **Fasettbar** avgör om ett fält kan användas i ett filter, en sortering eller en struktur för aspektbaserad navigering. 
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara. 

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. Strängarna får attributen hämtningsbara och sökbara. Heltal får attributen hämtningsbara, filtrerbara, sorterbara och fasettbara.

  ![Genererade realestate-index][3]

Klicka på **OK** för att skapa indexet.

#### <a name="step-3-define-the-indexer"></a>Steg 3: Definiera indexeraren
Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren. 

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för stunden ska du använda standardalternativet och köra indexeraren en gång direkt när du klickar på **OK**.  

  ![realestate indexer][8]

## <a name="check-progress"></a>Kontrollera förloppet
Övervaka dataimporten genom att gå tillbaka till instrumentpanelen för tjänsten, rulla nedåt och dubbelklicka på panelen **Indexerare** för att öppna listan med indexerare. Du bör då se den nyskapade indexeraren i listan med statusen ”pågående” eller ”lyckades”, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter][4]

## <a name="query-index"></a> Skicka frågor mot indexet
Nu har du ett sökindex som du kan börja skicka frågor mot. **Sökutforskaren** är ett frågeverktyg som är inbyggt i portalen. Det innehåller en sökruta så att du kan kontrollera att en sökning returnerar det du förväntar dig. 

> [!TIP]
> Följande steg demonstreras 6 minuter och 8 sekunder in i [översiktsvideon över Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren][5]

2. Klicka på **Ändra index** i kommandofältet för att byta till *realestate-us-sample*.

   ![Index- och API-kommandon][6]

3. Klicka på **Ange API-version** i kommandofältet om du vill se vilka REST-API:er som finns tillgängliga. Förhandsversions-API:er ger dig tillgång till nya funktioner som inte är släppta till allmänheten ännu. Använd den allmänt tillgängliga versionen (2017-11-11) för frågorna nedan om inget annat anges. 

    > [!NOTE]
    > [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) och [.NET-biblioteket](search-howto-dotnet-sdk.md#core-scenarios) är fullständigt likvärdiga, men **Sökutforskaren** kan endast hantera REST-anrop. Den accepterar syntax från både [enkel frågeparser](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig frågeparser (Lucene)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) samt alla tillgängliga sökparametrar i [dokumentsökningsoperationer](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Skriv in frågesträngarna nedan i sökfältet och klicka på **Sök**.

  ![Exempel på sökfråga][7]

**`search=seattle`**

+ Parametern **search** används för att ange en sökordssökning i det här fallet, och returnerar resultat från King County i delstaten Washington med *Seattle* i valfritt sökbart fält i dokumentet. 

+ **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Beroende på vilka dokument som används kan du behöva skriva kod som hanterar sökresultaten för att kunna extrahera viktiga element. 

+ Dokument består av alla fält som är markerade som hämtningsbara i indexet. Om du vill visa indexattribut i portalen klickar du på *realestate-us-sample* på ikonen **Index**.

**`search=seattle&$count=true&$top=100`**

+ Symbolen **&** används för att lägga till sökparametrar, som kan anges i valfri ordning. 

+  Parametern **$count=true** returnerar ett antal för summan av alla returnerade dokument. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. 

+ **$top=100** returnerar de högst rangordnade 100 dokumenten bland alla dokument. Som standard returnerar Azure Search de första 50 bästa matchningarna. Du kan öka eller minska antalet via **$top**.


## <a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

**`search=seattle&$filter=beds gt 3`**

+ Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. I det här fallet sovrum som är större än 3. 

+ Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger. 

**`search=*&facet=city&$top=2`**

+ **search=*** är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. Om du exempelvis vill att en fasetterande navigeringsstruktur ska bestå av alla städer i indexet.

+  **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. I det här fallet baseras kategorier på antalet städer. Det finns ingen aggregering i Azure Search, men du kan uppskatta aggregering via `facet`, som ger en uppräkning av dokument i varje kategori.

+ **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

**`search=seattle&facet=beds`**

+ De här frågan är en aspekt för sängar, i en textsökning för *Seattle*. *beds* kan klassas som ett fasettvärde eftersom fältet är märkt som ett hämtningsbart, filtrerbart och fasettbart fält i indexet. Värdena (numeriska, 1–5) är väl lämpade för att kategorisera och dela upp listor i grupper (listor med 3 sovrum, 4 sovrum etc.). 

+ Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

## <a name="highlight-query"></a> Lägga till markering

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet. 

**`search=granite countertops&highlight=description`**

+ I det här exemplet är den formaterade frasen *granite countertops* enklare att hitta i beskrivningsfältet.

**`search=mice&highlight=description`**

+ Fulltextsökning hittar ordformer med liknande semantisk struktur. I det här fallet har någon sökt på ”möss”. Sökresultaten innehåller då även texter med ordet ”mus” och referenser till skadedjursbekämpning. Tack vare språkanalysen kan olika former av samma ord visas i resultaten. 

+ Azure Search har stöd för 56 analysverktyg från både Lucene och Microsoft. Som standard används analysverktyget från Lucene av Azure Search. 

## <a name="fuzzy-search"></a>Använda fuzzy-sökning

Normalt sett får du inga träffar på felstavade ord – om du till exempel skrivit *samamish* när du sökte på ”Sammamish Plateau” utanför Seattle. Använd fuzzy-sökning för att hantera felstavningar. En beskrivning av detta finns i nästa exempel.

**`search=samamish`**

+ I det här exemplet stavas en stadsdel i Seattle-området fel.

**`search=samamish~&queryType=full`**

+ Fuzzy-sökning aktiveras när du skriver in symbolen **~** och använder den fullständiga frågeparsern för att tolka och parsa **~**-syntaxen på korrekt sätt. 

+ Fuzzy-sökning är tillgängligt när du väljer den fullständiga frågeparsern, som sker när du ställer in **queryType=full**. Mer information om frågescenarier i den fullständiga frågeparsern finns i [Lucene-frågesyntax i Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

+ När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen. 

## <a name="geo-search"></a> Prova geospatial sökning

Geospatial sökning stöds av [datatypen edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna. 

+ Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. Om användarna ställer krav på att kunna söka efter en ort eller ett land lägger du till fält för ort eller land som ett komplement till koordinaterna.

## <a name="next-steps"></a>Nästa steg

+ Ändra något av de objekt som du nyss skapade. När du har kört guiden en gång kan du gå tillbaka och visa eller ändra enskilda komponenter: index, indexerare eller datakälla. Vissa ändringar, till exempel ändring av ett fälts datatyp, är inte tillåtet i indexet, men de flesta egenskaper och inställningar kan ändras.

  Du kan visa enskilda komponenter genom att klicka på panelerna **Index**, **Indexerare** eller **Datakällor** på instrumentpanelen för att visa en lista med befintliga objekt. Mer information om indexredigeringar som inte kräver återskapning finns i [Uppdatera index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index).

+ Prova att använda verktygen och stegen med andra datakällor. Exempeldatauppsättningen `realestate-us-sample` kommer från en Azure SQL-databas som Azure Search kan crawla. Utöver Azure SQL Database kan Azure Search crawla och härleda ett index från fasta datastrukturer i Azure Table Storage, Blob Storage, SQL Server på en virtuell Azure-dator och Azure Cosmos DB. Guiden stöder samtliga dessa datakällor. I koden kan du på ett enkelt sätt fylla i ett index med en *indexerare*.

+ Alla övriga icke-indexerare-datakällor stöds via en push-modell, där koden skickar nya och ändrade raduppsättningar i JSON till ditt index. Mer information finns i [Lägga till, uppdatera eller ta bort dokument i Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Mer information om andra funktioner som omnämns i den här artikeln finns på dessa länkar:

* [Översikt över indexerare](search-indexer-overview.md)
* [Skapa index (innehåller en detaljerad förklaring av indexattributen)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Sökutforskaren](search-explorer.md)
* [Söka efter dokument (innehåller exempel på frågesyntax)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png