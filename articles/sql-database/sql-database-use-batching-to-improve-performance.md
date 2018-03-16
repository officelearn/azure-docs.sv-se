---
title: "Hur du använder batchbearbetning för att förbättra prestanda för Azure SQL Database-program"
description: "Avsnittet ger bevis som batching databasåtgärder avsevärt imroves hastighet och skalbarheten för dina Azure SQL Database-program. Även om dessa batching metoder fungera för SQL Server-databas, fokuserar av artikeln på Azure."
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 07/12/2016
ms.author: sstein
ms.openlocfilehash: 5c7846fdd8d6a7584cab2b4f3811151332171ba4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Hur du använder batchbearbetning för att förbättra programmens prestanda för SQL-databas
Batchbearbetning operations till Azure SQL Database avsevärt bättre prestanda och skalbarhet. Den första delen av den här artikeln innehåller några exempel på testresultat som jämför sekventiella och batch-förfrågningar till en SQL-databas för att förstå fördelarna. Resten av artikeln visar tekniker, scenarier och överväganden som hjälper dig att använda batchbearbetning har i din Azure-program.

## <a name="why-is-batching-important-for-sql-database"></a>Varför är batchbearbetning viktigt för SQL-databas?
Batchbearbetning anrop till en fjärrtjänsten är en välkänd strategi för att öka prestanda och skalbarhet. Det är fasta bearbetningskostnaderna för eventuella interaktioner med en fjärransluten tjänst, till exempel serialisering och nätverksöverföring deserialisering. Paketera många separata transaktioner i en enskild batch minimerar dessa kostnader.

I det här dokumentet som vi vill undersöka olika SQL-databas batchbearbetning strategier och scenarier. Men strategierna är också viktigt för lokala program som använder SQL Server, finns det flera orsaker till syntaxmarkering användningen av batchbearbetning för SQL-databas:

* Det finns potentiellt större Nätverksfördröjningen för åtkomst till SQL-databas, särskilt om du ansluter till SQL-databas från utanför samma Microsoft Azure-datacenter.
* Flera egenskaper för SQL-databasen innebär att effektiviteten för de data access layer motsvarar övergripande skalbarhet i databasen. SQL-databas måste förhindra att en enskild klient/användare alltid använder databasresurser till skada för andra klienter. Som svar på användning utöver fördefinierade kvoter SQL Database minska eller svara med begränsning undantag. Effektivitet, till exempel batchbearbetning, kan du fortsätta att arbeta med SQL-databasen innan det nådde dessa begränsningar. 
* Batchbearbetning är också gälla för konfigurationer med flera databaser (delning). Effektivitet för din interaktion med varje enhet i databasen är fortfarande en avgörande faktor för din övergripande skalbarhet. 

En av fördelarna med att använda SQL-databas är att du inte behöver hantera servrar som värd för databasen. Hanterade infrastrukturen innebär dock också att du behöver tänka på olika sätt på databasoptimering. Du kan inte längre leta för att förbättra databasinfrastruktur datormaskinvaran eller nätverksanslutningen. Microsoft Azure styr dessa miljöer. Området du kan styra är hur programmet samverkar med SQL-databas. Batchbearbetning är en av dessa optimeringar. 

Den första delen av dokumentet undersöker olika batching tekniker för .NET-program som använder SQL-databas. De sista två avsnitt beskriver batchbearbetning riktlinjer och scenarier.

## <a name="batching-strategies"></a>Batchbearbetning strategier
### <a name="note-about-timing-results-in-this-topic"></a>Observera om tidsinställning resultat i det här avsnittet
> [!NOTE]
> Resultatet är inte prestandamått men är avsedda att visa **relativa prestandan**. Tidsinställningar baseras på ett genomsnitt av minst 10 testkörningar. Åtgärder som infogar i en tom tabell. Dessa tester har uppmätta pre-V12 och de inte nödvändigtvis motsvarar dataflödet som kan uppstå i en V12-databas med den nya [tjänstnivåer](sql-database-service-tiers.md). Relativa fördelen att batching tekniken bör vara densamma.
> 
> 

