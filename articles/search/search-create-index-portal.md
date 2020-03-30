---
title: Skapa ett sökindex i Azure Portal
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar ett index för Azure Cognitive Search med hjälp av en inbyggd portalindexdesigner.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112842"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Skapa ett Azure Cognitive Search-index i portalen

Azure Cognitive Search innehåller en inbyggd indexdesigner i portalen som är användbar för prototyper eller skapa ett [sökindex](search-what-is-an-index.md) som finns på din Azure Cognitive Search-tjänst. Verktyget används för schemakonstruktion. När du sparar definitionen uttrycks ett tomt index helt i Azure Cognitive Search. Hur du laddar den med sökbart innehåll är upp till dig.

Indexdesignern är bara en metod för att skapa ett index. Du kan också både skapa och läsa in ett index med hjälp av [guiden Importera data](search-get-started-portal.md). Guiden fungerar bara med index som den skapar själv. Programmässigt kan du skapa ett index med hjälp av [.NET-](search-create-index-dotnet.md) eller REST-API:erna. [REST](search-create-index-rest-api.md)

## <a name="start-index-designer"></a>Starta indexdesigner

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna instrumentpanelen för tjänsten. Du kan klicka på **Alla tjänster** i indexet om du vill söka efter befintliga ”söktjänster” i den nuvarande prenumerationen. 

2. Klicka på länken **Lägg till index** i kommandofältet högst upp på sidan.

   ![Lägga till indexlänk i kommandofältet](media/search-create-index-portal/add-index.png "Lägga till indexlänk i kommandofältet")

3. Namnge ditt Azure Cognitive Search-index. Indexnamn refereras i indexerings- och frågeåtgärder. Indexnamnet blir en del av slutpunkts-URL:en som används på anslutningar till indexet och för att skicka HTTP-begäranden i AZURE Cognitive Search REST API.

   * Börja med en bokstav.
   * Använd endast gemena bokstäver, siffror och bindestreck (”-”).
   * Begränsa namnet till 60 tecken.

## <a name="add-fields"></a>Lägga till fält

Indexsammansättningen innehåller en *fält samling* som definierar sökbara data i ditt index. Sammantaget anger fältsamlingen strukturen på de dokument som du överför separat. En fältsamling innehåller obligatoriska och valfria fält, namngivna och maskinskrivna, med indexattribut som avgör hur fältet kan användas.

