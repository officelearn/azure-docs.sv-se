---
title: "Åtgärda ett fel vid anslutning till SQL, ett tillfälligt fel | Microsoft Docs"
description: "Lär dig hur du felsöker, diagnostisera och förhindra att ett fel vid anslutning till SQL eller ett tillfälligt fel i Azure SQL Database. "
keywords: "SQL-anslutning, anslutningssträngen, problem med nätverksanslutningen, tillfälligt fel, anslutningsfel"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: daleche
ms.openlocfilehash: 0d2d187ca55ca6e7723139423b4b28783f256704
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Felsöka, diagnostisera och förhindra SQL-anslutningsfel och tillfälliga fel för SQL Database
Den här artikeln beskriver hur du förhindra, Felsök, diagnostisera och minska anslutningsfel och tillfälliga fel som ditt klientprogram påträffar när det interagerar med Azure SQL Database. Lär dig hur du konfigurerar logik för omprövning, anslutningssträngen och justera andra anslutningsinställningar för.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tillfälliga fel tillfälligt
Ett tillfälligt fel - också tillfälliga fel – har en underliggande orsaken som kommer snart att lösas automatiskt. En tillfällig tillfälliga fel beror på när systemets Azure snabbt hamnar maskinvaruresurser bättre belastningsutjämning olika arbetsbelastningar. De flesta av dessa händelser för omkonfiguration Slutför ofta i mindre än 60 sekunder. Du kan ha problem med nätverksanslutningen till Azure SQL Database under det angivna tidsintervallet omkonfiguration. Program som ansluter till Azure SQL Database ska byggas räknar dessa tillfälliga fel, hantera dem genom att implementera logik i koden i stället för att visa dem till användare som programfel.

