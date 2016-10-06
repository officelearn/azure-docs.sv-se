<properties
    pageTitle="Importera data till Azure Search med hjälp av indexerare på Azure Portal | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Använd Azure Search i guiden Importera data i Azure Portal för att uppdatera data från Azure Blob Storage, Table Storage, SQL Database och SQL Server på virtuella Azure-datorer."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>


# Importera data till Azure Search med hjälp av portalen

Azure-portalen innehåller guiden **Importera data** i instrumentpanelen för Azure Search där du kan läsa in data i ett index. 

  ![Importera data i kommandofältet][1]

Internt konfigurerar och aktiverar guiden en *indexerare*, vilket automatiserar flera steg i indexeringsprocessen: 

- Ansluta till en extern datakälla i den aktuella Azure-prenumerationen
- Generera ett indexschema automatiskt baserat på källdatastrukturen
- Skapa dokument som baseras på en raduppsättning som hämtats från datakällan
- Överföra dokument till indexet i din söktjänst

Du kan testa det här arbetsflödet med exempeldata i DocumentDB. Om du behöver anvisningar läser du [Komma igång med Azure Search i Azure Portal](search-get-started-portal.md).

## Datakällor som stöds av guiden Importera data

Guiden Importera Data stöder följande datakällor: 

- Azure SQL Database
- SQL Server-relationsdata på en virtuell Azure-dator
- Azure DocumentDB
- Azure Blob Storage (förhandsversion)
- Azure Table Storage (förhandsversion)

En utjämnad datauppsättning är en obligatorisk inmatning. Du kan bara importera från en enskild tabell, databasvy eller likvärdig datastruktur. Innan du kör guiden bör du skapa den här datastrukturen.

Observera att några indexerare fortfarande är i förhandsgranskningsläge, vilket innebär att indexerarens definition säkerhetskopieras av förhandsversionen av API:n. Se [Översikt över indexerare](search-indexer-overview.md) för mer information och länkar.

## Ansluta till data

