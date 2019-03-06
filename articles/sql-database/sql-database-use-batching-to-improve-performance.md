---
title: Hur du använder batchbearbetning för att förbättra programmets prestanda för Azure SQL Database
description: Avsnittet ger bevis på att batchbearbetning databasåtgärder avsevärt förbättrar hastighet och skalbarhet för din Azure SQL Database-program. Även om dessa batchbearbetningen metoder fungera för alla SQL Server-databas, är fokus i artikeln på Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 0ef6d258be0165c7a73ce060879f55f1c7f404f9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453532"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Hur du använder batchbearbetning för att förbättra programmets prestanda för SQL-databas

Batchbearbetning åtgärder till Azure SQL Database avsevärt förbättrar prestanda och skalbarhet i dina program. För att förstå fördelarna, beskrivs den första delen av den här artikeln några exempel på testresultat som jämför sekventiella och gruppbaserade begäranden till en SQL-databas. Resten av artikeln visar de tekniker, scenarier och överväganden som hjälper dig att använda batchbearbetning har i din Azure-program.

## <a name="why-is-batching-important-for-sql-database"></a>Varför är batchbearbetning viktigt för SQL-databas

Batchbearbetning anrop till en fjärrtjänst är en välkänd strategi för att öka prestanda och skalbarhet. Det är fasta bearbetningskostnaderna på alla interaktioner med en fjärrtjänst som serialisering, överföring och deserialisering. Paketera många separata transaktioner i en enda batch minimerar kostnaderna.

I det här dokumentet, som vi vill undersöka olika SQL-databas batchbearbetning strategier och scenarier. Strategierna är också viktigt för lokala program som använder SQL Server, finns men det flera orsaker till markering användningen av batchbearbetning för SQL-databas:

* Det finns potentiellt större Nätverksfördröjningen vid åtkomst till SQL-databas, särskilt om du ansluter till SQL-databas från utanför samma Microsoft Azure-datacenter.
* SQL-databas med flera klienter egenskaper innebär att effektiviteten hos de data access layer motsvarar den övergripande skalbarheten i databasen. SQL-databas måste förhindra att någon enskild klient/användare använder alla databasresurser till skada för andra klienter. Som svar på användning som överstiger fördefinierade kvoter, SQL Database minska dataflöde eller svara med begränsning undantag. Effektivitet, till exempel batchbearbetning, kan du fortsätta att arbeta med SQL-databasen innan de når dessa gränser. 
* Batchbearbetning gäller även för arkitekturer som använder flera databaser (sharding). Effektivitet interaktionen med varje databasenhet är fortfarande en nyckelfaktor i din övergripande skalbarhet. 

En av fördelarna med att använda SQL-databas är att du inte behöver hantera servrar som värd för databasen. Den här hanterade infrastrukturen innebär dock även att du har olika tänka databasoptimering. Du kan inte längre söka för att förbättra databasinfrastruktur maskinvara eller nätverk. Microsoft Azure styr dessa miljöer. Området du kan styra är hur programmet interagerar med SQL-databas. Batchbearbetning är en av dessa optimeringar. 

Den första delen av dokumentet går igenom olika batchbearbetningen metoder för .NET-program som använder SQL-databas. De sista två avsnitten beskriver batchbearbetningen riktlinjer och scenarier.

## <a name="batching-strategies"></a>Batchbearbetning strategier

### <a name="note-about-timing-results-in-this-article"></a>Observera om tidsinställning resultaten i den här artikeln

> [!NOTE]
> Resultatet är inte prestandamått men är avsedda att visa **relativa prestandan**. Tidsinställningar baseras på ett genomsnitt av minst 10 testkörningar. Åtgärder är tillägg i en tom tabell. De här testerna har uppmätta pre-V12 och de inte nödvändigtvis motsvarar dataflödet som kan uppstå i en V12-databas med hjälp av den nya [DTU tjänstnivåerna](sql-database-service-tiers-dtu.md) eller [vCore tjänstnivåer](sql-database-service-tiers-vcore.md). Den relativa fördelen batchbearbetningen tekniken bör vara densamma.

### <a name="transactions"></a>Transaktioner

