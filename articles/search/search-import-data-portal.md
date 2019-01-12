---
title: Importera data till ett search-index med hjälp av Azure portal – Azure Search
description: Lär dig hur du använder guiden Importera Data i Azure-portalen för att uppdatera Azure data från Cosmos DB, Blob storage, table storage, SQL Database och SQL Server på virtuella Azure-datorer.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ee1b2a40dbcbd53a758ac71f30401778ef07e872
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229765"
---
# <a name="import-data-wizard-for-azure-search"></a>Guiden Importera data för Azure Search

Med guiden **Importera data** i instrumentpanelen för Azure Search på Azure Portal kan du läsa in data i ett index. I bakgrunden i guiden konfigurerar och aktiverar en *datakälla*, *index*, och *indexeraren* – vilket automatiserar flera steg i indexeringsprocessen: 

* Ansluter till en extern datakälla i samma Azure-prenumeration.
* Du kan också integreras optisk teckenläsning eller naturligt språk bearbetning för att extrahera text från Ostrukturerade data.
* Genererar ett index baserat på datasampling och metadata för den externa datakällan.
* Crawlar datakällan för sökbart innehåll serialisering och läsa in JSON-dokument i indexet.

Guiden kan inte ansluta till ett fördefinierat index eller köra en befintlig indexerare, men i guiden kan du konfigurera ett nytt index eller en indexerare som stöd för struktur och beteenden som du behöver.

Har du inte provat Azure Search än? Gå igenom den [Snabbstart: Importera, index- och fråga med hjälp av portal-verktyg](search-get-started-portal.md) kan provköra importera och indexering med **dataimport** och inbyggda realestate-provdatauppsättning.

## <a name="start-importing-data"></a>Börja importera data

Det här avsnittet beskrivs hur du startar guiden och ger en översikt över varje steg.

1. I den [Azure-portalen](https://portal.azure.com), öppna söktjänstsidan från instrumentpanelen eller [hitta din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänstelistan.

2. I tjänstöversiktssidan högst upp, klickar du på **dataimport**.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "starta guiden Importera data")

   > [!NOTE]
   > Du kan starta **dataimport** från andra Azure-tjänster, inklusive Azure Cosmos DB, Azure SQL Database och Azure Blob storage. Leta efter **Lägg till Azure Search** i det vänstra navigeringsfönstret på översiktssidan för tjänsten.