1. Lägg till fält för att helt ange vilka dokument du ska ladda upp och ange en [datatyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för var och en. Om dokument till exempel består av ett *hotell-ID,* *hotellnamn,* *adress,* *ort*och *region*skapar du ett motsvarande fält för var och en i indexet. Läs [designvägledningen i avsnittet nedan](#design) om du vill ha hjälp med att ange attribut.

1. Om inkommande data är hierarkisk karaktär bör schemat innehålla [komplexa typer](search-howto-complex-data-types.md) som representerar de kapslade strukturerna. Den inbyggda exempeldatauppsättningen Hotels illustrerar komplexa typer med hjälp av en adress (innehåller flera underfält) som har en 1:1-relation med varje hotell och en rumskomplex samling, där flera rum är associerade med varje hotell. 

1. Ange ett *nyckelfält* av typen Edm.String. Ett nyckelfält är obligatoriskt för varje Azure Cognitive Search-index och det måste vara en sträng. Värden för det här fältet måste unikt identifiera varje dokument. Som standard heter fältet *id* men du kan byta namn på det så länge strängen följer [namngivningsreglerna](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Om fältsamlingen till exempel innehåller *hotell-ID*väljer du det för din nyckel. 

1. Ange attribut för varje fält. Indexdesignern utesluter alla attribut som är ogiltiga för datatypen, men föreslår inte vad som ska inkluderas. Läs vägledningen i nästa avsnitt för att förstå vad attributen är till för.

    Azure Cognitive Search API-dokumentation innehåller kodexempel med ett enkelt *hotellindex.* I skärmbilden nedan kan du se indexdefinitionen, inklusive den franska språkanalysatorn som anges under indexdefinitionen, som du kan återskapa som en övning i portalen.

    ![Hotell demo index](media/search-create-index-portal/field-definitions.png "Hotell demo index")

1. När du är klar klickar du på **Skapa** för att spara och skapa indexet.

<a name="design"></a>

## <a name="set-attributes"></a>Ange attribut

Du kan visserligen lägga till nya fält när som helst, men befintliga fältdefinitioner är låsta under indexets hela livslängd. Av den anledningen använder många utvecklare portalen för att skapa enkla index, testa idéer och använda portalsidorna för att söka reda på en inställning. Frekvent upprepning av en indexdesign är mer effektiv om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

Analysverktyg och förslagsställare kopplas till fält innan indexet sparas. Var noga med att lägga till språkanalysatorer eller förslagsskrivare i indexdefinitionen medan du skapar den.

Strängfält är ofta är markerade som **sökbara** och **hämtningsbara**. Fält som används för att begränsa sökresultatet är **Sorterbart**, **Filtrerbart** och **Fasettbart**.

Fältattributen avgör hur ett fält används, till exempel om det används i fulltextsökning, fasetterad navigering, sorteringsåtgärder och så vidare. I följande tabell beskrivs alla attribut.

|Attribut|Beskrivning|  
|---------------|-----------------|  
|**Sökbara**|Fulltextsökbart och omfattas av lexikal analys som radbrytning under indexering. Om du anger ett sökbart fält till ett värde som ”solig dag” delas det upp internt i två enskilda token, ”solig” och ”dag”. Mer information finns i [Hur fulltextsökning fungerar](search-lucene-query-architecture.md).|  
|**filterbar**|Refereras till i **$filter**-frågor. Filtrerbara fält av typen `Edm.String` eller `Collection(Edm.String)` genomgår inte ordseparation, så jämförelserna gäller endast exakta matchningar. Om du till exempel anger ”solig dag” för ett sådant fält hittar inte `$filter=f eq 'sunny'` några matchningar, men det gör `$filter=f eq 'sunny day'`. |  
|**Sorterbar**|Som standard sorterar systemet resultaten efter bedömning, men du kan konfigurera sortering som baseras på fält i dokumenten. Fält av typen `Collection(Edm.String)` får inte vara **sorterbara**. |  
|**ansiktstable**|Används vanligtvis i en presentation av sökresultat som innehåller ett antal träffar efter kategori (till exempel hotell på en viss ort). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Fält av typen `Edm.String` som är **filtrerbara**, **sorterbara**, eller **fasettbara** får vara högst 32 kB långa. Mer information finns i [Skapa index (REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Unik identifierare för dokument i indexet. Exakt ett fält måste väljas som nyckelfält, och det måste vara av typen `Edm.String`.|  
|**kan hämtas**|Anger om fältet kan returneras i ett sökresultat. Detta är användbart om du vill använda ett fält (som *vinstmarginal*) som ett filter eller en sorterings- eller bedömningsmekanism, men inte vill att fältet ska visas för användaren. Det här attributet måste vara `true` för `key`-fält.|  

## <a name="next-steps"></a>Nästa steg

När du har skapat ett Azure Cognitive Search-index kan du gå vidare till nästa steg: [ladda upp sökbara data till indexet](search-what-is-data-import.md).

Alternativt kan du också ta en [djupare titt på index](search-what-is-an-index.md). Förutom samlingen Fält anges i index även analysverktyg, förslagsställare, bedömningsprofiler och CORS-inställningar. Portalen innehåller fliksidor för att definiera de vanligaste elementen: fält, analysverktyg och förslagsställare. Om du vill skapa eller ändra andra element kan du använda REST-API:et eller .NET SDK.

## <a name="see-also"></a>Se även

 [Så här fungerar fulltextsökning](search-lucene-query-architecture.md)  
 [REST-API för söktjänst](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