Det verkar onormalt att påbörja en granskning av batchbearbetning genom att diskutera transaktioner. Men användningen av transaktioner på klientsidan har en diskret serversidan batchbearbetningen effekt som förbättrar prestanda. Och transaktioner kan läggas till med bara några rader kod, så att de är ett snabbt sätt att förbättra prestanda för sekventiella åtgärder.

Överväg följande C#-koden som innehåller en sekvens med insert och uppdateringsåtgärder för en enkel tabell.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
Följande kod i ADO.NET utför sekventiellt dessa åtgärder.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Det bästa sättet att optimera den här koden är att implementera någon form av klientsidan batchbearbetning av dessa anrop. Men det finns ett enkelt sätt att öka prestanda för den här koden genom att helt enkelt omsluta sekvens med anrop i en transaktion. Här är samma kod som använder en transaktion.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transaktioner som faktiskt används i båda exemplen. I det första exemplet är varje enskilt anrop en implicit transaktion. I det andra exemplet radbryts alla anrop med en explicit transaktion. Per i dokumentationen för den [write-ahead transaktionsloggen](https://msdn.microsoft.com/library/ms186259.aspx), loggposter är replikatorn tömmer till disken när transaktionen genomförs. Så genom att lägga till fler anrop i en transaktion kan skrivåtgärd till transaktionsloggen fördröja tills transaktionen genomförs. Du aktiverar i praktiken batchbearbetning för skrivningar till serverns transaktionsloggen.

I följande tabell visas några ad hoc-testresultaten. Testerna utförs samma sekventiella infogningar med och utan transaktioner. För mer perspektiv kördes den första uppsättningen tester via en fjärranslutning från en bärbar dator till databasen i Microsoft Azure. Den första uppsättningen tester kördes från en tjänst i molnet och databasen att båda finns inom samma Microsoft Azure-datacenter (USA, västra). I följande tabell visar varaktighet i millisekunder för sekventiella infogningar med och utan transaktioner.

**Lokalt till Azure**:

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure-datorer (samma datacenter)**:

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).

Baserat på resultaten av föregående, minskar omsluta en enda åtgärd i en transaktion prestanda faktiskt. Men när du ökar antalet åtgärder i en enda transaktion förbättring av prestanda blir större. Prestandaskillnaden är också synligare när alla åtgärder som inträffar inom Microsoft Azure-datacenter. Ökad latens på hur du använder SQL-databas från utanför Microsoft Azure-datacentret överskuggar prestandaökning för att använda transaktioner.