Om klientprogrammet använder ADO.NET, programmet meddelade om tillfälligt fel av throw av en **SqlException**. Den **nummer** egenskapen kan jämföras med listan över tillfälliga fel längst upp i avsnittet: [SQL-felkoder för SQL Database-klientprogram](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Anslutningen jämfört med kommandot
Du kommer försök SQL-anslutning eller upprätta igen, beroende på följande:

* **Ett tillfälligt fel inträffar under ett anslutningsförsök**: anslutningen ska göras efter att förskjuta för några sekunder.
* **Ett tillfälligt fel inträffar under en SQL-kommandot query**: kommandot bör inte göras omedelbart. I stället efter en fördröjning i anslutningen bör nyligen upprättas. Sedan kan kommandot göras.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Logik för omprövning av tillfälliga fel
Klientprogram som kan uppstår ett tillfälligt fel är mer robusta när de innehåller logik för omprövning.

När ditt program kommunicerar med Azure SQL Database via en 3 part mellanprogram, fråga med leverantören om mellanprogram innehåller logik för omprövning av tillfälliga fel.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principer för nya försök
* Ett försök att öppna en anslutning ska göras om felet är tillfälligt.
* En SQL SELECT-instruktion som misslyckas med ett tillfälligt fel bör inte göras direkt.
  
  * I stället skapa en ny anslutning och försök sedan Välj.
* När en UPDATE SQL-instruktion misslyckas med ett tillfälligt fel, ska en ny anslutning upprättas innan UPPDATERINGEN igen.
  
  * Logik för omprövning måste se till att hela Databastransaktionen slutförts, eller som hela transaktionen återställs.

#### <a name="other-considerations-for-retry"></a>Andra överväganden för nya försök
* En kommandofil som startas automatiskt efter arbetstid och som kommer att slutföras innan morgon har råd att mycket tålamod med lång tidsintervall mellan dess nya försök.
* Ett gränssnitt användarprogram bör ta hänsyn till mänsklig tendens att avstå från efter för lång väntan.
  
  * Lösningen måste dock inte att försöka några sekunders eftersom principen kan översvämma system med begäranden.

#### <a name="interval-increase-between-retries"></a>Öka intervall mellan försök
Vi rekommenderar att du fördröjning för 5 sekunder innan din första försök. Försöker igen efter en kortare än 5 sekunder risker överbelasta Molntjänsten fördröjning. För varje efterföljande försök fördröjningen ska växa exponentiellt, upp till högst 60 sekunder.

En beskrivning av den *blockerande period* för klienter som använder ADO.NET finns i [SQL Server anslutning poolning (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Du kanske också vill ange ett maximalt antal försök innan programmet avslutar själva.

#### <a name="code-samples-with-retry-logic"></a>Kodexempel med logik
Kodexempel med logik för omprövning, på en mängd olika programmeringsspråk, finns på:

* [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testa din logik
Om du vill testa logik för omprövning, måste du simulera eller orsaka fel än kan korrigeras medan programmet körs.

##### <a name="test-by-disconnecting-from-the-network"></a>Testa genom frånkoppling från nätverket
Ett sätt som du kan testa din logik är att koppla från din klientdator från nätverket medan programmet körs. Felet är:

* **SqlException.Number** = 11001
* Meddelandet: ”värden är inte känd”

Som en del av det första nytt försöket, programmet korrigera felstavningen och försök sedan ansluta.

För att göra detta praktiska kan du koppla från datorn från nätverket innan du startar programmet. Sedan identifierar programmet en körning-parameter som innebär att:

1. Lägg till 11001 tillfälligt till i listan med fel ska vara tillfälligt.
2. Försök den första anslutningen som vanligt.
3. Ta bort 11001 i listan efter fel har inträffat.
4. Visa ett meddelande som uppmanar användaren att ansluta datorn till nätverket.
   * Pausa körning med hjälp av antingen den **Console.ReadLine** metod eller en dialogruta med en OK-knapp. Användaren trycker på RETUR när datorn är ansluten till nätverket.
5. Försök igen att ansluta, förväntas lyckades.

##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testa genom skriver namnet på databasen vid anslutning
Programmet kan ändamålsenligt fel användarnamn innan det första anslutningsförsöket. Felet är:

* **SqlException.Number** = 18456
* Meddelandet: ”inloggning misslyckades för användaren 'WRONG_MyUserName'”.

Som en del av det första nytt försöket, programmet korrigera felstavningen och försök sedan ansluta.

För att göra det praktiska kände programmet igen en körning-parameter som innebär att:

1. Lägg till 18456 tillfälligt till i listan med fel ska vara tillfälligt.
2. Lägg till 'WRONG_' ändamålsenligt i användarnamnet.
3. Ta bort 18456 i listan efter fel har inträffat.
4. Ta bort 'WRONG_' från användarnamnet.
5. Försök igen att ansluta, förväntas lyckades.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection parametrar för anslutningen försök igen
Om klientprogrammet ansluter till Azure SQL Database med hjälp av .NET Framework-klassen **System.Data.SqlClient.SqlConnection**, bör du använda .NET 4.6.1 eller senare (eller .NET Core) så att du kan utnyttja funktionen anslutning försök igen. Information om funktionen [här](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


När du skapar den [anslutningssträngen](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) för din **SqlConnection** objekt, bör du samordna värdena bland följande parametrar:

* ConnectRetryCount &nbsp; &nbsp; *(standardvärdet är 1. Intervallet är 0 och 255.)*
* ConnectRetryInterval &nbsp; &nbsp; *(standard är 1 sekund. Intervall är 1 till 60).*
* Timeout för anslutningen &nbsp; &nbsp; *(standardvärdet är 15 sekunder. Intervallet är 0 och 2147483647)*

Mer specifikt bör valda värdena Se följande likheten SANT:

* Timeout för anslutningen = ConnectRetryCount * ConnectionRetryInterval

Till exempel om antalet = 3 och intervall = 10 sekunder timeout för bara 29 sekunder inte skulle helt ge systemet tillräckligt med tid för dess 3 och slutgiltig försök vid anslutning: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Anslutningen jämfört med kommandot
Den **ConnectRetryCount** och **ConnectRetryInterval** -parametrar kan din **SqlConnection** objekt försök ansluta utan uppmanar eller stör din program, till exempel kontrollen återgår till programmet. Återförsöken kan inträffa i följande situationer:

* mySqlConnection.Open metodanrop
* mySqlConnection.Execute metodanrop

Det finns en liten finess. Om ett tillfälligt fel uppstår när din *frågan* körs din **SqlConnection** objekt inte försök ansluta igen och den verkligen inte köra frågan igen. Dock **SqlConnection** snabbt kontrollerar anslutningen innan du skickar frågan för körning. Om snabb kontrollen identifierar ett anslutningsproblem **SqlConnection** försöker ansluta igen. Om det nya försöket lyckas, skickas frågan för du för körning.

#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount kombineras med logik för omprövning av programmet?
Anta att programmet har robust anpassade logik. Det kan och försök ansluta igen 4 gånger. Om du lägger till **ConnectRetryInterval** och **ConnectRetryCount** = 3 till anslutningssträngen, ökar antalet försök att 4 * 3 = 12 återförsök. Du kan inte avser sådana ett stort antal återförsök.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Anslutningar till Azure SQL-databas
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Anslutningen: Anslutningssträng
Anslutningssträngen som är nödvändiga för att ansluta till Azure SQL Database är skiljer sig från strängen för att ansluta till Microsoft SQL Server. Du kan kopiera anslutningssträngen för databasen från den [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Anslutning: IP-adress för
Du måste konfigurera SQL Database-server för att acceptera kommunikation från IP-adress på den dator som är värd för dina klientprogram. Du kan göra detta genom att redigera brandväggsinställningar via den [Azure-portalen](https://portal.azure.com/).

Om du glömmer att konfigurera IP-adressen misslyckas programmet med en praktisk felmeddelande nödvändiga IP-adressen.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Mer information finns: [så här: konfigurera brandväggsinställningar på SQL-databas](sql-database-configure-firewall-settings.md)

<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Anslutning: portar
Vanligtvis behöver du bara kontrollera att port 1433 är öppen för utgående kommunikation på datorn som är värd-klientprogrammet.

Till exempel när klientprogrammet finns på en Windows-dator, kan Windows-brandväggen på värden du öppna port 1433:

1. Öppna Kontrollpanelen
2. &gt;Alla objekt på Kontrollpanelen
3. &gt;Windows-brandväggen
4. &gt;Avancerade inställningar
5. &gt;Utgående regler
6. &gt;Åtgärder
7. &gt;Ny regel

Om klientprogrammet finns på en Azure-dator (VM), bör du läsa:<br/>[Portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Mer bakgrundsinformation om cofiguration portar och IP-adress, se: [Azure SQL Database-brandvägg](sql-database-firewall-configure.md)

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Anslutning: ADO.NET 4.6.1
Om programmet använder ADO.NET-klasser som **System.Data.SqlClient.SqlConnection** för att ansluta till Azure SQL Database, rekommenderar vi att du använder .NET Framework version 4.6.1 eller senare.

ADO.NET 4.6.1:

* För Azure SQL-databas finns förbättrad tillförlitlighet när du öppnar en anslutning med hjälp av den **SqlConnection.Open** metod. Den **öppna** metoden omfattar nu bästa prestanda försök mekanismer som svar på tillfälliga problem för vissa fel inom tidsgränsen för anslutningen.
* Stöder anslutningspooler. Detta inkluderar en effektiv kontroll av att det anslutningsobjekt som den ger programmet fungerar.

När du använder ett anslutningsobjekt från en anslutningspool, rekommenderar vi att programmet tillfälligt stänga anslutningen när du använder den inte omedelbart. Öppna en anslutning är inte dyr sättet att skapa en ny anslutning.

Om du använder ADO.NET 4.0 eller tidigare, rekommenderar vi att du uppgraderar till den senaste ADO.NET.

* Från och med November 2015, kan du [hämta ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostik
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostik: Testa om verktyg kan ansluta
Om programmet inte kan ansluta till Azure SQL Database, kan diagnostiska du prova att ansluta till ett program. Verktyget skulle helst ansluta genom att använda samma bibliotek som används i programmet.

Du kan försöka verktygen på alla Windows-datorer:

* SQL Server Management Studio (ssms.exe) som ansluter med hjälp av ADO.NET.
* SQLCMD.exe som ansluter med hjälp av [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).

När du är ansluten, testa om ett kort SQL SELECT-frågan fungerar.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostik: Kontrollera öppna portar
Anta att du misstänker att anslutningsförsök misslyckas på grund av problem med port. Du kan köra ett verktyg som rapporter om portkonfigurationerna som på datorn.

Följande verktyg kan vara användbart i Linux:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * (Ändra exempelvärde ska vara din IP-adress).

I Windows på [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) verktyg kan vara till hjälp. Här är ett exempel körning som efterfrågas situationen port på en Azure SQL Database-server och som kördes på en bärbar dator:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostik: Logga din fel
Ett tillfälligt problem diagnostiserats ibland bäst av identifiering av ett allmänt mönster över dagar eller veckor.

Klienten kan hjälpa en diagnos genom att logga alla fel som påträffas. Du kanske kan korrelera loggposterna med Feldata Azure SQL Database loggar själva internt.

Enterprise-biblioteket 6 (EntLib60) ger hanterad klasser för att hjälpa till med loggning:

* [5 - lika enkelt som att falla en logg: använda loggning programmet Block](http://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostik: Granska systemloggar för fel
Här följer några Transact-SQL SELECT-satser som frågeloggar av fel och annan information.

| Frågan för logg | Beskrivning |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Den [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) vyn innehåller information om hur enskilda händelser, inklusive några som kan orsaka tillfälliga fel eller anslutningsfel.<br/><br/>Förhoppningsvis kan du jämföra den **starttid** eller **end_time** värden med information om när dina klientprogram stötte på problem.<br/><br/>**Tips:** måste du ansluta till den **master** databasen för att köra det här. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Den [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) visa erbjuder aggregerade antal händelsetyper för ytterligare diagnostik.<br/><br/>**Tips:** måste du ansluta till den **master** databasen för att köra det här. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostik: Sök efter problem händelser i loggen för SQL-databas
Du kan söka efter poster om problemet händelser i loggen för Azure SQL Database. Försök följande Transact-SQL SELECT-uttryck i den **master** databasen:

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Några returnerade rader från sys.fn_xe_telemetry_blob_target_read_file
Nästa är en returnerad rad kan se ut. Null-värden visas är ofta inte null i andra rader.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise-biblioteket 6 (EntLib60) är ett ramverk för .NET-klasser som hjälper dig att implementera robust klienter för molntjänster, varav en är Azure SQL Database-tjänsten. Du kan hitta avsnitt som är dedikerad till varje område där EntLib60 kan hjälpa genom att besöka första:

* [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

Logik för att hantera tillfälliga fel är en plats där EntLib60 kan hjälpa.

* [4 - perseverance, hemligheten för alla framgångar: använder tillfälligt fel hantering programmet Block](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> Källkoden för EntLib60 är tillgängligt för allmänheten [hämta](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft har inga planer på att göra ytterligare funktionsuppdateringar eller uppdateringar för underhåll EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasser för tillfälliga fel och försök igen
Följande EntLib60 klasser är särskilt användbara för logik. Alla dessa, eller ytterligare under namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

* **RetryPolicy** klass
  
  * **ExecuteAction** metod
* **ExponentialBackoff** klass
* **SqlDatabaseTransientErrorDetectionStrategy** klass
* **ReliableSqlConnection** klass
  
  * **ExecuteCommand** metod

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** klass
* **NeverTransientErrorDetectionStrategy** klass

Här är länkar till information om EntLib60:

* Ledigt [bok Download: Utvecklarhandbok Microsoft Enterprise-biblioteket, 2 Edition](http://www.microsoft.com/download/details.aspx?id=41145)
* Bästa praxis: [försök allmänna riktlinjer](../best-practices-retry-general.md) har en utmärkt detaljerad beskrivning av logik.
* Hämta NuGet [Enterprise Library - hantering av tillfälliga fel program block 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Loggning block
* Loggning blocket är en mycket flexibelt och konfigurerbara lösning som hjälper dig att:
  
  * Skapa och lagra meddelanden i en mängd olika platser.
  * Kategorisera och filtrera meddelanden.
  * Samla in relevant information som är användbart för felsökning och spårning och för granskning och allmänna loggning krav.
* Loggning blocket avlägsnar loggningsfunktionen från loggmålet så att programkoden är konsekvent, oavsett plats och typ för arkivet mål loggning.

Mer information finns: [5 – som enkelt som som omfattas av av en logg: med blocket loggning program](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Källkoden för EntLib60 IsTransient metod
Nästa från den **SqlDatabaseTransientErrorDetectionStrategy** klassen, är den C#-källkoden för den **IsTransient** metod. Källkoden klargör vilka fel ansågs vara tillfälligt och worthy på försök igen, från och med April 2013.

Ett stort antal **//comment** rader har tagits bort från den här kopian att betona läsbarhet.

```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Nästa steg
* Felsöka andra vanliga problem med Azure SQL Database anslutningen finns [felsökning av problem med anslutningen till Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
* [SQL Server anslutningspoolen (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)
* [*Du försöker* är ett Apache 2.0 licensierad allmänna du försöker biblioteket som skrivits i **Python**, för att förenkla uppgiften att lägga till försök beteende till bara om något.](https://pypi.python.org/pypi/retrying)