3. Guiden öppnar **Anslut till dina data**, där du kan välja en extern datakälla som ska användas för den här importen. Det finns flera saker att veta om det här steget, så var noga med att läsa den [datakällans indata](#data-source-inputs) mer information.

   ![Guiden Importera data i portalen](./media/search-import-data-portal/import-data-wizard-startup.png "i guiden Importera data för Azure Search")

4. Nästa är **Lägg till kognitiv sökning**, om du vill inkludera optisk teckenläsning (OCR) av text i bildfiler eller textanalys Ostrukturerade data. AI-algoritmer från Cognitive Services hämtas för den här uppgiften. Det finns två delar i det här steget:
  
   Först [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md) till ett Azure Search-kompetens.
  
   Dessutom välja vilka AI enrichments ska ingå i gruppens kunskaper avgör. En genomgång demonstration finns i den här [snabbstarten](cognitive-search-quickstart-blob.md).

   Om du vill importera data, hoppa över det här steget och gå direkt till indexdefinitionen.

5. Nästa är **anpassa målindex**, där du kan acceptera eller ändra indexschemat som visas i guiden. Guiden härleder fält och datatyper genom att sampla data och läsa metadata från den externa datakällan.

   För varje fält [Kontrollera indexattributen](#index-definition) att aktivera beteendet. Om du inte väljer några attribut, ditt index inte kan användas. 

6. Nästa är **skapa en indexerare**, vilket är en produkt i den här guiden. En indexerare är en crawler som extraherar sökbara data och metadata från en extern datakälla för Azure. Genom att välja datakälla och koppla kompetens (valfritt) och ett index har konfigurerar du en indexerare för varje steg i guiden.

   Ge ett namn för indexeraren och klicka på **skicka** att starta importen. 

Du kan övervaka indexeringen i portalen genom att klicka på indexerare i den **indexerare** lista. Under tiden dokumenten läses in ökar antalet dokument för det index som du har definierat. Ibland kan det ta några minuter för portalsidan att hämta de senaste uppdateringarna.

Indexet är redo för frågor när det första dokumentet har lästs in. Du kan använda [Sökutforskaren](search-explorer.md) för den här uppgiften.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Inmatningar av referensdata källa

Den **dataimport** guiden skapar ett beständigt datakällobjekt att ange information om anslutningen till en extern datakälla. Datakällobjektet uteslutande används tillsammans med [indexerare](search-indexer-overview.md) och kan skapas för följande datakällor: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) (stöds inte för [kognitiv sökning](cognitive-search-concept-intro.md) pipelines)

En utjämnad datauppsättning är en obligatorisk inmatning. Du kan bara importera från en enskild tabell, databasvy eller likvärdig datastruktur. Innan du kör guiden bör du skapa den här datastrukturen.

|  Val | Beskrivning |
| ---------- | ----------- |
| **Befintlig datakälla** |Om du redan har definierat indexerare i söktjänsten, kan du välja en definition av en befintlig datakälla för en annan import. Datakällobjekt som endast används av indexerare i Azure Search. Du kan skapa ett datakällobjekt programmässigt eller via den **dataimport** guiden.|
| **Exempel**| Azure Search är värd för en kostnadsfri offentlig Azure SQL-databas som du kan använda om du vill veta mer om importera och fråga begäranden i Azure Search. Gå till [Snabbstart: Importera, index- och fråga med hjälp av portal-verktyg](search-get-started-portal.md) en genomgång. |
| **Azure SQL Database** |Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **SQL Server på virtuella Azure-datorer** |Ange ett fullständigt kvalificerat namn, användar-ID och lösenord och databasen som en anslutningssträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. Instruktioner finns i [SQL VM-anslutning till Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **Azure Cosmos DB** |Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga för att utjämna eller filtrera raduppsättningen, eller identifiera ändrade dokument för efterföljande datauppdateringar. |
| **Azure Blob Storage** |Kraven innefattar lagringskonto och en container. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under containern. Se [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md) för mer information. |
| **Azure Table Storage** |Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage](search-howto-indexing-azure-tables.md) för mer information. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Indexattribut

Den **dataimport** guiden genererar ett index som fylls med dokument från indata-källa. 

Kontrollera att du har följande element som definierats för ett funktionella index.

1. Ett fält måste vara markerad som en **nyckel**, som används för att identifiera varje dokument. Den **nyckel** måste vara *Edm.string*. 

   Om fältvärdena innehåller mellanslag eller tankstreck, måste du ange den **Base64-koda nyckeln** alternativet i den **skapa en indexerare** steg under **avancerade alternativ**, för att utelämna den verifieringskontroll dessa tecken.

1. Ange indexattribut för varje fält. Ditt index är i stort sett innehålla något annat än nyckelfältet krävs om du väljer inga attribut. Välj en eller flera av dessa attribut för varje fält som ett minimum.
   
   + **Hämtningsbar** returnerar fältet i sökresultatet. Varje fält som anger innehåll till sökresultat måste ha det här attributet. Ange det här fältet påverkas inte Indexstorlek avsevärt.
   + **Filtrerbar** gör att fältet kan refereras i filteruttryck. Varje fält som används i en **$filter** uttrycket måste ha det här attributet. Filteruttryck är för exakta matchningar. Eftersom textsträngar förblir intakta, krävs ytterligare lagringsutrymme för ordagrant innehållet.
   + **Sökbara** möjliggör fulltextsökning. Varje fält som används i fri form frågor eller frågeuttryck måste ha det här attributet. Vägar i inverterad index skapas för varje fält som du definierar som **sökbar**.

1. Du kan också ange dessa attribut:

   + **Sorterbar** gör att fältet kan användas i en sortering. Varje fält som används i en **$Orderby** uttrycket måste ha det här attributet.
   + **Fasettbar** gör att fältet för fasetterad navigering. Endast fält också märkta **Filtrerbart** kan markeras som **Fasettbar**.

1. Ange en **Analyzer** om du vill att språk utökad indexering och frågor. Standardvärdet är *Standard Lucene* men du kan välja *Microsoft English* om du vill använda Microsofts analyzer för avancerade lexikal bearbetning, som att hantera oregelbunden substantiv och verb formulär.

   + Välj **sökbar** att aktivera den **Analyzer** lista.
   + Välj en analyzer som anges i listan. 
   
   Det går endast att ange språkanalysverktyg för närvarande. Om du använder ett anpassat analysverktyg eller en icke-språkanalys som t.ex. nyckelord, mönster och så vidare, kommer kod att krävas. Läs mer om analysverktyg, [skapa ett index för dokument på flera språk](search-language-support.md).

1. Välj den **förslagsställare** kryssrutan för att aktivera frågeifyllningsförslag frågeförslag på markerade fält.


## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om indexerare:

* [Indexera Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexera Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexera Table Storage](search-howto-indexing-azure-tables.md)