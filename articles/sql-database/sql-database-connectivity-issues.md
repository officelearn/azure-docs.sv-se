---
title: "Åtgärda ett fel vid anslutning till SQL, ett tillfälligt fel | Microsoft Docs"
description: "Lär dig hur du felsöker, diagnostisera och förhindra att ett fel vid anslutning till SQL eller ett tillfälligt fel i Azure SQL Database."
keywords: "SQL-anslutning, anslutningssträngen, problem med nätverksanslutningen, tillfälligt fel, anslutningsfel"
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 11/29/2017
ms.author: daleche
ms.openlocfilehash: f6b5f825d7f8111075fe37b5dc29d174928d913e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Felsöka, diagnostisera och förhindra SQL-anslutningsfel och tillfälliga fel för SQL Database
Den här artikeln beskriver hur du förhindra, Felsök, diagnostisera och minska anslutningsfel och tillfälliga fel som ditt klientprogram påträffar när det interagerar med Azure SQL Database. Lär dig hur du konfigurerar logik för omprövning, anslutningssträngen och justera andra anslutningsinställningar för.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tillfälliga fel tillfälligt
Ett tillfälligt fel, även kallat ett tillfälligt fel har en underliggande orsaken som snart löser sig själv. En tillfällig tillfälliga fel beror på när systemets Azure snabbt hamnar maskinvaruresurser bättre belastningsutjämning olika arbetsbelastningar. De flesta av dessa händelser för omkonfiguration Slutför i mindre än 60 sekunder. Du kan ha problem med nätverksanslutningen till SQL-databas under det angivna tidsintervallet omkonfiguration. Program som ansluter till SQL-databas ska byggas så att du förväntar dig dessa tillfälligt fel. Implementera logik för att hantera dem i koden i stället för att visa dem till användare som programfel.

