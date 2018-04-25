---
title: Skapa ett index (portal – Azure Search) | Microsoft Docs
description: Skapa ett index med hjälp av Azure-portalen.
manager: cgronlun
author: heidisteen
ms.service: search
ms.devlang: NA
ms.topic: quickstart
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: ab0352b8c830e875afc9b1d1b006ba4d2a512d7a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Skapa ett Azure Search-index med hjälp av Azure-portalen
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-an-index.md)
> * [Portalen](search-create-index-portal.md)
> * [NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Använd den inbyggda funktionen för indexdesign i Azure-portalen som prototyp eller skapa ett [sökindex](search-what-is-an-index.md) som ska köras i din Azure Search-tjänst. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du har en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och en [Azure Search-tjänst](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Hitta din söktjänst
1. Logga in på Azure-portalsidan och granska [söktjänsterna för din prenumeration](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Välj din Azure Search-tjänst.

## <a name="name-the-index"></a>Namnge ditt index

1. Klicka på knappen **Lägg till index** i kommandofältet högst upp på sidan.
2. Namnge ditt Azure Search-index. 
   * Börja med en bokstav.
   * Använd endast gemena bokstäver, siffror och bindestreck (”-”).
   * Begränsa namnet till 60 tecken.

  Indexnamnet blir en del av den slutpunkts-URL som används i anslutningar till indexet och för att skicka HTTP-förfrågningar med Azure Search REST-API:t.

## <a name="define-the-fields-of-your-index"></a>Definiera fälten i ditt index

Indexsammansättningen innehåller en *fält samling* som definierar sökbara data i ditt index. Mer specifikt anges strukturen för dokument som du laddar upp separat. Fältsamlingen innehåller obligatoriska och valfria fält, namngivna och typdefinierade, med indexattribut som avgör hur fältet kan användas.

1. På bladet **Lägg till index** klickar du på **Fält >** så att fältdefinitionsbladet öppnas. 

2. Godkänn det genererade *nyckel*fältet av typen Edm.String. Som standard heter fältet *id* men du kan byta namn på det så länge strängen följer [namngivningsreglerna](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Ett nyckelfält är obligatoriskt för alla Azure Search-index, och det måste vara en sträng.

3. Lägg till fält om du vill fullständigt specificera dokument som du ska ladda upp. Om dokumenten består av ett *id*, *hotellnamn*, *adress*, *ort* och *region* skapar du ett motsvarande fält för vart och ett av dessa i indexet. I [designriktlinjerna i avsnittet nedan](#design) får du hjälp med att ange attribut.

4. Du kan också lägga till fält som används internt i filteruttryck. Attribut i fältet kan ställas in för att utesluta fält från sökningar.

5. När du är klar klickar du på **OK** för att spara och skapa indexet.

## <a name="tips-for-adding-fields"></a>Tips för att lägga till fält

Det är ganska tangentbordsintensivt att skapa ett index i portalen. Minimera arbetet genom att följa det här arbetsflödet:

1. Skapa först fältlistan genom att ange namn och datatyper.

2. Använd sedan kryssrutorna överst i varje attribut för att massaktivera inställningen för alla fält, och avmarkera kryssrutorna för de få, enskilda fält där inställningen inte ska användas. Strängfält är till exempel vanligtvis sökbara. Därför kan du klicka på **Hämtningsbart** och **Sökbart** om du vill både returnera värdena för fältet i sökresultatet och tillåta fulltextsökning i fältet. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Designriktlinjer för att ange attribut

Du kan visserligen lägga till nya fält när som helst, men befintliga fältdefinitioner är låsta under indexets hela livslängd. Av den anledningen använder många utvecklare portalen för att skapa enkla index, testa idéer och använda portalsidorna för att söka reda på en inställning. Frekvent upprepning av en indexdesign är mer effektiv om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

Analysverktyg och förslagsställare kopplas till fält innan indexet sparas. Se till att klicka dig igenom varje fliksida och lägga till språkanalysverktyg och förslagsställare till din indexdefinition.

Strängfält är ofta är markerade som **sökbara** och **hämtningsbara**.

Fält som används för att begränsa sökresultatet är **Sorterbart**, **Filtrerbart** och **Fasettbart**.

Fältattributen avgör hur ett fält används, till exempel om det används i fulltextsökning, fasetterad navigering, sorteringsåtgärder och så vidare. I följande tabell beskrivs alla attribut.

|Attribut|Beskrivning|  
|---------------|-----------------|  
|**sökbart**|Fulltextsökbart och omfattas av lexikal analys som radbrytning under indexering. Om du anger ett sökbart fält till ett värde som ”solig dag” delas det upp internt i två enskilda token, ”solig” och ”dag”. Mer information finns i [Hur fulltextsökning fungerar](search-lucene-query-architecture.md).|  
|**filtrerbart**|Refereras till i **$filter**-frågor. Filtrerbara fält av typen `Edm.String` eller `Collection(Edm.String)` genomgår inte ordseparation, så jämförelserna gäller endast exakta matchningar. Om du till exempel anger ”solig dag” för ett sådant fält hittar inte `$filter=f eq 'sunny'` några matchningar, men det gör `$filter=f eq 'sunny day'`. |  
|**sorterbar**|Som standard sorterar systemet resultaten efter bedömning, men du kan konfigurera sortering som baseras på fält i dokumenten. Fält av typen `Collection(Edm.String)` får inte vara **sorterbara**. |  
|**fasettbart**|Används vanligtvis i en presentation av sökresultat som innehåller ett antal träffar efter kategori (till exempel hotell på en viss ort). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Fält av typen `Edm.String` som är **filtrerbara**, **sorterbara**, eller **fasettbara** får vara högst 32 kB långa. Mer information finns i [Skapa index (REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**nyckel**|Unik identifierare för dokument i indexet. Exakt ett fält måste väljas som nyckelfält, och det måste vara av typen `Edm.String`.|  
|**hämtningsbart**|Anger om fältet kan returneras i ett sökresultat. Detta är användbart om du vill använda ett fält (som *vinstmarginal*) som ett filter eller en sorterings- eller bedömningsmekanism, men inte vill att fältet ska visas för användaren. Det här attributet måste vara `true` för `key`-fält.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Skapa det hotellindex som används i API-avsnittsexempel

API-dokumentationen för Azure Search innehåller kodexempel med ett enkelt *hotell*index. Skärmbilderna nedan visar den indexdefinition, inklusive det franska språkanalysverktyget som angavs vid indexdefinitionen, som du kan återskapa som en praktisk övning i portalen.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat ett Azure Search-index kan du gå till nästa steg: [ladda upp sökbara data till indexet](search-what-is-data-import.md).

Du kan också ta en närmare titt på index. Förutom samlingen Fält anges i index även analysverktyg, förslagsställare, bedömningsprofiler och CORS-inställningar. Portalen innehåller fliksidor för att definiera de vanligaste elementen: fält, analysverktyg och förslagsställare. Om du vill skapa eller ändra andra element kan du använda REST-API:et eller .NET SDK.

## <a name="see-also"></a>Se även

 [Så här fungerar fulltextsökning](search-lucene-query-architecture.md)  
 [REST-API för söktjänst](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