1. Logga in på [Azure Portal](https://portal.azure.com) och öppna instrumentpanelen. Du kan klicka på **Sök tjänster** i index för att visa befintliga tjänster i nuvarande prenumeration. 

2. Klicka på **Importera data** i kommandofältet för att öppna bladet Importera data.  

3. Klicka på **Anslut till dina data** för att ange en definition för datakällan som används av en indexerare. Vid datakällor inom prenumerationen kan guiden vanligtvis identifiera och läsa anslutningsinformationen, vilket minimerar de allmänna konfigurationskraven.

| | |
|--------|------------|
|**Befintlig datakälla** | Om du redan har definierat indexerare i söktjänsten, kan du välja en definition av en befintlig datakälla för en annan import.|
|**Azure SQL Database** | Tjänstens namn, autentiseringsuppgifterna för en databasanvändare med läsbehörighet och ett databasnamn kan anges på sidan eller via en ADO.NET-anslutningssträng. Välj alternativet för anslutningssträngar till att visa eller anpassa egenskaperna. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver.|
|**SQL Server på virtuella Azure-datorer** | Ange ett fullständigt tjänstnamn, användar-ID och lösenord, samt databasen som en anslutningssträng. Om du vill använda den här datakällan måste du tidigare ha installerat ett certifikat i det lokala arkiv som krypterar anslutningen. <br/><br/>Tabellen eller vyn som visar raduppsättningen måste anges på sidan. Det här alternativet visas när anslutningen lyckats, med en listruta där du kan välja det du behöver.
|**DocumentDB** |Kraven innefattar konto, databas och samling. Alla dokument i samlingen kommer att ingå i indexet. Du kan definiera en fråga för att utjämna eller filtrera raduppsättningen, eller identifiera ändrade dokument för efterföljande datauppdateringar.|
|**Azure Blob Storage** | Kraven innefattar lagringskonto och en behållare. Om blob-namnet följer en virtuell namngivningskonvention i grupperingssyfte, kan du också ange den virtuella katalogdelen av namnet som en mapp under behållaren. Se [Indexera Blob Storage (förhandsgranskning)](search-howto-indexing-azure-blob-storage.md) för mer information. |
|**Azure Table Storage** | Kraven innefattar lagringskontot och ett tabellnamn. Du kan också ange en fråga för att hämta en delmängd av tabellerna. Se [Indexera Table Storage (förhandsgranskning)](search-howto-indexing-azure-tables.md) för mer information. |

## Anpassa målindex

Ett preliminärt index är vanligtvis härlett från datauppsättningen. Lägga till, redigera eller ta bort fält för att slutföra schemat. Du kan dessutom ange attributen på fältnivå för att fastställa dess efterföljande sökbeteenden.

1. I **Anpassa målindex** anger du namnet och en **nyckel** som ska användas för att identifiera varje dokument. Nyckeln måste vara en sträng. Om fältvärdena innehåller mellanslag eller tankstreck måste du ange avancerade alternativ i **Importera dina data** för att utelämna valideringskontrollen av dessa tecken.

2. Granska och ändra återstående fält. Fältnamnet och typen fylls vanligtvis i automatiskt. Du kan ändra datatypen.

3. Ange indexattribut för varje fält:

 - Hämtningsbar returnerar fältet i sökresultatet.
 - Filtrerbar gör att du kan referera till fältet i filteruttryck.
 - Sorterbar gör att fältet kan användas i en sortering.
 - Fasettbar gör att fältet kan användas för fasetterad navigering.
 - Sökbar gör att du kan använda fulltextsökning.
  
4. Klicka på fliken **Analyzer** om du vill ange ett språkanalysverktyg på fältnivå. Det går endast att ange språkanalysverktyg för närvarande. Om du använder ett anpassat analysverktyg eller en icke-språkanalys som t.ex. nyckelord, mönster och så vidare, kommer kod att krävas.

 - Klicka på **Sökbara** för att använda fulltextsökning i fältet och aktivera listrutan Analyzer.
 - Välj det analysverktyg som du vill använda. Mer information finns i [Skapa ett index för dokument på flera språk](search-language-support.md).

5. Klicka på **Förslagsställare** för att aktivera frågeifyllningsförslag i markerade fält.


## Importera dina data

1. I **Importera dina data** anger du ett namn på indexeraren. Kom ihåg att produkten från guiden Importera data är en indexerare. Om du senare vill visa eller redigera den måste du välja den från portalen i stället för att köra guiden igen. 

2. Ange det schema som är baserat på den nationella tidszonen där tjänsten har etablerats.

3. Ställ in avancerade alternativ för att ange tröskelvärden där indexeringen kan fortsätta om ett dokument tas bort. Dessutom kan du ange om fältet **Nyckel** får innehålla blanksteg och snedstreck.  

## Redigera en befintlig indexerare

Dubbelklicka på panelen Indexerare på instrumentpanelen för tjänsten för att visa en lista med alla indexerare som skapats för din prenumeration. Dubbelklicka på en av indexerarna för att köra, redigera eller ta bort den. Du kan ersätta indexet med ett annat befintligt index, ändra datakällan och ange alternativ för tröskelvärden för fel vid indexering.

## Redigera ett befintligt index

I Azure Search kräver strukturella uppdateringar av ett index att det aktuella indexet återskapas, vilket innebär att man tar bort indexet, återskapar indexet och läser in data igen. Strukturella uppdateringar innebär att man ändrar datatypen och byter namn på eller tar bort ett fält.

Ändringar som inte kräver att indexet återskapas omfattar att lägga till ett nytt fält, ändra bedömningsprofil, ändra förslagsställare eller ändra språkanalys. Mer information finns i [Uppdatera index](https://msdn.microsoft.com/library/azure/dn800964.aspx).

## Nästa steg

Använd dessa länkar om du vill veta mer om indexerare:

- [Indexera Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexera DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indexera Blob Storage (förhandsgranskning)](search-howto-indexing-azure-blob-storage.md)
- [Indexera Table Storage (förhandsgranskning)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png




<!--HONumber=Sep16_HO4-->


