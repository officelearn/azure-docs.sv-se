---
title: Importera data till ett Sök index med Azure Portal-Azure Search
description: Lär dig hur du använder guiden Importera data i Azure Portal för att crawla Azure-data från Cosmos DB, Blob Storage, Table Storage, SQL Database och SQL Server på virtuella Azure-datorer.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648251"
---
# <a name="import-data-wizard-for-azure-search"></a>Guiden Importera data för Azure Search

Med guiden **Importera data** i instrumentpanelen för Azure Search på Azure Portal kan du läsa in data i ett index. I bakgrunden konfigurerar och anropar guiden en *data källa*, *index*och indexerare – automatisera flera steg i indexerings processen: 

* Ansluter till en extern data källa i samma Azure-prenumeration.
* Alternativt integreras optisk tecken läsning eller bearbetning av naturligt språk för att extrahera text från ostrukturerade data.
* Genererar ett index baserat på data sampling och metadata för den externa data källan.
* Crawlar data källan för sökbart innehåll, serialisering och inläsning av JSON-dokument i indexet.

Guiden kan inte ansluta till ett fördefinierat index eller köra en befintlig indexerare, men i guiden kan du konfigurera ett nytt index eller indexerare så att den stöder den struktur och de beteenden du behöver.

Har du inte provat Azure Search än? Stega genom [snabb starten: Importera, indexera och fråga med hjälp av Portal](search-get-started-portal.md) verktyg för att testa import och indexering med hjälp av **import data** och den inbyggda exempel data uppsättningen för fastighets fastighets.

## <a name="start-importing-data"></a>Starta importen av data

I det här avsnittet beskrivs hur du startar guiden och ger en översikt över varje steg på hög nivå.