### <a name="transactions"></a>Transaktioner
Det verkar konstigt att påbörja en granskning av batchbearbetning av diskutera transaktioner. Men användning av transaktioner på klientsidan har en diskret serversidan batching effekt som förbättrar prestanda. Och transaktioner kan läggas till med bara några få rader med kod, så att de är ett snabbt sätt att förbättra prestanda för sekventiella operationer.

Överväg följande C#-koden som innehåller en sekvens med insert och uppdateringsåtgärder på en enkel tabell.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Följande kod ADO.NET utför sekventiellt dessa åtgärder.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Det bästa sättet att optimera koden är att genomföra någon form av klientsidan massbearbetning av dessa anrop. Men det finns ett enkelt sätt att öka prestanda för den här koden genom att helt enkelt omsluta sekvens med anrop i en transaktion. Här är samma kod som använder en transaktion.

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

Transaktioner används faktiskt i båda de här exemplen. I det första exemplet är varje enskild anropet en implicit transaktion. I det andra exemplet radbryts alla anropen med en explicit transaktion. Per i dokumentationen för den [write-ahead transaktionsloggen](https://msdn.microsoft.com/library/ms186259.aspx), loggposter är replikatorn tömmer till disken när transaktionen genomförts. Så genom att lägga till fler anrop i en transaktion kan skrivningen till transaktionsloggen fördröja tills genomförs transaktionen. Du aktiverar i praktiken batchbearbetning för skrivningar till serverns transaktionsloggen.

I följande tabell visas några ad hoc-testresultaten. Samma sekventiella infogningarna med och utan transaktioner tester. För mer perspektiv körde den första uppsättningen tester via fjärranslutning från en bärbar dator till databasen i Microsoft Azure. Den första uppsättningen tester körde från en tjänst i molnet och båda finns i samma datacenter (USA, västra) för Microsoft Azure-databas. Följande tabell visar varaktigheten i millisekunder för sekventiella infogningar med och utan transaktioner.

**Lokalt till Azure**:

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure till Azure (samma datacenter)**:

| Åtgärder | Ingen transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

Baserat på föregående testresultaten, minskar byta en enda åtgärd i en transaktion faktiskt prestanda. Men om du ökar antalet åtgärder i en enda transaktion förbättring av prestanda blir större. Prestandaskillnaden är också mer märkbart när alla åtgärder som vidtas i Microsoft Azure-datacenter. Ökad latens med SQL-databas från utanför Microsoft Azure-datacenter överskuggar prestandafördelar med transaktioner.

Även om användning av transaktioner kan öka prestanda, fortsätter att [Se metodtips för transaktioner och anslutningar](https://msdn.microsoft.com/library/ms187484.aspx). Behåll transaktioner så kort som möjligt och Stäng databasanslutningen när arbetet är klar. Den med hjälp av instruktionen i det förra exemplet säkerställer att anslutningen är stängd när efterföljande kodblocket har slutförts.

Föregående exempel visar att du kan lägga till en lokal transaktion ADO.NET koden med två rader. Transaktioner erbjuder ett snabbt sätt att förbättra prestanda för kod som gör sekventiella infoga, uppdatera och ta bort. Men för bästa prestanda, Överväg att ändra koden efter dra nytta av klientsidan batchbearbetning, till exempel tabellvärdeparametrar.

Mer information om transaktioner i ADO.NET finns [lokala transaktioner i ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>tabellvärdeparametrar
Tabellvärdeparametrar stöd för användardefinierade tabelltyper som parametrar i Transact-SQL-uttryck, lagrade procedurer och funktioner. Den här batching tekniken på klientsidan kan du skicka flera rader med data i parametern-tabellvärdesfunktion. Om du vill använda tabellvärdeparametrar måste du först definiera en tabelltyp. Följande Transact-SQL-instruktion skapas en tabelltyp som heter **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


I koden, skapar du en **DataTable** med exakt samma namn och typer av tabelltypen. Skicka detta **DataTable** i en parameter i en textfråga eller en lagrad procedur anropa. I följande exempel visas den här tekniken:

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

I föregående exempel är den **SqlCommand** objekt infogar rader från en tabellvärdesparameter  **@TestTvp** . Den tidigare skapade **DataTable** objekt som har tilldelats den här parametern med det **SqlCommand.Parameters.Add** metod. Batchbearbetning infogningar i ett anrop avsevärt ökar prestanda över sekventiella infogningar.

Använda en lagrad procedur i stället för ett textbaserat kommando för att förbättra det tidigare exemplet ytterligare. Följande Transact-SQL-kommando skapar en lagrad procedur som tar den **SimpleTestTableType** tabellvärdesparametern.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Ändra den **SqlCommand** deklarationen i det förra kodexemplet till följande objekt.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

I de flesta fall har tabellvärdeparametrar motsvarande eller bättre prestanda än andra batching metoder. Tabellvärdeparametrar är ofta bättre, eftersom de är mer flexibelt än andra alternativ. Till exempel tillåter andra tekniker, till exempel SQL-masskopiering, bara nya rader infogas. Men med tabellvärdeparametrar, du kan använda logiken i den lagrade proceduren för att avgöra vilka rader som uppdateras och som kan infogas. Tabelltypen kan också ändras så att den innehåller en ”åtgärden”-kolumn som anger om den angivna raden ska infogas, uppdateras eller tas bort.

Följande tabell visar ad hoc-testresultaten för användning av tabellvärdeparametrar i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Samma Azure-datacenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

Prestandafördelar från batchbearbetning är uppenbar. I det föregående sekventiella testet tog 1000 åtgärderna 129 sekunder utanför datacentret och 21 sekunder från inom datacentret. Men med tabellvärdeparametrar, 1000 operations Ta endast 2.6 sekunder utanför datacentret och 0,4 sekunder inom datacentret.

Mer information om tabellvärdeparametrar finns [Table-Valued parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL-masskopiering
SQL-masskopiering är ett annat sätt att infoga stora mängder data i en måldatabasen. .NET-program kan använda den **SqlBulkCopy körs** klassen för att utföra bulk insert åtgärder. **SqlBulkCopy körs** liknar i funktionen kommandoradsverktyget **Bcp.exe**, eller Transact-SQL-instruktion **BULK INSERT**. Följande kodexempel visar hur du masskopiera raderna i källan **DataTable**, tabell mytable prefix till måltabellen i SQL Server.

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

Det finns tillfällen där masskopiering är att föredra över tabellvärdeparametrar. Se jämförelsetabellen av tabellvärdeparametrar jämfört med BULK INSERT åtgärder i avsnittet [Table-Valued parametrar](https://msdn.microsoft.com/library/bb510489.aspx).

Följande ad hoc-testresultaten visar prestanda för batchbearbetning med **SqlBulkCopy körs** i millisekunder.

| Åtgärder | Lokalt till Azure (ms) | Samma Azure-datacenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

I batch är mindre, Använd tabellvärdeparametrar gick bättre än förväntat den **SqlBulkCopy körs** klass. Dock **SqlBulkCopy körs** utföras 12-31% snabbare än tabellvärdeparametrar för testerna av 1 000 och 10 000 rader. Som tabellvärdeparametrar, **SqlBulkCopy körs** är ett bra alternativ för gruppbaserad infogningar särskilt jämfört med utförandet av ett annat operationer.

Mer information om masskopiering i ADO.NET finns [Masskopieringsåtgärder i SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Flera rader som innehåller parametrar Infoga instruktioner
Ett alternativ för små batchar är att skapa en stor parametriserade INSERT-sats som infogar flera rader. Följande kodexempel visar den här tekniken.

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


Det här exemplet är avsedd att visa det grundläggande konceptet. En mer realistisk scenariot skulle gå igenom entiteterna krävs för att konstruera frågesträngen och parametrarna samtidigt. Du är begränsad till totalt 2100 frågeparametrar, så detta begränsar det totala antalet rader som kan bearbetas i det här sättet.

Följande ad hoc-testresultaten visar prestanda för den här typen av insert-instruktionen i millisekunder.

| Åtgärder | Tabellvärdeparametrar (ms) | Infoga enstaka uttryck (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

Den här metoden kan vara något snabbare för batchar som är mindre än 100 rader. Även om förbättring är liten är tekniken ett annat alternativ som fungerar bra i ditt specifika program scenario.

### <a name="dataadapter"></a>DataAdapter
Den **DataAdapter** klassen kan du ändra en **DataSet** objektet och sedan skicka ändringarna som INSERT, UPDATE och DELETE-åtgärder. Om du använder den **DataAdapter** på detta sätt är det viktigt att notera att anrop görs för varje distinkta åtgärd. Du kan förbättra prestanda i **UpdateBatchSize** egenskapen antal åtgärder som ska grupperas i taget. Mer information finns i [utför Batch åtgärder med hjälp av DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entity framework
Entity Framework stöder för närvarande inte batchbearbetning. Olika utvecklare i gemenskapen har försökt att visa lösningar, till exempel åsidosättning av **SaveChanges** metod. Men lösningarna som vanligtvis är komplicerade och anpassade program och datamodellen. Entity Framework codeplex projektet har för närvarande en diskussionssida på den här funktionsbegäran. Om du vill visa den här diskussionen [Design mötesanteckningar - 2 augusti 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
För fullständighetens skull bör du att det är viktigt att tala om XML-Datatypen som en batching strategi. Användning av XML har dock inga fördelar över andra metoder och flera nackdelar. Metoden som liknar tabellvärdeparametrar, men en XML-fil eller sträng har överförts till en lagrad procedur i stället för en användardefinierad tabell. Den lagrade proceduren Parsar kommandon i den lagrade proceduren.

Det finns flera nackdelar med att den här metoden:

* Arbeta med XML kan vara krånglig och tillförlitligt.
* Parsning av XML-filen på databasen kan vara Processorn märkbart.
* I de flesta fall är den här metoden går långsammare än om tabellvärdeparametrar.

Därför rekommenderas inte användning av XML för batch-frågor.

## <a name="batching-considerations"></a>Batchbearbetning överväganden
Följande avsnitt innehåller mer hjälp för användning av batchbearbetning i SQL Database-program.

### <a name="tradeoffs"></a>Nackdelar
Beroende på din arkitektur involverar batchbearbetning en kompromiss mellan prestanda och återhämtning. Tänk dig ett scenario där din roll oväntat stängs av. Om du tappar bort en rad med data är effekten mindre än effekten av att förlora en stor grupp med rader som inte har skickats. Det finns en större risk när du buffert rader innan de skickas till databasen i ett angivet tidsintervall.

På grund av det här förhållandet utvärdera vilken typ av åtgärder som du batch. Batch mer aggressivt (större batchar och längre tidsfönster) med data som är mindre viktigt.

### <a name="batch-size"></a>Batchstorlek
I våra tester fanns det vanligtvis ingen fördel med att dela upp stora batchar i mindre segment. Faktum är resulterade ofta den här delfältet i långsammare än att skicka en enda stor grupp. Tänk dig ett scenario där du vill infoga 1000 översta raderna. Följande tabell visar hur lång tid det tar för att använda tabellvärdeparametrar för att infoga 1000 översta raderna när indelat i mindre batchar.

| Batchstorlek | Upprepningar | Tabellvärdeparametrar (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

Du kan se att bästa prestanda för 1000 översta raderna är att skicka dem på samma gång. Det uppstod små prestandafördelar att dela en 10000 raden batch i två grupper med 5000 i andra tester (visas inte här). Men tabellschemat för dessa tester är relativt enkla, så du bör utföra tester på specifika data och batch-storlekar för att verifiera dessa resultat.

En annan faktor är att om den totala batchen blir för stor, SQL-databas kan begränsa vägrar att bekräfta batchen. Testa din situation för att fastställa om det finns en perfekt batchstorlek för bästa resultat. Se batchstorleken konfigureras vid körningen för snabba justeringar utifrån prestanda eller fel.

Slutligen balansera storlek på batch med risker med batchbearbetning. Om det är tillfälliga fel eller rollen misslyckas, bör du konsekvenserna av du försöker igen eller förlust av data i batchen.

### <a name="parallel-processing"></a>Parallell bearbetning
Vad händer om du tog metod för att minska batchstorleken men använda flera trådar för att utföra arbetet? Våra tester visade igen att flera mindre flertrådade batchar vanligtvis utförs värre än en enskild större batch. Följande test försöker infoga 1000 rader i en eller flera parallella batchar. Det här testet visar hur flera samtidiga batchar faktiskt minskade prestanda.

| Batchstorlek [iterationer] | Två trådar (ms) | Fyra trådar (ms) | Sex trådar (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultatet är inte prestandamått. Finns det [anteckning om tidsinställning resultat i det här avsnittet](#note-about-timing-results-in-this-topic).
> 
> 

Det finns flera möjliga orsaker till sämre prestanda på grund av parallellitet:

* Det finns flera samtidiga nätverket anrop i stället för en.
* Flera åtgärder mot en tabell kan resultera i konkurrens och blockerar.
* Det finns kostnader som flertrådsteknik.
* Kostnaden för öppna flera anslutningar uppväger fördelen med parallell bearbetning.

Om du anpassar olika tabeller eller databaser, är det möjligt att se vissa prestanda få med den här strategin. Horisontell partitionering databasen eller federationer är ett scenario för den här metoden. Horisontell partitionering använder flera databaser och dirigerar olika data för varje databas. Om en annan databas ska varje liten grupp, kan utför operationerna parallellt vara mer effektivt. Prestanda är inte tillräckligt stor för ska användas som grund för ett beslut för att använda databasen delning i din lösning.

I vissa Designer leda parallell körning av mindre batchar till bättre genomflöde begäranden i ett system under belastning. I det här fallet trots att det går snabbare att bearbeta en enskild större batch kan bearbeta flera batchar parallellt vara mer effektivt.

Om du använder parallell körning, bör du kontrollera det maximala antalet trådar. Ett mindre antal leda till mindre konkurrens och snabbare körningstid. Du kan också den ytterligare belastningen som placeras på måldatabasen både anslutningar och transaktioner.

### <a name="related-performance-factors"></a>Prestandadata relaterade faktorer
Vanliga vägledning om databasprestanda påverkar också batchbearbetning. Till exempel infoga minskar prestanda för tabeller som har en primärnyckel för stora eller många icke-grupperat index.

Om tabellvärdeparametrar använder en lagrad procedur kan du använda kommandot **SET NOCOUNT ON** i början av proceduren. Den här instruktionen Undertrycker avkastning för antalet berörda rader i proceduren. Men i våra tester kan användningen av **SET NOCOUNT ON** inte påverkar eller minskade prestanda. Testa lagrade proceduren har enkelt med en enda **infoga** från parametern-tabellvärdesfunktion. Det är möjligt att mer komplexa lagrade procedurer skulle dra nytta av den här instruktionen. Men förutsätt inte att lägga till **SET NOCOUNT ON** till den lagrade proceduren automatiskt förbättrar prestanda. Testa den lagrade proceduren med och utan för att förstå effekten av **SET NOCOUNT ON** instruktionen.

## <a name="batching-scenarios"></a>Batchbearbetning scenarier
I följande avsnitt beskrivs hur du använder tabellvärdeparametrar i tre scenarier för programmet. Det första scenariot visar hur buffring och batchbearbetning kan fungera tillsammans. Det andra scenariot förbättrar prestanda genom att utföra åtgärder för master-detaljer i en enda lagrade proceduranropet. Det sista scenariot visar hur du använder tabellvärdeparametrar i en ”UPSERT”-åtgärd.

### <a name="buffering"></a>Buffring
Men det är några scenarier som är uppenbara kandidat för batchbearbetning, finns det många scenarier som kan dra nytta av batchbearbetning av fördröjd bearbetning. Fördröjd bearbetningen har dock en större risk att data förloras vid ett oväntat fel. Det är viktigt att förstå den här risken och Överväg konsekvenserna.

Tänk dig ett webbprogram som spårar navigeringshistoriken för varje användare. Programmet kunde göra en databas-anrop för att registrera vyn sida på varje sida i begäran. Men högre prestanda och skalbarhet kan uppnås genom buffring användarnas navigering aktiviteter och informationen skickas till databasen i batchar. Du kan utlösa database-uppdateringen av förfluten tid och/eller buffertstorlek. En regel kan till exempel ange att gruppen ska bearbetas efter 20 sekunder eller när bufferten når 1 000 objekt.

Följande kodexempel används [reaktiv tillägg - Rx](https://msdn.microsoft.com/data/gg577609) bearbeta buffertlagrade händelser som skapats av en övervakningsklass. När bufferten fyller eller en tidsgräns uppnås, i gruppen med användardata skickas till databasen med en tabellvärdesparameter.

Följande NavHistoryData klass modeller navigering användarinformation. Den innehåller grundläggande information, till exempel användar-ID, URL: en som öppnas och åtkomst-tid.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

Klassen NavHistoryDataMonitor ansvarar för buffring navigering användardata till databasen. Den innehåller en metod, RecordUserNavigationEntry som svarar genom att en **OnAdded** händelse. Följande kod visar konstruktorn logiken som använder Rx för att skapa en synliga samling baserat på händelsen. Den sedan prenumererar synliga samlingen med metoden buffert. Överlagring anger att bufferten ska skickas varje 20 sekunder eller 1000 poster.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Hanteraren konverterar alla buffrade objekt till en tabellvärdesfunktion typ och skickar sedan denna typ till en lagrad procedur som bearbetar batchen. Följande kod visar slutförd definitionen för både NavHistoryDataEventArgs och NavHistoryDataMonitor-klasser.

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

        public void RecordUserNavigationEntry(NavHistoryData data)
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

Om du vill använda den här buffring klassen skapar programmet ett statiskt NavHistoryDataMonitor-objekt. Varje gång en användare ansluter till en sida anropar programmet metoden NavHistoryDataMonitor.RecordUserNavigationEntry. Buffring logiken fortsätter att vara noga med att skicka dessa poster till databasen i batchar.

### <a name="master-detail"></a>Master detaljer
Tabellvärdeparametrar är användbara för enkla INSERT-scenarier. Det kan vara svårare att batch infogningar som omfattar mer än en tabell. ”Översikt/detaljer” scenario är ett bra exempel. Huvudtabellen identifierar den primära entiteten. En eller flera tabeller i detalj lagra mer data om enheten. I det här scenariot framtvinga sekundärnyckelrelationer relationen mellan information till en unik master entitet. Överväg att en förenklad version av PurchaseOrder tabellerna och dess associerade OrderDetail. Följande Transact-SQL skapar PurchaseOrder-tabell med fyra kolumner: OrderID, OrderDate, CustomerID och Status.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Varje innehåller en eller flera produktinköp. Den här informationen samlas i tabellen PurchaseOrderDetail. Följande Transact-SQL skapar PurchaseOrderDetail-tabell med fem kolumner: OrderID, OrderDetailID, ProductID, Enhetspris och OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

En order måste referera till kolumnen OrderID i tabellen PurchaseOrderDetail från tabellen PurchaseOrder. Följande definition av en sekundärnyckel tillämpar den här begränsningen.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Du måste ha en användardefinierad tabelltyp för varje måltabellen för att kunna använda tabellvärdeparametrar.

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

Definiera en lagrad procedur som accepterar tabeller för dessa typer. Den här proceduren kan ett program till en uppsättning order och orderinformationen i ett enda anrop batch-lokalt. Följande Transact-SQL ger fullständig lagrade procedur-deklaration för det här köpet ordning exemplet.

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

I det här exemplet är det lokalt definierade @IdentityLink tabellen lagras de faktiska värdena OrderID från nyligen infogade rader. Dessa identifierare ordning skiljer sig från tillfälliga OrderID värdena i den @orders och @details tabellvärdeparametrar. Därför den @IdentityLink tabell ansluter sedan OrderID värdena från den @orders parameter till verkliga OrderID värdena för nya rader i tabellen PurchaseOrder. Efter det här steget i @IdentityLink tabell kan underlätta Infoga orderinformationen med faktiska OrderID som uppfyller foreign key-begränsningen.

Den här lagrade proceduren kan användas från kod eller andra Transact-SQL-anrop. Se avsnittet tabellvärdeparametrar för det här dokumentet som ett exempel. Följande Transact-SQL visar hur du anropar sp_InsertOrdersBatch.

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

Den här lösningen kan varje batch för att använda en uppsättning OrderID värden som börjar vid 1. Värdena för tillfälliga OrderID beskriva relationer i batchen, men de faktiska värdena OrderID bestäms vid tidpunkten för insert-åtgärden. Du kan köra samma instruktioner i föregående exempel upprepade gånger och generera unika order i databasen. Överväg att lägga till mer kod eller databasen logik som förhindrar att duplicerade order när du använder detta batchbearbetning tekniken därför.

Det här exemplet visar att ännu mer komplexa databasåtgärder, till exempel översikt detaljer operations kan grupperas med tabellvärdeparametrar.

### <a name="upsert"></a>UPSERT
En annan batching scenariet inbegriper samtidigt uppdaterar befintliga rader och infoga nya rader. Den här åtgärden kallas ibland för en ”UPSERT” (update + insert)-åtgärd. I stället för separata anropar infoga och uppdatera MERGE-instruktion som är bäst för den här uppgiften. MERGE-instruktion kan utföra både insert och uppdatera åtgärder i ett enda anrop.

Tabellvärdeparametrar kan användas med MERGE-instruktion för att utföra uppdateringar och infogningar. Anta till exempel att en förenklad medarbetare tabell som innehåller följande kolumner: EmployeeID, FirstName, LastName, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

Du kan använda det faktum att SocialSecurityNumber är unikt för dokument för flera anställda i det här exemplet. Först skapar du användardefinierade tabelltypen:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Sedan skapar en lagrad procedur eller skriva kod som använder MERGE-instruktion för att utföra uppdateringen och infoga. I följande exempel används MERGE-instruktion på en tabellvärdesparameter @employees, av typen EmployeeTableType. Innehållet i den @employees tabellen visas inte här.

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

Mer information finns i dokumentation och exempel för MERGE-instruktion. Även om samma arbets kunde utföras i en flera steg lagras proceduranropet med separata INSERT och UPDATE-åtgärder, MERGE-instruktionen är effektivare. Databaskod kan också skapa Transact-SQL-anrop som MERGE-instruktion direkt utan att två databasanrop för INSERT och UPDATE.

## <a name="recommendation-summary"></a>Rekommendation sammanfattning
Följande lista innehåller en sammanfattning av batching rekommendationerna som beskrivs i det här avsnittet:

* Använd buffring och batchbearbetning för att öka prestanda och skalbarhet SQL-databas.
* Förstå kompromisser mellan batchbearbetning/buffring och återhämtning. Vid fel, roll, kan risken för att förlora en obearbetat batch affärskritiska data uppväger prestandafördelarna batchbearbetning.
* Försök att hålla alla anrop till databasen i ett enda datacenter att minska svarstiden.
* Om du väljer en enda batching teknik erbjuder tabellvärdeparametrar bästa prestanda och flexibilitet.
* Följer dessa allmänna riktlinjer för bästa prestanda för insert, men testa ditt scenario:
  * Använd ett enda parametrar INSERT-kommando för < 100 rader.
  * Använda tabellvärdeparametrar för < 1000 rader.
  * För > = 1000 rader, Använd SqlBulkCopy körs.
* För update och delete-åtgärder kan använda tabellvärdeparametrar med lagrade proceduren logik som anger rätt igen på varje rad i tabellen-parametern.
* Riktlinjer för batch-storlek:
  * Använd de största batch-storlekar som passar ditt program och affärskrav.
  * Balansera prestandafördelar med stora batchar med risk för tillfälliga eller katastrofalt fel. Vad är en följd av återförsök eller förlust av data i gruppen? 
  * Testa den största batchstorleken för att verifiera att SQL-databasen inte avvisa den.
  * Skapa inställningar som kontrollen batchbearbetning, till exempel batchstorleken eller tidsfönstret buffring. Dessa inställningar ger flexibilitet. Du kan ändra batching beteendet i produktionsmiljö utan att omdistribuera Molntjänsten.
* Undvik parallell körning av batchar som fungerar på en enda tabell i en databas. Om du väljer att dela upp en enskild batch över flera trådar, kör du testerna för att fastställa bästa antal trådar. När du har ett okänt tröskelvärde fler trådar kommer försämra prestanda i stället öka den.
* Överväg att buffring på storleken och tid som ett sätt att implementera batchbearbetning för flera scenarier.

## <a name="next-steps"></a>Nästa steg
Den här artikeln fokuserar på hur databasdesign och kodning tekniker relaterade till batchbearbetning kan förbättra ditt programprestanda och skalbarhet. Men det är bara en faktor i din övergripande säkerhetsstrategi. Fler sätt att förbättra prestanda och skalbarhet finns [Azure SQL Database-prestandaråd för enskilda databaser](sql-database-performance-guidance.md) och [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool-guidance.md).

