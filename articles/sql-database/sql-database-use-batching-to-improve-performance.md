---
title: Så här använder du batchbearbetning för att förbättra programmets prestanda
description: Avsnittet innehåller bevis för att batch-databasåtgärder avsevärt förbättrar hastigheten och skalbarheten för dina Azure SQL Database-program. Även om dessa batchtekniker fungerar för alla SQL Server-databaser, är fokus för artikeln på Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545032"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Använda batchbearbetning för att förbättra SQL Database-programprestanda

Batching-åtgärder till Azure SQL Database förbättrar avsevärt prestanda och skalbarhet för dina program. För att förstå fördelarna täcker den första delen av den här artikeln några exempeltestresultat som jämför sekventiella och batchade begäranden med en SQL-databas. Resten av artikeln visar tekniker, scenarier och överväganden som hjälper dig att använda batchbearbetning framgångsrikt i dina Azure-program.

## <a name="why-is-batching-important-for-sql-database"></a>Varför är batchbearbetning viktigt för SQL Database

Batching-anrop till en fjärrtjänst är en välkänd strategi för att öka prestanda och skalbarhet. Det finns fasta bearbetningskostnader för alla interaktioner med en fjärrtjänst, till exempel serialisering, nätverksöverföring och avserialisering. Om du paketerar många separata transaktioner i en enda batch minimeras dessa kostnader.

I det här dokumentet vill vi undersöka olika SQL Database batching strategier och scenarier. Även om dessa strategier också är viktiga för lokala program som använder SQL Server, finns det flera skäl att markera användningen av batchbearbetning för SQL Database:

* Det finns potentiellt större nätverksfördröjning vid åtkomst till SQL Database, särskilt om du använder SQL Database utanför samma Microsoft Azure-datacenter.
* Sql Databases multitenantegenskaper innebär att effektiviteten i dataåtkomstlagret korrelerar till databasens övergripande skalbarhet. SQL Database måste förhindra att en enskild klient/användare monopoliserar databasresurser till nackdel för andra klienter. Som svar på användning som överstiger fördefinierade kvoter kan SQL Database minska dataflödet eller svara med begränsningsundningar. Effektivitetsvinster, till exempel batchbearbetning, gör att du kan arbeta mer med SQL Database innan du når dessa gränser. 
* Batchbearbetning är också effektivt för arkitekturer som använder flera databaser (fragmentering). Effektiviteten i din interaktion med varje databasenhet är fortfarande en viktig faktor i din totala skalbarhet. 

En av fördelarna med att använda SQL Database är att du inte behöver hantera de servrar som är värdar för databasen. Men den här hanterade infrastrukturen innebär också att du måste tänka annorlunda om databasoptimeringar. Du kan inte längre försöka förbättra databasens maskinvara eller nätverksinfrastruktur. Microsoft Azure styr dessa miljöer. Det huvudsakliga område som du kan styra är hur ditt program interagerar med SQL Database. Batching är en av dessa optimeringar. 

Den första delen av dokumentet undersöker olika batchtekniker för .NET-program som använder SQL Database. De två sista avsnitten täcker batching riktlinjer och scenarier.

## <a name="batching-strategies"></a>Batching strategier

### <a name="note-about-timing-results-in-this-article"></a>Observera om tidsresultat i den här artikeln

> [!NOTE]
> Resultaten är inte riktmärken utan är avsedda att visa **relativa resultat**. Tidsinställningarna baseras på ett genomsnitt på minst 10 provkörningar. Operationer infogas i en tom tabell. Dessa tester mättes före V12 och de motsvarar inte nödvändigtvis dataflödet som du kan uppleva i en V12-databas med hjälp av de nya [DTU-tjänstnivåerna](sql-database-service-tiers-dtu.md) eller [vCore-tjänstnivåerna](sql-database-service-tiers-vcore.md). Den relativa fördelen med batchtekniken bör vara likartad.

### <a name="transactions"></a>Transaktioner

Det verkar konstigt att börja en översyn av batching genom att diskutera transaktioner. Men användningen av transaktioner på klientsidan har en subtil batching-effekt på serversidan som förbättrar prestanda. Och transaktioner kan läggas till med endast ett fåtal rader kod, så de ger ett snabbt sätt att förbättra prestanda för sekventiella åtgärder.

