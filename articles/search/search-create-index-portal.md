---
title: Skapa ett Azure Search-index i Azure portal – Azure Search
description: Lär dig hur du skapar ett index för Azure Search med hjälp av en inbyggd portal index designers.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817298"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Skapa ett Azure Search-index i portalen

Azure Search innehåller ett inbyggt index designer i portal användbar för prototyper eller skapa en [sökindex](search-what-is-an-index.md) finns på Azure Search-tjänsten. Verktyget används för schemat konstruktion. När du sparar definitionen blir ett tomt index fullständigt uttrycks i Azure Search. Hur du läser in den med sökbara data är upp till dig.

Index-designer är bara en metod för att skapa ett index. Programmässigt, kan du skapa ett index med hjälp av den [.NET](search-create-index-dotnet.md) eller [REST](search-create-index-rest-api.md) API: er.

## <a name="start-index-designer"></a>Starta index designer

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna instrumentpanelen för tjänsten. Du kan klicka på **Alla tjänster** i indexet om du vill söka efter befintliga ”söktjänster” i den nuvarande prenumerationen. 

2. Klicka på den **Lägg till index** länken i kommandofältet överst på sidan.

   ![Lägg till index länk i kommandofältet](media/search-create-index-portal/add-index.png "Lägg till index länk i kommandofältet")

3. Namnge ditt Azure Search-index. Namn på index refereras i indexerings- och åtgärder. Indexnamnet blir en del av den slutpunkts-URL som används i anslutningar till indexet och för att skicka HTTP-förfrågningar med Azure Search REST-API:t.

   * Börja med en bokstav.
   * Använd endast gemena bokstäver, siffror och bindestreck (”-”).
   * Begränsa namnet till 60 tecken.

## <a name="add-fields"></a>Lägg till fält

Indexsammansättningen innehåller en *fält samling* som definierar sökbara data i ditt index. Helt och hållet, fältsamlingen anger strukturen för dokument som du laddar upp separat. En samling fält innehåller obligatoriska och valfria fält, namngivna och typdefinierade, med indexattribut som avgör hur fältet kan användas.

1. Lägg till fält om du vill fullständigt specificera dokument som du kommer att överföra, ställa in en [datatypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för vart och ett. Exempel: om dokumenten består av en *hotell-id*, *hotellnamn*, *adress*, *Stad*, och *region*, skapa ett motsvarande fält för vart och ett i indexet. Granska den [designriktlinjerna i avsnittet nedan](#design) hjälp med att ange attribut.

2. Ange en *nyckel* fält av typen Edm.String. Värden för det här fältet måste identifiera varje dokument. Som standard heter fältet *id* men du kan byta namn på det så länge strängen följer [namngivningsreglerna](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Om din fältsamlingen innehåller till exempel *hotell-id*, väljer du som för din nyckel. Ett nyckelfält är obligatoriskt för alla Azure Search-index, och det måste vara en sträng.

3. Ange attribut för varje fält. Index-designer omfattar inte några attribut som är ogiltiga för datatypen, men föreslår inte vad som ska ingå. Granska riktlinjerna i nästa avsnitt för att förstå vilka attribut som är för.

    API-dokumentationen för Azure Search innehåller kodexempel med ett enkelt *hotell*index. I skärmbilden nedan ser du den Indexdefinition, inklusive det franska språkanalysverktyget som angavs vid indexdefinitionen, som du kan återskapa som en praktisk övning i portalen.

    ![Hotels demo indexet](media/search-create-index-portal/field-definitions.png "Hotels demo indexet")

4. När du är klar klickar du på **skapa** att spara och skapa indexet.

<a name="design"></a>

## <a name="set-attributes"></a>Ange attribut

Du kan visserligen lägga till nya fält när som helst, men befintliga fältdefinitioner är låsta under indexets hela livslängd. Av den anledningen använder många utvecklare portalen för att skapa enkla index, testa idéer och använda portalsidorna för att söka reda på en inställning. Frekvent upprepning av en indexdesign är mer effektiv om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

Analysverktyg och förslagsställare kopplas till fält innan indexet sparas. Glöm inte att lägga till språkanalysverktyg och förslagsställare till din Indexdefinition när du skapar den.

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

Du kan också du kan också ta en [närmare titt på index](search-what-is-an-index.md). Förutom samlingen Fält anges i index även analysverktyg, förslagsställare, bedömningsprofiler och CORS-inställningar. Portalen innehåller fliksidor för att definiera de vanligaste elementen: Fält, analysverktyg och förslagsställare. Om du vill skapa eller ändra andra element kan du använda REST-API:et eller .NET SDK.

## <a name="see-also"></a>Se också

 [Så här fungerar fulltextsökning](search-lucene-query-architecture.md)  
 [REST-API för söktjänst](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