Även om användning av transaktioner kan öka prestanda, fortsätter att [Se metodtips för transaktioner och anslutningar](https://msdn.microsoft.com/library/ms187484.aspx). Behåll transaktionen så korta som möjligt och stäng anslutningen till databasen när arbetet är klar. Den med hjälp av instruktionen i det förra exemplet säkerställer att anslutningen är stängd när efterföljande kodblocket har slutförts.

I föregående exempel visar att du kan lägga till en lokal transaktion till någon ADO.NET-kod med två rader. Transaktioner erbjuder ett snabbt sätt att förbättra prestandan för kod som gör sekventiella infoga, uppdatera och ta bort. Men för bästa prestanda, Överväg att ändra koden ytterligare till dra nytta av klientsidan batchbearbetning, till exempel tabellvärdeparametrar.

Läs mer om transaktioner i ADO.NET [lokala transaktioner i ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>tabellvärdeparametrar

Tabellvärdeparametrar stöder användardefinierade tabelltyper som parametrar i Transact-SQL-uttryck, lagrade procedurer och funktioner. Den här batchbearbetningen tekniken för klientsidan kan du skicka flera rader med data i parametern-tabellvärdesfunktion. Om du vill använda tabellvärdeparametrar måste du först definiera en tabelltyp. Följande Transact-SQL-uttrycket skapar en tabelltyp med namnet **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

I koden, skapar du en **DataTable** med exakt samma namn och typer av tabelltypen. Skicka det **DataTable** i en parameter i en textfråga eller lagrad procedur anropa. I följande exempel visas den här tekniken:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

I exemplet ovan den **SqlCommand** objekt infogar rader från en tabellvärdesparameter **@TestTvp**. Den tidigare skapade **DataTable** objektet har tilldelats den här parametern med det **SqlCommand.Parameters.Add** metod. Batchbearbetning infogningar i ett anrop avsevärt ökar prestanda över sekventiella infogningar.

Använda en lagrad procedur i stället för ett textbaserat kommando för att förbättra det föregående exemplet ytterligare. Följande Transact-SQL-kommando skapar en lagrad procedur som tar den **SimpleTestTableType** tabellvärdesparametern.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Ändra den **SqlCommand** objekt deklarationen i det förra exemplet nedan.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

I de flesta fall har tabellvärdeparametrar motsvarande eller bättre prestanda än andra batchbearbetningen metoder. Tabellvärdeparametrar är ofta föredra, eftersom de är mer flexibel än andra alternativ. Andra metoder, till exempel SQL-masskopiering, till exempel tillåta endast nya rader infogas. Men med tabellvärdeparametrar, du kan använda logik i den lagrade proceduren för att avgöra vilka rader som uppdateringar och som är infogar. Tabelltypen kan också ändras så att den innehåller en ”åtgärden”-kolumn som anger om den angivna raden ska infogas, uppdateras eller tas bort.

I följande tabell visar ad hoc-testresultaten för användning av tabellvärdeparametrar i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Samma Azure-datacenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Prestandaökning från batchbearbetning är omedelbart synliga. I det föregående sekventiella testet tog 1000 åtgärderna 129 sekunder utanför datacentret och 21 sekunder från inom datacentret. Men med tabellvärdeparametrar, 1000 åtgärder Ta endast 2,6 sekunder utanför datacentret och 0,4 sekunder inom datacentret.

Mer information om tabellvärdeparametrar finns [Table-Valued parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL-masskopiering

SQL-masskopiering är ett annat sätt att infoga stora mängder data i en måldatabas. .NET-program kan använda den **SqlBulkCopy körs** klass som utför infogningsåtgärder. **SqlBulkCopy körs** liknar i funktionen kommandoradsverktyget **Bcp.exe**, eller Transact-SQL-instruktionen **BULK INSERT**. I följande kodexempel visar hur du masskopiera raderna i källan **DataTable**, tabell MyTable till måltabell i SQL Server.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Det finns tillfällen där masskopiering vägen prioriteras framför tabellvärdeparametrar. Se jämförelsetabellen av tabellvärdeparametrar jämfört med BULK INSERT-åtgärder i artikeln [Table-Valued parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

Följande ad hoc-testresultaten visar prestanda för batchbearbetning med **SqlBulkCopy körs** i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Samma Azure-datacenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

I batch är mindre, Använd tabellvärdeparametrar – bättre än den **SqlBulkCopy körs** klass. Dock **SqlBulkCopy körs** utförs 12-31% snabbare än tabellvärdeparametrar för testerna av 1 000 och 10 000 rader. Som tabellvärdeparametrar, **SqlBulkCopy körs** är ett bra alternativ för satsvis infogningar, särskilt när jämfört med prestanda för icke-batchar åtgärder.

Läs mer på masskopiering i ADO.NET [Masskopieringsåtgärder i SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Flera rader som innehåller parametrar Infoga uttryck

Ett alternativ för små batchar är att skapa en stor parametriserade INSERT-instruktionen som infogar flera rader. I följande kodexempel visar den här tekniken.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Det här exemplet är avsedd att visa det grundläggande konceptet. Ett mer realistiskt scenario skulle gå igenom entiteterna som krävs för att konstruera frågesträngen och parametrarna samtidigt. Du är begränsad till totalt 2100 frågeparametrar, så detta begränsar det totala antalet rader som kan bearbetas i det här sättet.

Följande ad hoc-testresultaten visar prestanda för den här typen av insert-instruktionen i millisekunder.

| Åtgärder | Tabellvärdeparametrar (ms) | Single-statement INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Den här metoden kan vara lite snabbare för batchar som är mindre än 100 rader. Även om förbättring är liten, är den här tekniken ett annat alternativ som fungerar bra i ditt scenario för specifika program.

### <a name="dataadapter"></a>DataAdapter

Den **DataAdapter** klassen kan du ändra en **datauppsättning** objektet och sedan skicka ändringarna som infoga, uppdatera och ta bort åtgärder. Om du använder den **DataAdapter** i det här sättet är det viktigt att Observera att anrop görs för varje distinkta operationen. Förbättra prestanda genom att använda den **UpdateBatchSize** egenskap till antalet åtgärder som ska kunna batchbearbetas i taget. Mer information finns i [utför Batch åtgärder med hjälp av DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entity framework

Entity Framework stöder för närvarande inte batchbearbetning. Olika utvecklare i communityn har försökt att visa lösningar, till exempel åsidosättning den **SaveChanges** metod. Men lösningarna som vanligtvis är komplicerade och anpassade program och datamodellen. Entity Framework codeplex projektet har för närvarande en diskussionssida på den här funktionsbegäran. Den här diskussionen finns [Design mötesanteckningar - den 2 augusti 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

För fullständighetens skull anser vi att det är viktigt att tala om XML-Datatypen som en batchbearbetningen strategi. Användning av XML har dock inga fördelar jämfört med andra metoder och flera nackdelar. Metoden som liknar tabellvärdeparametrar, men en XML-fil eller en sträng som skickas till en lagrad procedur i stället för en användardefinierad tabell. Den lagrade proceduren tolkar kommandona i den lagrade proceduren.

Det finns flera nackdelar med denna metod:

* Arbeta med XML kan vara krånglig och felbenägna.
* Tolkning av XML för databasen kan vara processorintensiva.
* Den här metoden är långsammare än tabellvärdeparametrar i de flesta fall.

Därmed behöver rekommenderas inte användning av XML för batch-frågor.

## <a name="batching-considerations"></a>Batchbearbetning överväganden

Följande avsnitt innehåller mer vägledning för användning av batchbearbetning i SQL Database-program.

### <a name="tradeoffs"></a>Kompromisser

Beroende på din arkitektur involverar batchbearbetning en kompromiss mellan prestanda och återhämtning. Tänk dig ett scenario där din roll oväntat stängs av. Om du förlorar en datarad för är effekten mindre än effekten av att förlora en stor grupp med rader som inte har skickats. Det finns en större risk när du buffrar rader innan de skickas till databasen i ett angivet tidsintervall.

Utvärdera vilken typ av åtgärder som du batch på grund av den här kompromiss. Batch-mer aggressivt (större batchar och längre tidsfönster) med data som är mindre viktigt.

### <a name="batch-size"></a>Batchstorlek

I våra tester kan uppstod vanligtvis ingen fördel med att dela upp stora batchar i mindre segment. I själva verket lett detta delfält ofta till långsammare än skickar en enda stor grupp. Tänk dig ett scenario där du vill infoga 1000 rader. I följande tabell visar hur lång tid det tar för att använda tabellvärdeparametrar för att infoga 1000 rader när uppdelad i mindre Batcher.

| Batchstorlek | Iterationer | Tabellvärdeparametrar (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Du kan se att bästa möjliga prestanda för 1000 rader är att skicka dem på samma gång. I andra testerna (visas inte här) uppstod prestandafördelar små att dela en 10000 rad batch i två grupper med 5000. Men tabellschemat för dessa tester är relativt enkelt, så du bör utföra tester på dina specifika data eller batch storlekar att verifiera dessa resultat.

En annan faktor är att om den totala batchen blir för stor, kan SQL Database begränsa och vägra att bekräfta batchen. Testa ditt specifika scenario för att avgöra om det finns en perfekt batchstorlek för bästa resultat. Kontrollera batchstorleken kan konfigureras vid körning för att aktivera snabb anpassning av baserat på prestanda eller fel.

Slutligen belastningsutjämna batchens storlek med risker med batchbearbetning. Om det finns tillfälliga fel eller rollen misslyckas kan du överväga konsekvenserna av gör om åtgärden eller för att förlora data i batchen.

### <a name="parallel-processing"></a>Parallell bearbetning

Vad händer om du tog metod för att minska batchstorleken men används flera trådar för att utföra arbetet? Våra tester visade igen, att flera mindre flertrådat batchar normalt utförs sämre resultat än en enda större batch. Följande test försöker infoga 1000 rader i en eller flera parallella batchar. Det här testet visas hur flera samtidiga batchar minskar faktiskt prestanda.

| Batchstorlek [iterationer] | Två trådar (ms) | Fyra trådar (ms) | Sex trådar (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultatet är inte prestandamått. Se den [om tidsinställning resultaten i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Det finns flera möjliga orsaker till försämrade prestanda på grund av parallellitet:

* Det finns flera samtidiga nätverksanrop istället för en.
* Flera åtgärder mot en enskild tabell kan resultera i konkurrensen och blockerar.
* Det finns kostnader som flertrådsteknik.
* Kostnaden för att öppna flera anslutningar uppväger fördelen med parallell bearbetning.

Om du anpassar olika tabeller eller databaser, är det möjligt att se vissa prestanda få med den här strategin. Database sharding eller federationer är ett scenario för den här metoden. Horisontell partitionering använder flera databaser och dirigerar olika data till varje databas. Om varje små batch ska till en annan databas, kan det vara mer effektivt att utföra åtgärderna parallellt. Prestanda är inte tillräckligt stor för att använda som grund för ett beslut för att använda databasen horisontell partitionering i din lösning.

I vissa utformningar kan parallell körning av mindre batchar leda till bättre genomströmning på begäranden i ett system under belastning. I det här fallet, även om det går snabbare att bearbeta en enskild större batch kan bearbetar flera batchar parallellt vara mer effektivt.

Om du använder parallell körning kan du överväga att kontrollera det maximala antalet arbetstrådar. Ett mindre antal kan leda till mindre konkurrens och en snabbare körningstid. Överväg även den belastning som placeras i måldatabasen både i anslutningar och transaktioner.

### <a name="related-performance-factors"></a>Prestandadata relaterade faktorer

Vanliga vägledning för databasprestanda påverkar också batchbearbetning. Infoga exempelvis prestanda minskar för tabeller som har en primärnyckel för stora eller många icke-grupperat index.

Om tabellvärdeparametrar använder en lagrad procedur kan du använda kommandot **SET NOCOUNT ON** i början av proceduren. Den här instruktionen Undertrycker avkastningen på antalet berörda rader i proceduren. Men i våra tester kan användningen av **SET NOCOUNT ON** antingen inte påverkar eller minskar prestanda. Testa lagrade proceduren har enkelt med en enda **infoga** från parametern-tabellvärdesfunktion. Det är möjligt att mer komplexa lagrade procedurer skulle ha nytta av den här instruktionen. Men inte utgår från att lägga till **SET NOCOUNT ON** till den lagrade proceduren automatiskt förbättrar prestanda. Testa den lagrade proceduren med och utan för att förstå effekten på **SET NOCOUNT ON** instruktionen.

## <a name="batching-scenarios"></a>Batchbearbetning scenarier

I följande avsnitt beskrivs hur du använder tabellvärdeparametrar i tre Programscenarier. Det första scenariot visar hur buffring och batchbearbetning kan fungera tillsammans. I det andra scenariot förbättrar prestanda genom att utföra åtgärder för master-detaljer i en enda lagrade proceduranropet. Sista scenariot visar hur du använder tabellvärdeparametrar i en ”UPSERT”-åtgärd.

### <a name="buffering"></a>Buffring

Det finns vissa scenarier som är självklara kandidater för batchbearbetning, finns men det många scenarier som kan dra nytta av batchbearbetning av fördröjda bearbetning. Försenade bearbetning innebär dock även en större risk att data förloras i händelse av ett oväntat fel. Det är viktigt att förstå den här risken och överväga konsekvenserna.

Tänk dig ett webbprogram som spårar navigeringhistorik för varje användare. Programmet kan göra en databasanropet spela in användarens Sidvisning på varje sida i begäran. Men högre prestanda och skalbarhet kan uppnås genom buffring användarnas navigering aktiviteter och sedan skicka dessa data till databasen i batchar. Du kan utlösa databasuppdatering av förfluten tid och/eller buffertstorlek. En regel kan till exempel ange att batchen ska bearbetas efter 20 sekunder eller när bufferten når 1 000 objekt.

Följande kodexempel används [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) att bearbeta buffrade händelser som skapats av en övervakning klass. När bufferten blir fullt eller en tidsgräns uppnås, i gruppen med användardata skickas till databasen med en tabellvärdesparameter.

Följande NavHistoryData klass modeller användarinformation för navigering. Den innehåller grundläggande information, till exempel användar-ID och den URL som används vid åtkomst.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Klassen NavHistoryDataMonitor ansvarar för buffring navigering användardata i databasen. Den innehåller en metod, RecordUserNavigationEntry som svarar genom att höja ett **OnAdded** händelse. Följande kod visar konstruktor logiken som använder Rx för att skapa en synliga samling baserat på händelsen. Den sedan prenumererar på den här synliga samlingen med metoden buffert. Överlagringen anger att bufferten ska skickas var 20: e sekund eller 1000 poster.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Hanteraren konverterar alla buffrade objekt till en tabellvärderade typ och skickar den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar slutförd definitionen för både NavHistoryDataEventArgs och NavHistoryDataMonitor klasser.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Hanteraren konverterar alla buffrade objekt till en tabellvärderade typ och skickar den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar slutförd definitionen för både NavHistoryDataEventArgs och NavHistoryDataMonitor klasser.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Om du vill använda den här buffring klassen skapar programmet ett statiskt NavHistoryDataMonitor-objekt. Varje gång en användare ansluter till en sida, anropar programmet metoden NavHistoryDataMonitor.RecordUserNavigationEntry. Buffring logiken fortsätter att ta hand om skicka dessa poster till databasen i batchar.

### <a name="master-detail"></a>Master information

Tabellvärdeparametrar är användbara för enkel INSERT-scenarier. Det kan dock vara mer utmanande att batch-infogningar som innefattar mer än en tabell. ”Översikt/detaljer”-scenario är ett bra exempel. Huvudtabellen identifierar den primära entiteten. En eller flera tabeller i detalj lagra mer data om enheten. I det här scenariot genomdriva sekundärnyckelrelationer förhållandet mellan information till en unik master-entitet. Överväg en förenklad version av PurchaseOrder tabellerna och dess associerade OrderDetail. Följande Transact-SQL skapar tabellen PurchaseOrder med fyra kolumner: OrderID, OrderDate, CustomerID och Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Varje beställning innehåller en eller flera produktinköp. Den här informationen samlas i tabellen PurchaseOrderDetail. Följande Transact-SQL skapar tabellen PurchaseOrderDetail med fem kolumner: OrderID, OrderDetailID, ProductID, Enhetspris och OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

Kolumnen OrderID i tabellen PurchaseOrderDetail måste hänvisa till en order från tabellen PurchaseOrder. Följande definition för en sekundärnyckel framtvingar den här begränsningen.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Du måste ha en användardefinierad tabelltyp för varje måltabell som för att kunna använda tabellvärdeparametrar.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Definiera en lagrad procedur som sekvenstabeller av de här typerna. Den här proceduren kan ett program till lokalt batch en uppsättning beställningar och beställningsinformation i ett enda anrop. Följande Transact-SQL ger fullständig lagrad procedur-deklarationen för det här exemplet för köp ordning.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

I det här exemplet lokalt definierade @IdentityLink tabellen lagras de faktiska värdena OrderID från de nyligen infogade raderna. Dessa order-ID: n skiljer sig från tillfälliga OrderID värdena i den @orders och @details tabellvärdeparametrar. Därför måste den @IdentityLink tabell ansluter sedan OrderID värden från den @orders parametern till de verkliga OrderID-värden för de nya raderna i tabellen PurchaseOrder. Efter det här steget i @IdentityLink tabell kan underlätta infogar orderinformationen med den faktiska OrderID som uppfyller sekundärnyckelbegränsningen.

Den här lagrade proceduren kan användas från kod eller från andra Transact-SQL-anrop. Se avsnittet tabellvärdeparametrar i det här dokumentet som ett exempel. Följande Transact-SQL visar hur du anropar sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Den här lösningen kan varje batch för att använda en värdeuppsättning OrderID som börjar vid 1. Värdena för tillfälliga OrderID beskriver relationer i batchen, men de faktiska värdena för OrderID bestäms vid tidpunkten för insert-åtgärden. Du kan köra samma uttryck i föregående exempel upprepade gånger och generera unika order i databasen. Överväg att lägga till mer kod eller databasen logik som förhindrar att duplicerade order när du använder detta batchbearbetning tekniken därför.

Det här exemplet visar att ännu mer komplexa databasåtgärderna, till exempel översikt detaljer åtgärder, kan batchhanteras med tabellvärdeparametrar.

### <a name="upsert"></a>UPSERT

En annan batchbearbetningen scenariet inbegriper att uppdatera befintliga rader och infoga nya rader samtidigt. Den här åtgärden kallas ibland för en ”UPSERT” (uppdatering + insert)-åtgärd. I stället för att göra separata anrop till INFOGNINGS- och fungerar MERGE-instruktion bäst för den här uppgiften. MERGE-instruktion kan utföra både insert och uppdateringsåtgärder i ett enda anrop.

Tabellvärdeparametrar kan användas med MERGE-instruktion för att utföra uppdateringar och infogningar. Anta exempelvis att en förenklad anställd-tabell som innehåller följande kolumner: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

I det här exemplet kan du använda det faktum att SocialSecurityNumber är unika för utför en SAMMANSLAGNING av flera anställda. Börja med att skapa en användardefinierad tabelltyp:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Därefter skapa en lagrad procedur eller skriva kod som använder MERGE-instruktion för att utföra uppdateringen och infoga. I följande exempel används MERGE-instruktion på en tabellvärdesparameter @employees, av typen EmployeeTableType. Innehållet i den @employees tabellen visas inte här.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Mer information finns i dokumentation och exempel för MERGE-instruktion. Även om samma arbetet kan utföras i en flera steg lagrade proceduranrop med separata INSERT och uppdateringsåtgärder, MERGE-instruktionen är mer effektivt. Databaskod kan också skapa Transact-SQL-anrop som MERGE-instruktion direkt utan att kräva två databasanrop för INSERT och UPDATE.

## <a name="recommendation-summary"></a>Sammanfattning av rekommendation

Följande lista innehåller en sammanfattning av batchbearbetningen rekommendationerna som beskrivs i den här artikeln:

* Använda buffring och batchbearbetning för att öka prestanda och skalbarhet för SQL Database-program.
* Förstå kompromisser batchbearbetning/buffring och återhämtningskapaciteten. Vid fel roll, kan risk att förlora en obearbetade batch med verksamhetskritiska data uppväga prestandafördelarna batchbearbetning.
* Försök att hålla alla anrop till databasen i ett och samma datacenter som minskar svarstiderna.
* Om du väljer en enda batchbearbetningen teknik, erbjuder tabellvärdeparametrar bästa prestanda och flexibilitet.
* Följ dessa riktlinjer för insert snabbaste prestanda, men testa ditt scenario:
  * Använd ett enda kommando med parametrar INSERT för < 100 rader.
  * Använda tabellvärdeparametrar för < 1000 rader.
  * För > = 1000 rader, använda SqlBulkCopy körs.
* För uppdatera och ta bort åtgärder kan använda tabellvärdeparametrar med lagrade proceduren logik som anger rätt igen på varje rad i tabellen-parametern.
* Riktlinjer för batch-storlek:
  * Använd de största batch-storlekar som passar ditt program och dina affärsbehov.
  * Balansera prestandaökning för stora batchar med risk för tillfälliga eller oåterkalleligt fel. Vad är en följd av återförsök eller förlust av data i batchen? 
  * Testa den största batchstorleken som för att verifiera att SQL-databas inte avvisar den.
  * Skapa inställningar för den kontrollen batchbearbetning, till exempel batchstorleken eller buffring tidsfönstret. De här inställningarna ger flexibilitet. Du kan ändra beteendet för batchbearbetningen i produktion utan att omdistribuera Molntjänsten.
* Undvik att parallell körning av batchar som fungerar på en enskild tabell i en databas. Om du väljer att dela en enskild batch över flera trådar kan köra tester för att avgöra perfekt antalet trådar. När du har ett okänt tröskelvärde fler trådar kommer försämra prestanda i stället öka den.
* Överväg att buffring på storlek och tid som ett sätt att implementera batchbearbetning för fler scenarier.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur databasdesign och kodning tekniker relaterade till batchbearbetning kan förbättra dina programprestanda och skalbarhet. Men det är bara en faktor i din övergripande strategi. Fler sätt att förbättra prestanda och skalbarhet, se [Azure SQL Database prestanda hos enskilda databaser](sql-database-performance-guidance.md) och [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool-guidance.md).

