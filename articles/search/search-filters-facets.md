---
title: Aspektfilter för söknavigering i appar
titleSuffix: Azure Cognitive Search
description: Filtrera villkor efter användarsäkerhetsidentitet, geografisk plats eller numeriska värden för att minska sökresultaten på frågor i Azure Cognitive Search, en värdbaserad molnsöktjänst på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792902"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Så här skapar du ett aspektfilter i Azure Cognitive Search 

Aspektbaserad navigering används för självstyrd filtrering på frågeresultat i en sökapp, där ditt program erbjuder gränssnittskontroller för omfångssökning till grupper av dokument (till exempel kategorier eller varumärken) och Azure Cognitive Search tillhandahåller datastrukturen för att stödja upplevelsen. I den här artikeln bör du snabbt gå igenom de grundläggande stegen för att skapa en aspektad navigeringsstruktur som stöder den sökupplevelse som du vill tillhandahålla. 

> [!div class="checklist"]
> * Välj fält för filtrering och fasning
> * Ange attribut på fältet
> * Skapa index- och inläsningsdata
> * Lägga till aspektfilter i en fråga
> * Hantera resultat

Faser är dynamiska och returneras på en fråga. Söksvaren för med sig de aspektkategorier som används för att navigera i resultaten. Om du inte är bekant med faser är följande exempel en illustration av en aspektnavigeringsstruktur.

  ![](./media/search-filters-facets/facet-nav.png)

Ny till fasterad navigering och vill ha mer detaljer? Se [Så här implementerar du fasterad navigering i Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Välj fält

Faser kan beräknas över fält med ett värde samt samlingar. Fält som fungerar bäst i aspektad navigering har låg kardinalitet: ett litet antal distinkta värden som upprepas i dokument i sökkorpusen (till exempel en lista med färger, länder/regioner eller varumärken). 

Fasning aktiveras fält för fält när du skapar indexet genom `facetable` att `true`ange attributet på . Du bör i `filterable` allmänhet `true` också ange attributet till för sådana fält så att sökprogrammet kan filtrera på de fält baserat på aspekter som slutanvändaren väljer. 

När du skapar ett index med REST API markeras alla [fälttyper](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som `facetable` eventuellt kan användas i fasterad navigering som standard:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numeriska `Edm.Int32`fälttyper: `Edm.Int64`,`Edm.Double`
+ Samlingar av ovanstående typer (till exempel `Collection(Edm.String)` eller `Collection(Edm.Double)`)

Du kan `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` inte använda eller fält i fasterad navigering. Faser fungerar bäst på fält med låg kardinalitet. På grund av upplösningen av geo-koordinater är det ovanligt att två uppsättningar koordinater kommer att vara lika i en viss datauppsättning. Fasor stöds därför inte för geokoordinater. Du skulle behöva en stad eller region fält för att aspekt efter plats.

## <a name="set-attributes"></a>Ange attribut

Indexattribut som styr hur ett fält används läggs till i enskilda fältdefinitioner i indexet. I följande exempel består fält med låg kardinalitet, användbara `category` för faceting, av: (hotell, motell, vandrarhem), `tags`och `rating`. Dessa fält `filterable` har `facetable` attributen och som uttryckligen anges i följande exempel för illustrativa ändamål. 

> [!Tip]
> Som bästa praxis för prestanda- och lagringsoptimering, inaktivera fasning för fält som aldrig ska användas som en aspekt. I synnerhet bör strängfält för unika värden, till exempel ett `"facetable": false` ID eller produktnamn, ställas in för att förhindra att de används oavsiktligt (och ineffektivt) i fasetterad navigering.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Den här indexdefinitionen kopieras från [Skapa ett Azure Cognitive Search-index med REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Den är identisk med undantag för ytliga skillnader i fältdefinitionerna. `filterable` Attributen `facetable` och läggs uttryckligen `category` `tags`till `parkingIncluded` `smokingAllowed`i `rating` , , , och fälten. I praktiken `filterable` `facetable` och skulle aktiveras som standard på dessa fält när du använder REST API. När du använder .NET SDK måste dessa attribut aktiveras explicit.

## <a name="build-and-load-an-index"></a>Skapa och ladda ett index

Ett mellanliggande (och kanske självklart) steg är att du måste [skapa och fylla i indexet](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) innan du formulerar en fråga. Vi nämner detta steg här för fullständighet. Ett sätt att avgöra om indexet är tillgängligt är att kontrollera indexlistan i [portalen](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Lägga till aspektfilter i en fråga

I programkod konstruerar du en fråga som anger alla delar av en giltig fråga, inklusive sökuttryck, faset, filter, bedömningsprofiler – allt som används för att formulera en begäran. I följande exempel skapas en begäran som skapar aspektnavigering baserat på typen av boende, klassificering och andra bekvämligheter.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Returnera filtrerade resultat på klickhändelser

När slutanvändaren klickar på ett nominellt värde bör hanteraren för klickhändelsen använda ett filteruttryck för att förverkliga användarens avsikt. Med `category` tanke på en aspekt, klicka på kategorin "motell" genomförs med ett `$filter` uttryck som väljer boende av den typen. När en användare klickar på "motell" för att ange att endast motell ska visas, innehåller `$filter=category eq 'motel'`nästa fråga som programmet skickar .

Följande kodavsnitt lägger till kategori i filtret om en användare väljer ett värde från kategoriassetten.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Om användaren klickar på ett nominellt värde `tags`för ett samlingsfält, till exempel värdet "pool", bör programmet använda följande filtersyntax:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tips och lösningar

### <a name="initialize-a-page-with-facets-in-place"></a>Initiera en sida med fasor på plats

Om du vill initiera en sida med faseter på plats kan du skicka en fråga som en del av sidans initiering för att dirigera sidan med en inledande aspektstruktur.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Bevara en aspektnavigeringsstruktur asynkront av filtrerade resultat

En av utmaningarna med aspektnavigering i Azure Cognitive Search är att aspekter bara finns för aktuella resultat. I praktiken är det vanligt att behålla en statisk uppsättning fasor så att användaren kan navigera i omvänd, retracing steg för att utforska alternativa vägar genom sökinnehåll. 

Även om detta är ett vanligt användningsfall, det är inte något aspekten navigeringsstruktur för närvarande ger out-of-the-box. Utvecklare som vill ha statiska aspekter arbetar vanligtvis runt begränsningen genom att utfärda två filtrerade frågor: en begränsad till resultaten, den andra som används för att skapa en statisk lista över aspekter för navigering.

## <a name="see-also"></a>Se även

+ [Filter i Azure Cognitive Search](search-filters.md)
+ [Skapa REST-API för index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents)
