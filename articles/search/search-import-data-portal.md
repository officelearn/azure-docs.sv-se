---
title: Importera data till search-index med hjälp av Azure portal – Azure Search
description: Lär dig hur du använder guiden Importera Data i Azure-portalen för att uppdatera Azure data från Cosmos DB, Blob storage, table storage, SQL Database och SQL Server på virtuella Azure-datorer.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ceca9b8e89a963cd9a9226be143d24ed5429747b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316482"
---
# <a name="how-to-import-data-into-azure-search-index-using-the-azure-portal"></a>Hur du importerar data till Azure Search-index med hjälp av Azure portal

Med guiden **Importera data** i instrumentpanelen för Azure Search på Azure Portal kan du läsa in data i ett index. 

  ![Importera data i kommandofältet][1]

Internt konfigurerar och aktiverar guiden en *indexerare*, vilket automatiserar flera steg i indexeringsprocessen: 

* Anslut till en extern datakälla i samma Azure-prenumeration
* Generera ett ändringsbart indexschema baserat på källdatastrukturen
* Läs in JSON-dokument i ett index med en raduppsättning som hämtats från datakällan

> [!NOTE]
> Du kan starta guiden **Importera data** från Azure Cosmos DB-instrumentpanelen för att förenkla indexeringen för datakällan. Välj **Samlingar** > **Lägg till Azure Search** i navigeringsfältet till vänster för att komma igång.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Datakällor som stöds av guiden Importera data
Guiden Importera Data stöder följande datakällor: 

* Azure SQL Database
* SQL Server-relationsdata på en virtuell Azure-dator
* Azure Cosmos DB
* Azure Blob Storage
* Azure Table Storage

En utjämnad datauppsättning är en obligatorisk inmatning. Du kan bara importera från en enskild tabell, databasvy eller likvärdig datastruktur. Innan du kör guiden bör du skapa den här datastrukturen.