Om klientprogrammet använder ADO.NET, programmet meddelade om tillfälligt fel av throw av **SqlException**. Jämför den **nummer** av egenskap mot en lista över tillfälliga fel som finns längst upp i artikeln [SQL-felkoder för SQL Database-klientprogram](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Anslutningen jämfört med kommandot
Gör om SQL-anslutning eller upprätta igen, beroende på följande:

* **Ett tillfälligt fel inträffar under ett anslutningsförsök**: gör anslutningen efter en fördröjning på några sekunder.
* **Ett tillfälligt fel inträffar under en SQL-kommandot query**: inte omedelbart försök igen. I stället efter en stund nyligen upprätta anslutningen. Försök sedan igen.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logik för omprövning av tillfälliga fel
Klientprogram som kan uppstår ett tillfälligt fel är mer robusta när de innehåller logik för omprövning.

När ditt program kommunicerar med SQL-databasen via tredje parts mellanprogram kan du be leverantören om mellanprogram innehåller logik för omprövning av tillfälliga fel.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principer för nya försök
* Om felet är tillfälligt, försök för att öppna en anslutning.
* Försök inte en SQL SELECT-uttryck som misslyckades med ett tillfälligt fel direkt.
  * I stället skapa en ny anslutning och försök sedan Välj.
* När en UPDATE SQL-instruktion misslyckas med ett tillfälligt fel upprätta en ny anslutning innan du försöker igen UPPDATERINGEN.
  * Logik för omprövning måste se till att hela databasen-transaktionen är klar eller att hela transaktionen återställs.

### <a name="other-considerations-for-retry"></a>Andra överväganden för nya försök
* En kommandofil som automatiskt startar efter arbetstid och är klar innan morgon har råd att vara mycket patient med lång tidsintervall mellan dess nya försök.
* Ett gränssnitt användarprogram bör ta hänsyn till mänsklig tendens att avstå från efter för lång väntan.
  * Lösningen måste inte gör några sekunders eftersom principen kan översvämma system med förfrågningar.

### <a name="interval-increase-between-retries"></a>Öka intervall mellan försök
Vi rekommenderar att du väntar fem sekunder innan din första försök igen. Försöker igen efter en kortare än 5 sekunder risker överbelasta Molntjänsten fördröjning. För varje efterföljande försök fördröjningen ska växa exponentiellt, upp till högst 60 sekunder.

En beskrivning av blockerande perioden för klienter som använder ADO.NET finns [(ADO.NET) för SQL Server-anslutningspoolen](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Du kanske också vill ange ett maximalt antal försök innan programmet avslutar själv.

### <a name="code-samples-with-retry-logic"></a>Kodexempel med logik
Kodexempel med logik finns på:

- [Resiliently att ansluta till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Resiliently att ansluta till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testa din logik
Om du vill testa logik för omprövning, måste du simulera eller orsakar ett fel som kan korrigeras medan programmet körs.

#### <a name="test-by-disconnecting-from-the-network"></a>Testa genom frånkoppling från nätverket
Ett sätt som du kan testa din logik är att koppla från din klientdator från nätverket medan programmet körs. Felet är:

* **SqlException.Number** = 11001
* Meddelandet: ”värden är inte känd”

Som en del av det första nytt försöket, programmet korrigera felstavningen och försök sedan ansluta.

Om du vill göra det här testet praktiska att koppla från datorn från nätverket innan du startar programmet. Sedan identifierar programmet en runtime-parameter som innebär att:

* Lägg till 11001 tillfälligt till i listan med fel ska vara tillfälligt.
* Försök den första anslutningen som vanligt.
* Ta bort 11001 i listan efter fel har inträffat.
* Visa ett meddelande som talar om för användaren att ansluta datorn till nätverket.
   * Pausa körning med hjälp av antingen den **Console.ReadLine** metod eller en dialogruta med en OK-knapp. Användaren trycker på RETUR när datorn är ansluten till nätverket.
* Försök igen att ansluta, förväntas lyckades.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testa genom skriver namnet på databasen vid anslutning
Programmet kan ändamålsenligt fel användarnamn innan det första anslutningsförsöket. Felet är:

* **SqlException.Number** = 18456
* Meddelandet: ”inloggning misslyckades för användaren 'WRONG_MyUserName'”.

Som en del av det första nytt försöket, programmet korrigera felstavningen och försök sedan ansluta.

Om du vill göra det här testet praktiska identifierar programmet en runtime-parameter som innebär att:

* Lägg till 18456 tillfälligt till i listan med fel ska vara tillfälligt.
* Lägg till 'WRONG_' ändamålsenligt i användarnamnet.
* Ta bort 18456 i listan efter fel har inträffat.
* Ta bort 'WRONG_' från användarnamnet.
* Försök igen att ansluta, förväntas lyckades.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection parametrar för anslutningen försök igen
Om klientprogrammet ansluter till SQL-databas med hjälp av .NET Framework-klassen **System.Data.SqlClient.SqlConnection**, använda .NET 4.6.1 eller senare (eller .NET Core) så att du kan använda funktionen anslutning försök igen. Mer information om funktionen finns [den här webbsidan](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

När du skapar den [anslutningssträngen](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) för din **SqlConnection** objekt, samordna värdena bland följande parametrar:

* **ConnectRetryCount**:&nbsp;&nbsp;standardvärdet är 1. Intervallet är 0 och 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;standardvärdet är 1 sekund. Intervallet är mellan 1 och 60.
* **Timeout för anslutningen**:&nbsp;&nbsp;standardvärdet är 15 sekunder. Intervallet är 0 och 2147483647.

Mer specifikt bör valda värdena Se följande likheten SANT:

Timeout för anslutningen = ConnectRetryCount * ConnectionRetryInterval

Till exempel om antalet är lika med 3 och intervallet är lika med 10 sekunder, en tidsgräns på endast 29 sekunder får inte systemet tillräckligt med tid för den tredje och sista försök att ansluta: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Anslutningen jämfört med kommandot
Den **ConnectRetryCount** och **ConnectRetryInterval** -parametrar kan din **SqlConnection** objekt försök ansluta utan uppmanar eller stör din program, till exempel kontrollen återgår till programmet. Återförsöken kan inträffa i följande situationer:

* mySqlConnection.Open metodanrop
* mySqlConnection.Execute metodanrop

Det finns en liten finess. Om ett tillfälligt fel uppstår när din *frågan* körs din **SqlConnection** objekt inte försök ansluta igen. Det har inte köra frågan igen. Dock **SqlConnection** snabbt kontrollerar anslutningen innan du skickar frågan för körning. Om snabb kontrollen identifierar ett anslutningsproblem **SqlConnection** försöker ansluta igen. Om det nya försöket lyckas, skickas frågan för körning.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount kombineras med logik för omprövning av programmet?
Anta att programmet har robust anpassade logik. Det kan försök connect fyra gånger. Om du lägger till **ConnectRetryInterval** och **ConnectRetryCount** = 3 till anslutningssträngen, ökar antalet försök att 4 * 3 = 12 återförsök. Du kan inte avser sådana ett stort antal återförsök.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Anslutningar till SQL-databas
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Anslutningen: Anslutningssträng
Den anslutningssträng som krävs för att ansluta till SQL-databas är skiljer sig från den sträng som används för att ansluta till SQL Server. Du kan kopiera anslutningssträngen för databasen från den [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Anslutning: IP-adress för
Du måste konfigurera SQL Database-server för att acceptera kommunikation från IP-adress på den dator som är värd för dina klientprogram. Om du vill konfigurera den här konfigurationen, redigera brandväggsinställningar via den [Azure-portalen](https://portal.azure.com/).

Om du glömmer att konfigurera IP-adressen misslyckas programmet en praktisk felmeddelande nödvändiga IP-adressen.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Mer information finns i [konfigurera brandväggsinställningar på SQL-databas](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Anslutning: portar
Vanligtvis måste du se till att endast port 1433 är öppen för utgående kommunikation på den dator som är värd för dina klientprogram.

Till exempel när klientprogrammet finns på en Windows-dator, kan du använda Windows-brandväggen på värden för öppna port 1433.

1. Öppna Kontrollpanelen.

2. Välj **alla styra objekt på Kontrollpanelen** > **Windows-brandväggen** > **avancerade inställningar** > **utgående regler**   >  **Åtgärder** > **ny regel**.

Om klientprogrammet finns på en Azure-dator (VM), kan du läsa [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Mer information om konfiguration av portarna och IP-adresser, se [Azure SQL Database-brandvägg](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Anslutning: ADO.NET 4.6.1
Om programmet använder ADO.NET-klasser som **System.Data.SqlClient.SqlConnection** för att ansluta till SQL-databas, rekommenderar vi att du använder .NET Framework version 4.6.1 eller senare.

ADO.NET 4.6.1:

* För SQL-databas ökad tillförlitlighet när du öppnar en anslutning med hjälp av den **SqlConnection.Open** metod. Den **öppna** metoden omfattar nu bästa försök mekanismer som svar på tillfälliga problem för vissa fel inom tidsgränsen för anslutningen.
* Anslutningspooler stöds, som innehåller en effektiv kontroll av att det anslutningsobjekt som den ger programmet fungerar.

När du använder ett anslutningsobjekt från en anslutningspool, rekommenderar vi att programmet tillfälligt stänga anslutningen när den inte används omedelbart. Det är inte dyra att öppna en anslutning, men det är att skapa en ny anslutning.

Om du använder ADO.NET 4.0 eller tidigare, rekommenderar vi att du uppgraderar till den senaste ADO.NET. Från och med November 2015, kan du [hämta ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostik
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostik: Testa om verktyg kan ansluta
Om programmet inte kan ansluta till SQL-databas, kan diagnostiska du prova att ansluta till ett program. Vi rekommenderar att verktyget ansluter genom att använda samma bibliotek som används i programmet.

Du kan försöka verktygen på alla Windows-datorer:

* SQL Server Management Studio (ssms.exe) som ansluter med hjälp av ADO.NET
* SQLCMD.exe som ansluter med hjälp av [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

När ditt program är anslutet testa om ett kort SQL SELECT-frågan fungerar.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostik: Kontrollera öppna portar
Om du misstänker att anslutningsförsök misslyckas på grund av problem med port, kan du köra ett verktyg på datorn som rapporterar för portkonfigurationer.

Följande verktyg kan vara användbart i Linux:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Ändra exempelvärdet så att din IP-adress.

I Windows, den [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) verktyg kan vara till hjälp. Här är ett exempel körning som efterfrågas situationen port på en SQL Database-server och som kördes på en bärbar dator:

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

Klienten kan hjälpa en diagnos genom att logga alla fel som påträffas. Du kanske kan korrelera loggposterna med Feldata som SQL-databasen loggar själva internt.

Enterprise-biblioteket 6 (EntLib60) erbjuder hanterad klasser för att hjälpa till med loggning. Mer information finns i [5 - lika enkelt som att falla en logg: använda loggning programmet blocket](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostik: Granska systemloggar för fel
Här följer några Transact-SQL SELECT-satser som fråga felloggarna och annan information.

| Frågan för logg | Beskrivning |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Den [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) vyn innehåller information om hur enskilda händelser som innehåller vissa som kan orsaka tillfälliga fel eller anslutningsfel.<br/><br/>Helst bör du kan jämföra den **starttid** eller **end_time** värden med information om när dina klientprogram stötte på problem.<br/><br/>Du måste ansluta till den *master* databasen för att köra den här frågan. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Den [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) visa erbjuder aggregerade antal händelsetyper för ytterligare diagnostik.<br/><br/>Du måste ansluta till den *master* databasen för att köra den här frågan. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostik: Sök efter problem händelser i loggen för SQL-databas
Du kan söka efter poster om problemet händelser i loggen för SQL-databas. Försök följande Transact-SQL SELECT-uttryck i den *master* databasen:

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
I följande exempel visas hur en returnerad rad kan se ut. Null-värden visas är ofta inte null i andra rader.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise-biblioteket 6 (EntLib60) är ett ramverk för .NET-klasser som hjälper dig att implementera robust klienter för molntjänster, varav en är SQL Database-tjänsten. Du hittar information för varje område där EntLib60 kan hjälpa [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logik för att hantera tillfälliga fel är ett område som EntLib60 kan hjälpa. Mer information finns i [4 - Perseverance, hemligheten för alla framgångar: använda tillfälliga fel hanterar programmet blocket](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Källkoden för EntLib60 är offentliga hämtas från den [Download Center](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft har inga planer på att göra ytterligare funktionsuppdateringar eller uppdateringar för underhåll EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasser för tillfälliga fel och försök igen
Följande EntLib60 klasser är särskilt användbara för logik. Dessa klasser finns i eller i namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** klass
  
  * **ExecuteAction** metod
* **ExponentialBackoff** klass
* **SqlDatabaseTransientErrorDetectionStrategy** klass
* **ReliableSqlConnection** klass
  
  * **ExecuteCommand** metod

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** klass
* **NeverTransientErrorDetectionStrategy** klass

Här följer några länkar till information om EntLib60:

* Kostnadsfri hämtning: [Utvecklarhandbok för Microsoft Enterprise-biblioteket, 2 edition](http://www.microsoft.com/download/details.aspx?id=41145).
* Bästa praxis: [försök allmänna riktlinjer](../best-practices-retry-general.md) har en utmärkt detaljerad beskrivning av logik.
* Hämta NuGet: [Enterprise Library - tillfälliga fel hanterar programmet Block 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Loggning block
* Loggning blocket är en lösning för mycket flexibelt och kan konfigureras som du kan använda för att:
  
  * Skapa och lagra meddelanden i en mängd olika platser.
  * Kategorisera och filtrera meddelanden.
  * Samla in relevant information som är användbart för felsökning och spårning och för granskning och allmänna loggning krav.
* Loggning blocket avlägsnar loggningsfunktionen från loggmålet så att programkoden är konsekvent, oavsett plats och typ för arkivet mål loggning.

Mer information finns i [5 - lika enkelt som att falla en logg: använda loggning programmet blocket](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Källkoden för EntLib60 IsTransient metod
Nästa från den **SqlDatabaseTransientErrorDetectionStrategy** klassen, är den C#-källkoden för den **IsTransient** metod. Källkoden klargör vilka fel ansågs tillfälligt och worthy på försök igen, från och med April 2013.

```csharp
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
* Mer information om felsökning av andra vanliga problem med anslutningen SQL-databas finns [felsökning av problem med anslutningen till Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
* [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)
* [SQL Server anslutningspoolen (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Du försöker* är ett Apache 2.0 licensierad allmänna du försöker biblioteket som skrivits i Python,](https://pypi.python.org/pypi/retrying) att förenkla uppgiften att lägga till försök beteende till bara om något.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

