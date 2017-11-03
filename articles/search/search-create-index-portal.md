---
title: Skapa ett index (portal - Azure Search) | Microsoft Docs
description: "Skapa ett index med hjälp av Azure-portalen."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Skapa ett Azure Search-index med hjälp av Azure-portalen
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-an-index.md)
> * [Portalen](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Använda inbyggda index designer i Azure-portalen som prototyp eller skapa en [sökindex](search-what-is-an-index.md) ska köras på din Azure Search-tjänst. 

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och [Azure Search-tjänsten](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Hitta din söktjänst
1. Logga in på sidan för Azure-portalen och granska de [söktjänster för din prenumeration](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Välj din Azure Search-tjänst.

## <a name="name-the-index"></a>Namn på index

1. Klicka på den **Lägg till index** knappen i kommandofältet längst upp på sidan.
2. Namnge ditt Azure Search-index. 
   * Börja med en bokstav.
   * Använd endast gemener, siffror eller bindestreck (”-”).
   * Begränsa namn till 60 tecken.

  Indexnamnet blir en del av slutpunkts-URL som används på anslutningar till indexet och för att skicka HTTP-begäranden med Azure Search REST API.

## <a name="define-the-fields-of-your-index"></a>Definiera fälten för ditt index

Index sammansättning innehåller en *fält samling* som definierar sökbara data i ditt index. Mer specifikt anger strukturen för dokument som du överför separat. Samlingen fält innehåller obligatoriska och valfria fält med namnet och skrivits med indexattribut för att avgöra hur fältet kan användas.

1. I den **lägga till indexet** bladet, klickar du på **fält >** på bilden öppna bladet fältet definition. 

2. Acceptera den genererade *nyckeln* fältet av typen Edm.String. Som standard heter fältet *id* men du kan byta namn på det så länge strängen uppfyller [namngivningsregler](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Ett nyckelfält är obligatoriskt för varje Azure Search index och det måste vara en sträng.

3. Lägga till fält om du vill ange fullständigt dokument som du kommer att överföra. Om dokument som består av en *id*, *hotell namnet*, *adress*, *Stad*, och *region*, skapa ett motsvarande fält för var och en i indexet. Granska de [utforma riktlinjerna i avsnittet nedan](#design) för hjälp med att attribut.

4. Du kan också lägga till alla fält som används internt i filteruttryck. Attribut i fältet kan ställas in för att utesluta fält från sökningar.

5. När du är klar klickar du på **OK** att spara och skapa indexet.

## <a name="tips-for-adding-fields"></a>Tips för att lägga till fält

Skapa ett index i portalen är beräkningsintensiva tangentbord. Minimera steg genom att följa det här arbetsflödet:

1. Skapa först fältlistan genom att ange namn och ange datatyper.

2. Sedan används attributet kryssrutorna längst upp i varje grupp aktivera inställningen för alla fält och avmarkerar selektivt kryssrutorna för få fält som inte kräver den. Till exempel är strängfält vanligtvis sökbar. Därför kan du klicka på **Retrievable** och **sökbara** både returnera värdet för fältet i sökresultat som tillåter fulltextsökning i fältet. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Riktlinjer för designen för att ange attribut

Du kan lägga till nya fält när som helst, är befintliga fältdefinitioner skrivskyddade livslängden för indexet. Därför kan använda utvecklare vanligtvis portalen för att skapa enkla index, testa idéer eller med hjälp av portalens sidor för att leta upp en inställning. Ofta iteration via en design för indexet är effektivare om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

Analyzers och suggesters är kopplade till fält innan spara indexet. Se till att klicka dig igenom varje fliksida lägga till språkanalys eller suggesters Indexdefinition.

Strängfält ofta är markerade som **sökbara** och **Retrievable**.

Fälten som används för att begränsa sökresultatet är **Sortable**, **Filterable**, och **Facetable**.

Fältattribut avgör hur ett fält används, till exempel om den används i fulltextsökning, fasetterad navigering, sorteringsåtgärder och så vidare. I följande tabell beskrivs alla attribut.

|Attribut|Beskrivning|  
|---------------|-----------------|  
|**sökbara**|Fulltext-sökbara omfattas lexikala analys, till exempel radbrytning under indexeringen. Om du anger ett sökbara fält till ett värde som ”skiner” delas internt den upp i enskilda token ”Soligt” och ”dag”. Mer information finns i [hur full text search fungerar](search-lucene-query-architecture.md).|  
|**Filtrera**|Refereras till i **$filter** frågor. Filtrera fält av typen `Edm.String` eller `Collection(Edm.String)` inte genomgår radbrytning, så att jämförelser för endast exakt matchar. Om du ställer in sådana ett fält f ”skiner”, till exempel `$filter=f eq 'sunny'` hittar inga matchningar men `$filter=f eq 'sunny day'` kommer. |  
|**sorterbar**|Som standard sorterar systemet resultat poäng men du kan konfigurera sortering som baseras på fält i dokumenten. Fält av typen `Collection(Edm.String)` får inte vara **sorterbar**. |  
|**facetable**|Används vanligtvis i en presentation av sökresultat som innehåller ett antal träffar efter kategori (till exempel hotell på en viss ort). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Fält av typen `Edm.String` som är **filtrera**, **sorterbar**, eller **facetable** får vara högst 32 kB längd. Mer information finns i [Create Index (REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**nyckel**|Unik identifierare för dokument i indexet. Exakt ett fält måste väljas som nyckelfältet och den måste vara av typen `Edm.String`.|  
|**strängfält**|Anger om fältet kan returneras i sökresultatet. Detta är användbart när du vill använda ett fält (som *vinst*) som ett filter, sortera eller bedömningen mekanism, men inte vill att fält som ska visas för slutanvändaren. Det här attributet måste vara `true` för `key` fält.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Skapa hotell indexet som används i exempel API-avsnitt

Azure Search-API-dokumentationen innehåller kodexempel med en enkel *hotell* index. I skärmbilderna nedan ser du indexdefinitionen, inklusive den franska versionen analyzer som anges under indexdefinitionen, som du kan återskapa som en övning i portalen.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat ett Azure Search-index kan du gå till nästa steg: [överföra sökbara data till indexet](search-what-is-data-import.md).

Alternativt kan du också ta en djupare inblick i index. Förutom samlingen fält anger också ett index analyzers, suggesters, bedömningsprofil profiler och CORS-inställningarna. Portalen innehåller sidor för att definiera de vanligaste elementen: fält, analyzers och suggesters. Du kan använda REST API: et eller .NET SDK för att skapa eller ändra andra element.

## <a name="see-also"></a>Se även

 [Så här fungerar fulltextsökning](search-lucene-query-architecture.md)  
 [Söktjänsten REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