Tänk på följande C#-kod som innehåller en sekvens av infognings- och uppdateringsåtgärder i en enkel tabell.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
Följande ADO.NET kod utför sekventiellt dessa åtgärder.

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

Det bästa sättet att optimera den här koden är att implementera någon form av batchbearbetning på klientsidan av dessa anrop. Men det finns ett enkelt sätt att öka prestanda för denna kod genom att helt enkelt slå in sekvensen av samtal i en transaktion. Här är samma kod som använder en transaktion.

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

Transaktioner används faktiskt i båda dessa exempel. I det första exemplet är varje enskilt anrop en implicit transaktion. I det andra exemplet radbrys alla anrop i ett explicit transaktion. Enligt dokumentationen för [transaktionsloggen](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)för skriv framåt rensas loggposter till disken när transaktionen genomförs. Så genom att inkludera fler samtal i en transaktion kan skrivningen till transaktionsloggen fördröjas tills transaktionen har genomförts. I själva verket aktiverar du batchbearbetning för skrivningar till serverns transaktionslogg.

I följande tabell visas några ad hoc-testresultat. Testerna utförde samma sekventiella skär med och utan transaktioner. För mer perspektiv kördes den första uppsättningen tester på distans från en bärbar dator till databasen i Microsoft Azure. Den andra uppsättningen tester kördes från en molntjänst och databas som båda finns i samma Microsoft Azure-datacenter (västra USA). I följande tabell visas varaktigheten i millisekunder av sekventiella infogningar med och utan transaktioner.

**Lokalt till Azure:**

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure till Azure (samma datacenter):**

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).

Baserat på tidigare testresultat minskar om du radbryter en enskild åtgärd i en transaktion prestanda. Men när du ökar antalet åtgärder inom en enskild transaktion blir prestandaförbättringen mer markant. Prestandaskillnaden är också mer märkbar när alla åtgärder sker i Microsoft Azure-datacentret. Den ökade svarstiden för att använda SQL Database utanför Microsoft Azure-datacentret överskuggar prestandavinsten för att använda transaktioner.