## <a name="connect-to-your-data"></a>Ansluta till data
1. Logga in på [Azure Portal](https://portal.azure.com) och öppna instrumentpanelen för tjänsten. Du kan klicka på **Alla tjänster** i indexet om du vill söka efter befintliga ”söktjänster” i den nuvarande prenumerationen. 

1. Klicka på **Importera data** i kommandofältet för att öppna bladet Importera data.

1. Klicka på **Anslut till dina data** för att ange en definition för datakällan som används av en indexerare. Vid datakällor inom prenumerationen kan guiden vanligtvis identifiera och läsa anslutningsinformationen, vilket minimerar de allmänna konfigurationskraven.

|  |  |
| --- | --- |
| **Befintlig datakälla** |Om du redan har definierat indexerare i söktjänsten, kan du välja en definition av en befintlig datakälla för en annan import. |
| **Azure SQL Database** |Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **SQL Server på virtuella Azure-datorer** |Ange ett fullständigt tjänstnamn, användar-ID och lösenord, samt databasen som en anslutningssträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. Instruktioner finns i [SQL VM-anslutning till Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver. |
| **Azure Cosmos DB** |Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga för att utjämna eller filtrera raduppsättningen, eller identifiera ändrade dokument för efterföljande datauppdateringar. |
| **Azure Blob Storage** |Kraven innefattar lagringskonto och en container. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under containern. Se [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md) för mer information. |
| **Azure Table Storage** |Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage](search-howto-indexing-azure-tables.md) för mer information. |

## <a name="customize-target-index"></a>Anpassa målindex
Ett preliminärt index är vanligtvis härlett från datauppsättningen. Lägga till, redigera eller ta bort fält för att slutföra schemat. Du kan dessutom ange attributen på fältnivå för att fastställa dess efterföljande sökbeteenden.

1. I **Anpassa målindex** anger du namnet och en **nyckel** som ska användas för att identifiera varje dokument. Nyckeln måste vara en sträng. Om fältvärdena innehåller mellanslag eller tankstreck måste du ange avancerade alternativ i **Importera dina data** för att utelämna valideringskontrollen av dessa tecken.

1. Granska och ändra återstående fält. Fältnamnet och typen fylls vanligtvis i automatiskt. Du kan ändra datatypen fram till dess att indexet har skapats. Om du ändrar datatypen efter detta måste indexet återskapas.

1. Ange indexattribut för varje fält:
   
   * Hämtningsbar returnerar fältet i sökresultatet.
   * Filtrerbar gör att du kan referera till fältet i filteruttryck.
   * Sorterbar gör att fältet kan användas i en sortering.
   * Fasettbar gör att fältet kan användas för fasetterad navigering.
   * Sökbar gör att du kan använda fulltextsökning.

1. Klicka på fliken **Analyzer** om du vill ange ett språkanalysverktyg på fältnivå. Det går endast att ange språkanalysverktyg för närvarande. Om du använder ett anpassat analysverktyg eller en icke-språkanalys som t.ex. nyckelord, mönster och så vidare, kommer kod att krävas.
   
   * Klicka på **Sökbara** för att använda fulltextsökning i fältet och aktivera listrutan Analyzer.
   * Välj det analysverktyg som du vill använda. Mer information finns i [Skapa ett index för dokument på flera språk](search-language-support.md).

1. Klicka på **Förslagsställare** för att aktivera frågeifyllningsförslag i markerade fält.

## <a name="import-your-data"></a>Importera dina data
1. I **Importera dina data** anger du ett namn på indexeraren. Kom ihåg att produkten från guiden Importera data är en indexerare. Om du senare vill visa eller redigera den måste du välja den från portalen i stället för att köra guiden igen. 

1. Ange det schema som är baserat på den nationella tidszonen där tjänsten har etablerats.

1. Ställ in avancerade alternativ för att ange tröskelvärden där indexeringen kan fortsätta om ett dokument tas bort. Dessutom kan du ange om fältet **Nyckel** får innehålla blanksteg och snedstreck.  

1. Klicka på **OK** för att skapa indexet och importera data.

Du kan övervaka indexeringen i portalen. Under tiden dokumenten läses in ökar antalet dokument för det index som du har definierat. Ibland kan det ta några minuter för portalsidan att hämta de senaste uppdateringarna.

Så snart alla dokument har lästs in kan du köra frågor mot indexet.

## <a name="query-an-index-using-search-explorer"></a>Fråga ett index med Sökutforskaren

Portalen innehåller **Sökutforskaren** så att du kan fråga ett index utan att behöva skriva någon kod. Du kan använda [Sökutforskaren](search-explorer.md) för valfritt index.

Sökupplevelse baseras på standardinställningar, t.ex. [enkel syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) och [searchMode-frågeparametern](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Resultaten returneras i JSON (i ett utförligt format) så att du kan gå igenom hela dokumentet.

## <a name="edit-an-existing-indexer"></a>Redigera en befintlig indexerare
Guiden Importera data skapar som sagt en **indexerare**, som du kan ändra som en fristående konstruktion i portalen.

Dubbelklicka på panelen Indexerare på instrumentpanelen för tjänsten för att visa en lista med alla indexerare som skapats för din prenumeration. Dubbelklicka på en av indexerarna för att köra, redigera eller ta bort den. Du kan ersätta indexet med ett annat befintligt index, ändra datakällan och ange alternativ för tröskelvärden för fel vid indexering.

## <a name="edit-an-existing-index"></a>Redigera ett befintligt index
Guiden skapade även ett **index**. Om det görs strukturella uppdateringar av ett index måste indexet återskapas i Azure Search. Vid återskapandet tas indexet bort och skapas på nytt med ett reviderat schema som har de ändringar du vill ha, och data läses in på nytt. Strukturella uppdateringar innebär att man ändrar datatypen och byter namn på eller tar bort ett fält.

Ändringar som inte kräver att indexet återskapas omfattar att lägga till ett nytt fält, ändra bedömningsprofil, ändra förslagsställare eller ändra språkanalys. Mer information finns i [Uppdatera index](https://msdn.microsoft.com/library/azure/dn800964.aspx).


## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om indexerare:

* [Indexera Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexera Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexera Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexera Table Storage](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

