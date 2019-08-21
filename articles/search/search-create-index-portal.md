---
title: Skapa ett Azure Search-index i Azure Portal-Azure Search
description: Lär dig hur du skapar ett index för Azure Search med hjälp av en inbyggd Portal index designers.
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.openlocfilehash: fec81cd9660348d492b1dabd24ac689f2b06e880
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638815"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Skapa ett Azure Search-index i portalen

Azure Search innehåller en inbyggd indexerad designer i portalen som är användbar för prototyper eller skapar ett [sökindex](search-what-is-an-index.md) som finns på din Azure Search-tjänst. Verktyget används för schema konstruktion. När du sparar definitionen, uttrycks ett tomt index fullständigt i Azure Search. Hur du läser in det med sökbara data är upp till dig.

Index designern är bara en metod för att skapa ett index. Program mässigt kan du skapa ett index med hjälp av [.net](search-create-index-dotnet.md) -eller [rest](search-create-index-rest-api.md) -API: er.

## <a name="start-index-designer"></a>Starta index designern

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna instrumentpanelen för tjänsten. Du kan klicka på **Alla tjänster** i indexet om du vill söka efter befintliga ”söktjänster” i den nuvarande prenumerationen. 

2. Klicka på länken **Lägg till index** i kommando fältet högst upp på sidan.

   ![Lägg till index länk i kommando fältet](media/search-create-index-portal/add-index.png "Lägg till index länk i kommando fältet")

3. Namnge ditt Azure Search-index. Index namn refereras till i indexerings-och fråge åtgärder. Indexnamnet blir en del av den slutpunkts-URL som används i anslutningar till indexet och för att skicka HTTP-förfrågningar med Azure Search REST-API:t.

   * Börja med en bokstav.
   * Använd endast gemena bokstäver, siffror och bindestreck (”-”).
   * Begränsa namnet till 60 tecken.

## <a name="add-fields"></a>Lägg till fält

Indexsammansättningen innehåller en *fält samling* som definierar sökbara data i ditt index. Helt, anger fält samlingen dokument strukturen som du överför separat. En fält samling innehåller obligatoriska och valfria fält, namngivna och skrivna, med indexattribut som avgör hur fältet kan användas.

1. Lägg till fält för att ange de dokument som ska överföras fullständigt, och ange en [datatyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för var och en. Om dokument till exempel består av ett *hotell-ID*, *hotell-namn*, *adress*, *stad*och *region*, skapar du ett motsvarande fält för var och en i indexet. Läs [rikt linjerna för design i avsnittet nedan](#design) om du vill ha hjälp med att ställa in attribut.

2. Ange ett *nyckel* fält av typen EDM. String. Värdena i det här fältet måste unikt identifiera varje dokument. Som standard heter fältet *id* men du kan byta namn på det så länge strängen följer [namngivningsreglerna](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Om dina fält exempelvis innehåller *hotell-ID*väljer du det för din nyckel. Ett nyckelfält är obligatoriskt för alla Azure Search-index, och det måste vara en sträng.

3. Ange attribut för varje fält. Index designern undantar inte attribut som är ogiltiga för data typen, men föreslår inte vad som ska tas med. Läs anvisningarna i nästa avsnitt för att förstå vad attributen är för.

    API-dokumentationen för Azure Search innehåller kodexempel med ett enkelt *hotell*index. I skärm bilden nedan kan du se index definitionen, inklusive den franska språk analys som anges under index definitionen, som du kan återskapa som en övning i portalen.

    ![Demo index för hotell](media/search-create-index-portal/field-definitions.png "Demo index för hotell")

4. När du är färdig klickar du på **skapa** för att spara och skapa indexet.

<a name="design"></a>

## <a name="set-attributes"></a>Ange attribut

Du kan visserligen lägga till nya fält när som helst, men befintliga fältdefinitioner är låsta under indexets hela livslängd. Av den anledningen använder många utvecklare portalen för att skapa enkla index, testa idéer och använda portalsidorna för att söka reda på en inställning. Frekvent upprepning av en indexdesign är mer effektiv om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

Analysverktyg och förslagsställare kopplas till fält innan indexet sparas. Se till att lägga till språk analys verktyg eller förslag till din index definition när du skapar den.

Strängfält är ofta är markerade som **sökbara** och **hämtningsbara**. Fält som används för att begränsa sökresultatet är **Sorterbart**, **Filtrerbart** och **Fasettbart**.

Fältattributen avgör hur ett fält används, till exempel om det används i fulltextsökning, fasetterad navigering, sorteringsåtgärder och så vidare. I följande tabell beskrivs alla attribut.

|Attribut|Beskrivning|  
|---------------|-----------------|  
|**sökbart**|Fulltextsökbart och omfattas av lexikal analys som radbrytning under indexering. Om du anger ett sökbart fält till ett värde som ”solig dag” delas det upp internt i två enskilda token, ”solig” och ”dag”. Mer information finns i [Hur fulltextsökning fungerar](search-lucene-query-architecture.md).|  
|**filtrerbart**|Refereras till i **$filter**-frågor. Filtrerbara fält av typen `Edm.String` eller `Collection(Edm.String)` genomgår inte ordseparation, så jämförelserna gäller endast exakta matchningar. Om du till exempel anger ”solig dag” för ett sådant fält hittar inte `$filter=f eq 'sunny'` några matchningar, men det gör `$filter=f eq 'sunny day'`. |  
|**sorterbar**|Som standard sorterar systemet resultaten efter bedömning, men du kan konfigurera sortering som baseras på fält i dokumenten. Fält av typen `Collection(Edm.String)` får inte vara **sorterbara**. |  
|**fasettbart**|Används vanligtvis i en presentation av sökresultat som innehåller ett antal träffar efter kategori (till exempel hotell på en viss ort). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Fält av typen `Edm.String` som är **filtrerbara**, **sorterbara**, eller **fasettbara** får vara högst 32 kB långa. Mer information finns i [Skapa index (REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**nyckel**|Unik identifierare för dokument i indexet. Exakt ett fält måste väljas som nyckelfält, och det måste vara av typen `Edm.String`.|  
|**hämtningsbart**|Anger om fältet kan returneras i ett sökresultat. Detta är användbart om du vill använda ett fält (som *vinstmarginal*) som ett filter eller en sorterings- eller bedömningsmekanism, men inte vill att fältet ska visas för användaren. Det här attributet måste vara `true` för `key`-fält.|  

## <a name="next-steps"></a>Nästa steg

När du har skapat ett Azure Search-index kan du gå till nästa steg: [ladda upp sökbara data till indexet](search-what-is-data-import.md).

Alternativt kan du också ta en [djupare titt på index](search-what-is-an-index.md). Förutom samlingen Fält anges i index även analysverktyg, förslagsställare, bedömningsprofiler och CORS-inställningar. Portalen innehåller tabbade sidor för att definiera de vanligaste elementen: Fält, analyser och förslag. Om du vill skapa eller ändra andra element kan du använda REST-API:et eller .NET SDK.

## <a name="see-also"></a>Se också

 [Så här fungerar fulltextsökning](search-lucene-query-architecture.md)  
 [REST-API för söktjänst](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