Även om användningen av transaktioner kan öka prestanda, fortsätta att [följa bästa praxis för transaktioner och anslutningar](https://msdn.microsoft.com/library/ms187484.aspx). Behåll transaktionen så kort som möjligt och stäng databasanslutningen när arbetet är klart. Användningssatsen i föregående exempel säkerställer att anslutningen stängs när det efterföljande kodblocket är klart.

I föregående exempel visas att du kan lägga till en lokal transaktion i valfri ADO.NET kod med två rader. Transaktioner erbjuder ett snabbt sätt att förbättra prestanda för kod som gör sekventiell infoga, uppdatera och ta bort åtgärder. Men för den snabbaste prestanda, överväga att ändra koden ytterligare för att dra nytta av klientsidan batchbearbetning, till exempel tabell-värderade parametrar.

Mer information om transaktioner i ADO.NET finns [i Lokala transaktioner i ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Tabellvärderade parametrar

Tabellvärderade parametrar stöder användardefinierade tabelltyper som parametrar i Transact-SQL-uttryck, lagrade procedurer och funktioner. Med den här batchtekniken på klientsidan kan du skicka flera rader med data i parametern tabellvärderad. Om du vill använda tabellvärdade parametrar definierar du först en tabelltyp. Följande Transact-SQL-uttryck skapar en tabelltyp med namnet **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

I kod skapar du en **DataTable** med exakt samma namn och typer av tabelltypen. Skicka den här **DataTable** i en parameter i en textfråga eller ett lagrat proceduranrop. Följande exempel visar den här tekniken:

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

I föregående exempel infogar **SqlCommand-objektet** rader från en tabellvärderad parameter, ** \@TestTvp**. Det tidigare skapade **DataTable-objektet** tilldelas den här parametern med metoden **SqlCommand.Parameters.Add.** Om du grupperar infogningen i ett anrop ökar prestandan över sekventiella skär avsevärt.

Om du vill förbättra föregående exempel ytterligare använder du en lagrad procedur i stället för ett textbaserat kommando. Följande Transact-SQL-kommando skapar en lagrad procedur som tar parametern **SimpleTestTableType-tabellvärde.**

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

Ändra sedan **sqlcommand-objektdeklarationen** i föregående kodexempel till följande.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

I de flesta fall har tabellvärdna parametrar likvärdiga eller bättre prestanda än andra batchtekniker. Tabellvärdna parametrar är ofta att föredra, eftersom de är mer flexibla än andra alternativ. Andra tekniker, till exempel SQL-masskopia, tillåter till exempel bara att nya rader infogas. Men med tabellvärdade parametrar kan du använda logik i den lagrade proceduren för att avgöra vilka rader som är uppdateringar och vilka som är infogningar. Tabelltypen kan också ändras så att den innehåller kolumnen "Operation" som anger om den angivna raden ska infogas, uppdateras eller tas bort.

I följande tabell visas ad hoc-testresultat för användning av tabellvärdade parametrar i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Azure samma datacenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Prestandavinsten från batchbearbetning är omedelbart uppenbar. I det föregående sekventiella testet tog 1000 åtgärder 129 sekunder utanför datacentret och 21 sekunder inifrån datacentret. Men med tabellvärdna parametrar tar 1000 åtgärder bara 2,6 sekunder utanför datacentret och 0,4 sekunder inom datacentret.

Mer information om tabellvärdade parametrar finns i [Tabellvärderade parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL-masskopia

SQL bulk kopia är ett annat sätt att infoga stora mängder data i en måldatabas. .NET-program kan använda klassen **SqlBulkCopy** för att utföra massinfogningar. **SqlBulkCopy** liknar funktionen i kommandoradsverktyget, **Bcp.exe**eller Transact-SQL-uttrycket, **BULK INSERT**. I följande kodexempel visas hur du masskopierar raderna i tabellen **DataTable**, till måltabellen i SQL Server, MyTable.

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

Det finns vissa fall där masskopia föredras framför tabellvärdade parametrar. Se jämförelsetabellen för tabellvärderade parametrar jämfört med MASS INSERT-åtgärder i artikeln [Tabellvärdade parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

Följande ad hoc-testresultat visar resultatet av batchbearbetning med **SqlBulkCopy** i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Azure samma datacenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

I mindre batchstorlekar överträffade de använda tabellvärdna parametrarna klassen **SqlBulkCopy.** **SqlBulkCopy** utförde dock 12-31% snabbare än tabellvärdna parametrar för testerna på 1 000 och 10 000 rader. Liksom tabellvärderade parametrar är **SqlBulkCopy** ett bra alternativ för batchade skär, särskilt jämfört med prestanda för icke-batchade åtgärder.

Mer information om masskopiering i ADO.NET finns [i Masskopieringsåtgärder i SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Parameterbedragna INSERT-satser med flera rader

Ett alternativ för små batchar är att konstruera en stor parameteriserad INSERT-sats som infogar flera rader. Följande kodexempel visar den här tekniken.

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

Det här exemplet är avsett att visa grundkonceptet. Ett mer realistiskt scenario skulle gå igenom de nödvändiga entiteterna för att konstruera frågesträngen och kommandoparametrarna samtidigt. Du är begränsad till totalt 2100 frågeparametrar, så detta begränsar det totala antalet rader som kan bearbetas på det här sättet.

Följande ad hoc-testresultat visar prestanda för den här typen av skärsats i millisekunder.

| Åtgärder | Tabellvärderade parametrar (ms) | INFOGA MED ETT UTDRAG (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Den här metoden kan vara något snabbare för batchar som är mindre än 100 rader. Även om förbättringen är liten är den här tekniken ett annat alternativ som kan fungera bra i ditt specifika programscenario.

### <a name="dataadapter"></a>DataAdapter (dataadapter)

Med klassen **DataAdapter** kan du ändra ett **DataSet-objekt** och sedan skicka ändringarna som insert-, UPDATE- och DELETE-åtgärder. Om du använder **DataAdapter** på detta sätt är det viktigt att notera att separata samtal görs för varje enskild operation. För att förbättra prestanda använder du egenskapen **UpdateBatchSize** till antalet åtgärder som ska batchas åt gången. Mer information finns i [Utföra batchoperationer med dataadapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Ramverk för entitet

Entity Framework stöder för närvarande inte batchbearbetning. Olika utvecklare i communityn har försökt demonstrera lösningar, till exempel åsidosätta **metoden SaveChanges.** Men lösningarna är vanligtvis komplexa och anpassade till programmet och datamodellen. Entity Framework-kodplexprojektet har för närvarande en diskussionssida om den här funktionsbegäran. Information om hur du visar den här diskussionen finns i [Anteckningar för designmöte - 2 augusti 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

För fullständighet anser vi att det är viktigt att prata om XML som en batchstrategi. Användningen av XML har dock inga fördelar jämfört med andra metoder och flera nackdelar. Metoden liknar tabellvärdna parametrar, men en XML-fil eller sträng skickas till en lagrad procedur i stället för en användardefinierad tabell. Den lagrade proceduren tolkar kommandona i den lagrade proceduren.

Det finns flera nackdelar med detta tillvägagångssätt:

* Att arbeta med XML kan vara besvärligt och felbenägna.
* Att tolka XML-koden i databasen kan vara processorkrävande.
* I de flesta fall är den här metoden långsammare än tabellvärdade parametrar.

Av dessa skäl rekommenderas inte användning av XML för batchfrågor.

## <a name="batching-considerations"></a>Batching överväganden

Följande avsnitt ger mer vägledning för användning av batchbearbetning i SQL Database-program.

### <a name="tradeoffs"></a>Kompromisser

Beroende på din arkitektur kan batchbearbetning innebära en avvägning mellan prestanda och återhämtning. Tänk till exempel på det scenario där din roll oväntat går ner. Om du förlorar en rad med data är effekten mindre än effekten av att förlora en stor batch med oinlämnade rader. Det finns en större risk när du buffrar rader innan du skickar dem till databasen i ett angivet tidsfönster.

På grund av den här kompromissen utvärderar du vilken typ av åtgärder som du batchar. Batch mer aggressivt (större batchar och längre tidsfönster) med data som är mindre kritiska.

### <a name="batch-size"></a>Batchstorlek

I våra tester fanns det vanligtvis ingen fördel med att bryta stora partier i mindre bitar. Faktum är att denna underavdelning ofta resulterade i långsammare prestanda än att skicka in en enda stor sats. Tänk dig till exempel ett scenario där du vill infoga 1 000 rader. Följande tabell visar hur lång tid det tar att använda tabellvärdade parametrar för att infoga 1 000 rader när de delas upp i mindre batchar.

| Batchstorlek | Iterationer | Tabellvärderade parametrar (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Du kan se att det bästa resultatet för 1000 rader är att skicka in dem alla på en gång. I andra tester (visas inte här), det fanns en liten prestanda vinst att bryta en 10000 rad parti i två partier av 5000. Men tabellschemat för dessa tester är relativt enkelt, så du bör utföra tester på dina specifika data och batchstorlekar för att verifiera dessa resultat.

En annan faktor att tänka på är att om den totala batchen blir för stor, SQL Database kan begränsa och vägra att genomföra batchen. Bäst resultat får du om du testar ditt specifika scenario för att avgöra om det finns en idealisk batchstorlek. Gör batchstorleken konfigurerbar vid körning för att aktivera snabba justeringar baserat på prestanda eller fel.

Slutligen balansera storleken på batchen med de risker som är förknippade med batchbearbetning. Om det finns tillfälliga fel eller om rollen misslyckas bör du överväga konsekvenserna av att försöka utföra åtgärden igen eller att förlora data i batchen.

### <a name="parallel-processing"></a>Parallellbearbetning

Vad händer om du tog tillvägagångssättet för att minska batchstorleken men använde flera trådar för att utföra arbetet? Återigen visade våra tester att flera mindre flertrådade partier vanligtvis presterade sämre än en enda större sats. Följande testförsök försöker infoga 1 000 rader i en eller flera parallella batchar. Det här testet visar hur fler samtidiga batchar faktiskt minskade prestanda.

| Batchstorlek [Iterationer] | Två trådar (ms) | Fyra trådar (ms) | Sex trådar (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultaten är inte riktmärken. Se [anmärkningen om tidsresultat i den här artikeln](#note-about-timing-results-in-this-article).
> 
> 

Det finns flera möjliga orsaker till nedbrytningen i prestanda på grund av parallellism:

* Det finns flera samtidiga nätverkssamtal i stället för ett.
* Flera åtgärder mot en enda tabell kan resultera i konkurrens och blockering.
* Det finns omkostnader som är associerade med flertrådning.
* Kostnaden för att öppna flera anslutningar överväger fördelen med parallell bearbetning.

Om du inriktar dig på olika tabeller eller databaser är det möjligt att se en viss prestandavinst med den här strategin. Databassharding eller federationer skulle vara ett scenario för den här metoden. Sharding använder flera databaser och dirigerar olika data till varje databas. Om varje liten batch går till en annan databas, sedan utföra operationerna parallellt kan vara effektivare. Prestandavinsten är dock inte tillräckligt viktig för att användas som grund för ett beslut om att använda databasskärva i din lösning.

I vissa utföranden kan parallell körning av mindre batchar resultera i förbättrad dataflöde av begäranden i ett system som är infilt. I det här fallet, även om det är snabbare att bearbeta en enda större batch, kan bearbetning av flera batchar parallellt vara effektivare.

Om du använder parallell körning bör du överväga att kontrollera det maximala antalet arbetstrådar. Ett mindre tal kan resultera i mindre konkurrens och snabbare körningstid. Tänk också på den ytterligare belastning som den här placerar i måldatabasen både i anslutningar och transaktioner.

### <a name="related-performance-factors"></a>Relaterade prestandafaktorer

Typisk vägledning om databasprestanda påverkar också batchbearbetning. Infoga prestanda minskas till exempel för tabeller som har en stor primärnyckel eller många icke-grupperade index.

Om tabellvärderade parametrar använder en lagrad procedur kan du använda kommandot **SET NOCOUNT ON** i början av proceduren. Den här satsen undertrycker avkastningen av antalet berörda rader i proceduren. Men i våra tester, användningen av **SET NOCOUNT ON** antingen hade ingen effekt eller minskad prestanda. Den testbevarade proceduren var enkel med ett enda **INSERT-kommando** från parametern för tabellvärde. Det är möjligt att mer komplexa lagrade förfaranden skulle gynnas av detta uttalande. Men anta inte att om du lägger **TILL SET NOCOUNT ON** i den lagrade proceduren förbättras prestanda automatiskt. För att förstå effekten, testa din lagrade procedur med och utan **SET NOCOUNT ON-satsen.**

## <a name="batching-scenarios"></a>Batch-scenarier

I följande avsnitt beskrivs hur du använder tabellvärdade parametrar i tre programscenarier. Det första scenariot visar hur buffring och batchbearbetning kan fungera tillsammans. Det andra scenariot förbättrar prestanda genom att utföra huvudinformationsåtgärder i ett enda lagrat proceduranrop. Det slutliga scenariot visar hur du använder tabellvärdna parametrar i en "UPSERT"-åtgärd.

### <a name="buffering"></a>Buffrar

Även om det finns vissa scenarier som är uppenbara kandidat för batchbearbetning, finns det många scenarier som kan dra nytta av batchbearbetning genom fördröjd bearbetning. Men fördröjd bearbetning medför också en större risk för att data går förlorade i händelse av ett oväntat fel. Det är viktigt att förstå denna risk och överväga konsekvenserna.

Tänk dig till exempel ett webbprogram som spårar navigeringshistoriken för varje användare. På varje sidbegäran kan programmet ringa ett databasanrop för att registrera användarens sidvy. Men högre prestanda och skalbarhet kan uppnås genom att buffra användarnas navigeringsaktiviteter och sedan skicka dessa data till databasen i batchar. Du kan utlösa databasuppdateringen med förfluten tids- och/eller buffertstorlek. En regel kan till exempel ange att batchen ska bearbetas efter 20 sekunder eller när bufferten når 1 000 artiklar.

I följande kodexempel används [Reaktiva tillägg - Rx](https://msdn.microsoft.com/data/gg577609) för att bearbeta buffrade händelser som utlöses av en övervakningsklass. När bufferten fylls eller en timeout nås skickas batchen med användardata till databasen med en parameter som värderas av tabell.

Följande NavHistoryData-klass modellerar användarnavigeringsinformationen. Den innehåller grundläggande information som användaridentifieraren, den åtkomst till webbadressen och åtkomsttiden.

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

Klassen NavHistoryDataMonitor ansvarar för att buffra användarnavigeringsdata till databasen. Den innehåller en metod, RecordUserNavigationEntry, som svarar genom att höja en **OnAdded** händelse. Följande kod visar konstruktorlogiken som använder Rx för att skapa en observerbar samling baserat på händelsen. Den prenumererar sedan på den här observerbara samlingen med buffertmetoden. Överbelastningen anger att bufferten ska skickas var 20:e sekund eller 1000 poster.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Hanteraren konverterar alla buffrade artiklar till en tabellvärderad typ och skickar sedan den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar den fullständiga definitionen för både NavHistoryDataEventArgs och NavHistoryDataMonitor-klasserna.

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

Hanteraren konverterar alla buffrade artiklar till en tabellvärderad typ och skickar sedan den här typen till en lagrad procedur som bearbetar batchen. Följande kod visar den fullständiga definitionen för både NavHistoryDataEventArgs och NavHistoryDataMonitor-klasserna.

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

Om du vill använda den här buffringsklassen skapar programmet ett statiskt NavHistoryDataMonitor-objekt. Varje gång en användare öppnar en sida anropar programmet metoden NavHistoryDataMonitor.RecordUserNavigationEntry. Buffringslogiken fortsätter att ta hand om att skicka dessa poster till databasen i batchar.

### <a name="master-detail"></a>Huvuddetalj

Tabellvärderade parametrar är användbara för enkla INSERT-scenarier. Det kan dock vara svårare att batchinfogningar som omfattar mer än en tabell. Scenariot "master/detail" är ett bra exempel. Huvudtabellen identifierar den primära entiteten. I en eller flera detaljerade tabeller lagras mer data om entiteten. I det här fallet upprätthåller relationer med främmande nyckel relation relationen mellan detaljer till en unik huvudentitet. Överväg en förenklad version av en Inköpsordertabell och dess associerade OrderDetail-tabell. Följande Transact-SQL skapar tabellen Inköpsorder med fyra kolumner: OrderID, OrderDate, CustomerID och Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Varje order innehåller ett eller flera produktinköp. Den här informationen samlas in i tabellen Inköpsorderdetail. Följande Transact-SQL skapar tabellen PurchaseOrderDetail med fem kolumner: OrderID, OrderDetailID, ProductID, UnitPrice och OrderQty.

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

Kolumnen OrderID i tabellen Inköpsorderdetail måste referera till en order från tabellen Inköpsorder. Följande definition av en sekundär nyckel tillämpar denna begränsning.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Om du vill använda tabellvärdade parametrar måste du ha en användardefinierad tabelltyp för varje måltabell.

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

Definiera sedan en lagrad procedur som accepterar tabeller av dessa typer. Med den här proceduren kan ett program lokalt batcha en uppsättning order och orderinformation i ett enda anrop. Följande Transact-SQL innehåller fullständig lagrad procedurdeklaration för det här inköpsorderexemplet.

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

I det här exemplet @IdentityLink lagrar den lokalt definierade tabellen de faktiska OrderID-värdena från de nyligen infogade raderna. Dessa orderidentifierare skiljer sig från de @orders tillfälliga @details OrderID-värdena i parametrarna och tabellvärderade. Därför kopplar @IdentityLink tabellen sedan OrderID-värdena från @orders parametern till de verkliga OrderID-värdena för de nya raderna i tabellen Inköpsorder. Efter det här @IdentityLink steget kan tabellen underlätta att infoga orderinformationen med det faktiska OrderID som uppfyller sekundärnyckelbegränsningen.

Den här lagrade proceduren kan användas från kod eller från andra Transact-SQL-anrop. Se avsnittet tabellvärdade parametrar i det här dokumentet för ett kodexempel. Följande Transact-SQL visar hur du anropar sp_InsertOrdersBatch.

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

Den här lösningen gör det möjligt för varje batch att använda en uppsättning OrderID-värden som börjar vid 1. Dessa tillfälliga OrderID-värden beskriver relationerna i batchen, men de faktiska OrderID-värdena bestäms vid tidpunkten för infogningen. Du kan köra samma satser i föregående exempel upprepade gånger och generera unika order i databasen. Av den här anledningen bör du överväga att lägga till mer kod- eller databaslogik som förhindrar dubblettorder när du använder den här batchtekniken.

Det här exemplet visar att ännu mer komplexa databasåtgärder, till exempel huvuddetaljåtgärder, kan batchas med tabellvärderade parametrar.

### <a name="upsert"></a>UPSERT (UPPSERT)

Ett annat batchscenario innebär att befintliga rader uppdateras samtidigt och infoga nya rader. Den här åtgärden kallas ibland för en "UPSERT" (uppdatering + infoga) åtgärd. I stället för att göra separata anrop till INSERT och UPDATE passar MERGE-satsen bäst för den här uppgiften. MERGE-satsen kan utföra både infognings- och uppdateringsåtgärder i ett enda anrop.

Tabellvärdna parametrar kan användas med MERGE-satsen för att utföra uppdateringar och infogningar. Tänk dig till exempel en förenklad medarbetartabell som innehåller följande kolumner: EmployeeID, Förnamn, Efternamn, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

I det här exemplet kan du använda det faktum att SocialSecurityNumber är unikt för att utföra en sammanslagning av flera anställda. Skapa först den användardefinierade tabelltypen:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Skapa sedan en lagrad procedur eller skrivkod som använder MERGE-satsen för att utföra uppdateringen och infogningen. I följande exempel används MERGE-satsen för @employeesen tabellvärderad parameter, av typen EmployeeTableType. Innehållet i @employees tabellen visas inte här.

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

Mer information finns i dokumentationen och exemplen för MERGE-satsen. Även om samma arbete kan utföras i ett flerstegssamtal med separata INSERT- och UPDATE-åtgärder, är MERGE-satsen effektivare. Databaskod kan också konstruera Transact-SQL-anrop som använder MERGE-satsen direkt utan att kräva två databasanrop för INSERT och UPDATE.

## <a name="recommendation-summary"></a>Sammanfattning av rekommendationen

Följande lista innehåller en sammanfattning av batchrekommendationerna som beskrivs i den här artikeln:

* Använd buffring och batchbearbetning för att öka prestanda och skalbarhet för SQL Database-program.
* Förstå kompromisserna mellan batchbearbetning/buffring och återhämtning. Under ett rollfel kan risken för att förlora en obearbetad batch med affärskritiska data uppväga prestandafördelarna med batchbearbetning.
* Försök att behålla alla anrop till databasen i ett enda datacenter för att minska svarstiden.
* Om du väljer en enda batchteknik erbjuder tabellvärderade parametrar bästa prestanda och flexibilitet.
* För den snabbaste insatsprestandan följer du dessa allmänna riktlinjer men testar ditt scenario:
  * För < 100 rader använder du ett enda parameteriserat INSERT-kommando.
  * För < 1 000 rader använder du tabellvärdade parametrar.
  * För >= 1000 rader använder du SqlBulkCopy.
* För uppdaterings- och borttagningsåtgärder använder du tabellvärderade parametrar med lagrad procedurlogik som bestämmer rätt åtgärd på varje rad i tabellparametern.
* Riktlinjer för batchstorlek:
  * Använd de största batchstorlekarna som är meningsfulla för dina program- och affärskrav.
  * Balansera prestandavinsten för stora batchar med riskerna för tillfälliga eller katastrofala fel. Vad är konsekvensen av återförsök eller förlust av data i partiet? 
  * Testa den största batchstorleken för att kontrollera att SQL Database inte avvisar den.
  * Skapa konfigurationsinställningar som styr batchbearbetning, till exempel batchstorlek eller bufferttidsfönstret. Dessa inställningar ger flexibilitet. Du kan ändra batchningsbeteendet i produktionen utan att distribuera molntjänsten om.
* Undvik parallell körning av batchar som fungerar på en enda tabell i en databas. Om du väljer att dela upp en enda batch över flera arbetstrådar kör du tester för att fastställa det perfekta antalet trådar. Efter ett ospecificerat tröskelvärde minskar fler trådar prestanda i stället för att öka den.
* Överväg buffring på storlek och tid som ett sätt att implementera batchbearbetning för fler scenarier.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på hur databasdesign och kodningstekniker relaterade till batchbearbetning kan förbättra programmets prestanda och skalbarhet. Men detta är bara en faktor i din övergripande strategi. Fler sätt att förbättra prestanda och skalbarhet finns i [Prestandavägledning för Azure SQL Database för enskilda databaser](sql-database-performance-guidance.md) och [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool-guidance.md).