1. I [Azure Portal](https://portal.azure.com)öppnar du sidan Sök tjänst från instrument panelen eller [söker efter tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänst listan.

2. På sidan tjänst översikt längst upp klickar du på **Importera data**.

   ![Importera data kommando i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

   > [!NOTE]
   > Du kan starta **Importera data** från andra Azure-tjänster, inklusive Azure Cosmos DB, Azure SQL Database och Azure Blob Storage. Sök efter **Lägg till Azure Search** i det vänstra navigerings fönstret på sidan tjänst översikt.

3. Guiden öppnar för att **ansluta till dina data**, där du kan välja en extern data källa som ska användas för den här importen. Det finns flera saker att känna till i det här steget, så se till att läsa avsnittet [data källa](#data-source-inputs) indata för mer information.

   ![Guiden Importera data i portalen](./media/search-import-data-portal/import-data-wizard-startup.png "Guiden Importera data för Azure Search")

4. Härnäst ska du **lägga till kognitiv sökning**, om du vill inkludera OCR (optisk tecken läsning) av text i bildfiler eller text analys över ostrukturerade data. AI-algoritmer från Cognitive Services hämtas för den här uppgiften. Det finns två delar i det här steget:
  
   Anslut först [en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md) till en Azure Search färdigheter.
  
   Sedan väljer du vilka AI-berikare som ska ingå i färdigheter. En genom gångs demonstration finns i den här [snabb](cognitive-search-quickstart-blob.md)starten.

   Om du bara vill importera data hoppar du över det här steget och går direkt till index definitionen.

5. Nu ska du **Anpassa mål indexet**, där du kan godkänna eller ändra det index schema som visas i guiden. Guiden härleder fälten och data typerna genom att sampla data och läsa metadata från den externa data källan.

   För varje fält, [kontrol lera index](#index-definition) -attributen för att aktivera vissa beteenden. Om du inte väljer några attribut går det inte att använda indexet. 

6. Nu ska du **skapa en indexerare**, som är en produkt i den här guiden. En indexerare är en Crawler som extraherar sökbara data och metadata från en extern Azure-datakälla. Genom att välja data källa och bifoga färdighetsuppsättningar (valfritt) och ett index har du konfigurerat en indexerare när du går igenom varje steg i guiden.

   Ge indexeraren ett namn och klicka på **Skicka** för att starta import processen. 

Du kan övervaka indexeringen i portalen genom att klicka på indexeraren i listan **indexerare** . Under tiden dokumenten läses in ökar antalet dokument för det index som du har definierat. Ibland kan det ta några minuter för portalsidan att hämta de senaste uppdateringarna.

Indexet är klart att fråga så snart det första dokumentet har lästs in. Du kan använda [Sök Utforskaren](search-explorer.md) för den här uppgiften.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Data källans indata

Guiden **Importera data** skapar ett beständigt data käll objekt som anger anslutnings information till en extern data källa. Datakällobjektet används exklusivt med indexerare och kan [](search-indexer-overview.md) skapas för följande data Källor: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure-Table Storage](search-howto-indexing-azure-tables.md) (stöds inte för [kognitiva Sök](cognitive-search-concept-intro.md) pipeliner)

Du kan bara importera från en enskild tabell, databas visning eller motsvarande data struktur, men strukturen kan innehålla hierarkiska eller kapslade under strukturer. Mer information finns i [så här modellerar du komplexa typer](search-howto-complex-data-types.md).

Du bör skapa den här data strukturen innan du kör guiden och den måste innehålla innehåll. Kör inte guiden **Importera data** på en tom data källa.

|  Val | Beskrivning |
| ---------- | ----------- |
| **Befintlig datakälla** |Om du redan har definierat indexerare i söktjänsten, kan du välja en definition av en befintlig datakälla för en annan import. I Azure Search används endast data käll objekt av indexerare. Du kan skapa ett data käll objekt program mässigt eller via guiden **Importera data** .|
| **Exempel**| Azure Search är värd för en kostnads fri Global Azure SQL-databas som du kan använda för att lära dig att importera och fråga efter begär anden i Azure Search. Gå till [Snabbstart: Importera, indexera och fråga med Portal verktyg](search-get-started-portal.md) för en genom gång. |
| **Azure SQL Database** |Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **SQL Server på virtuella Azure-datorer** |Ange ett fullständigt tjänst namn, användar-ID och lösen ord samt databas som anslutnings sträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. Instruktioner finns i [SQL VM-anslutning till Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **Cosmos DB** |Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga för att förenkla eller filtrera rad uppsättningen eller lämna frågan tom. En fråga krävs inte i den här guiden.|
| **Azure Blob Storage** |Kraven innefattar lagringskonto och en container. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under containern. Se [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md) för mer information. |
| **Azure Table Storage** |Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage](search-howto-indexing-azure-tables.md) för mer information. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Indexera attribut

Guiden **Importera data** genererar ett index som kommer att fyllas i med dokument som hämtats från Indatakällan för indata. 

Se till att du har definierat följande element för ett funktionellt index.

1. Ett fält måste markeras som en **nyckel**, som används för att unikt identifiera varje dokument. **Nyckeln** måste vara en *EDM. String*. 

   Om fältvärdena innehåller blank steg eller tank streck, måste du ange alternativet **bas-64-kodning** i steget **skapa ett indexerare** , under **Avancerade alternativ**för att utelämna verifierings kontrollen för dessa tecken.

1. Ange indexattribut för varje fält. Om du väljer inga attribut är indexet i grunden tomt, förutom det obligatoriska nyckel fältet. Välj minst ett av attributen för varje fält.
   
   + **Hämtnings** bar returnerar fältet i Sök resultatet. Alla fält som tillhandahåller innehåll till Sök resultat måste ha det här attributet. Att ställa in det här fältet påverkar inte index storleken märkbart.
   + **Filterable** tillåter att fältet refereras till i filter uttryck. Alla fält som används i ett **$filter** -uttryck måste ha det här attributet. Filter uttryck är för exakta matchningar. Eftersom text strängar förblir intakta krävs ytterligare lagrings utrymme för att rymma orda Grant-innehållet.
   + **Sökbart** möjliggör full texts ökning. Alla fält som används i kostnads fria formulär frågor eller i frågeuttryck måste ha det här attributet. Inverterade index skapas för varje fält som du markerar som sökbart.

1. Du kan också ange följande attribut efter behov:

   + **Sorterbar** gör att fältet kan användas i en sortering. Alla fält som används i ett **$OrderBy** -uttryck måste ha det här attributet.
   + **Fasettable** aktiverar fältet för fasett-navigering. Endast fält som har marker ATS som Filterable kan markeras som **fasettable**.

1. Ange en **analys** om du vill använda språkförbättrad indexering och frågor. Standardvärdet är standard *Lucene* men du kan välja *Microsoft English* om du vill använda Microsofts analys för avancerad lexikal bearbetning, till exempel att lösa oregelbundna Substantiv-och verb-formulär.

   + Välj **sökbar** för att aktivera **analys** listan.
   + Välj en analys som finns i listan. 
   
   Det går endast att ange språkanalysverktyg för närvarande. Om du använder ett anpassat analysverktyg eller en icke-språkanalys som t.ex. nyckelord, mönster och så vidare, kommer kod att krävas. Mer information om analys verktyg finns i [skapa ett index för dokument på flera språk](search-language-support.md).

1. Markera kryss rutan för att markera kryss rutan för att aktivera fråge förslag för Skriv steget i de valda fälten.


## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om indexerare:

* [Indexera Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexera Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexera Table Storage](search-howto-indexing-azure-tables.md)