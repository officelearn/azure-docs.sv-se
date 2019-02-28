---
title: Arbeta med tillfälliga fel – Azure SQL Database | Microsoft Docs
description: Lär dig mer om att felsöka, diagnostisera och förhindra att en SQL-anslutningsfel eller tillfälligt fel i Azure SQL Database.
keywords: SQL-anslutning, anslutningssträngen, problem med nätverksanslutningen, tillfälligt fel, anslutningsfel
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 11/14/2018
ms.openlocfilehash: 1a13f5f038afed914fd2b2a178974a0ba2e7255f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984228"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>Arbeta med anslutningsproblem för SQL-databas och tillfälliga fel

Den här artikeln beskriver hur du förhindrar, felsöka, diagnostisera och lösa anslutningsfel och tillfälliga fel som klientprogrammet som påträffar när det interagerar med Azure SQL Database. Lär dig hur du konfigurerar logik för omprövning, bygga anslutningssträngen och justera andra anslutningsinställningar.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tillfälligt fel (tillfälliga fel)

Ett tillfälligt fel, även kallat ett tillfälligt fel, har en underliggande orsak som snart löser sig själv. En enstaka tillfälliga fel beror på när Azure-systemet snabbt vi maskinvaruresurser på bättre belastningsutjämning olika arbetsbelastningar. De flesta av dessa händelser för omkonfiguration Slutför på mindre än 60 sekunder. Du kan ha problem med nätverksanslutningen till SQL-databas under det angivna tidsintervallet omkonfiguration. Program som ansluter till SQL-databas ska skapas för att förvänta sådana tillfälliga fel. Om du vill hantera dem omprövningslogik i koden i stället för att visa dem till användare som programfel.

Om klientprogrammet använder ADO.NET, ditt program är ombedd om tillfälliga fel throw av **SqlException**. Jämför den **nummer** egenskap mot listan över tillfälliga fel som hittas i den övre delen av artikeln [SQL-felkoder för SQL Database-klientprogram](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Anslutningen jämfört med kommandot

SQL-anslutningen försöker ansluta igen eller upprätta igen, beroende på följande:

- **Ett tillfälligt fel uppstår under ett anslutningsförsök**

Försöka ansluta igen efter en fördröjning på några sekunder.

- **Ett tillfälligt fel uppstår under ett kommando för SQL-fråga**

Försök inte kommandot omedelbart. I stället efter en stund nyligen upprätta anslutningen. Försök sedan igen.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logik för omprövning av tillfälliga fel

Klientprogram som ibland stöta på ett tillfälligt fel är mer robust när de innehåller logik för omprövning. När ditt program kommunicerar med SQL-databas via tredje parts mellanprogram kan du be leverantören om mellanprogrammet innehåller logik för omprövning av tillfälliga fel.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principer för återförsök

- Om felet är tillfälligt, försök för att öppna en anslutning.
- Försök inte direkt att göra en SQL `SELECT` instruktionen som misslyckades med ett tillfälligt fel. I stället upprätta en ny anslutning och försök sedan den `SELECT`.
- När en SQL `UPDATE` instruktionen misslyckas med ett tillfälligt fel, upprätta en ny anslutning innan du försöker igen UPPDATERINGEN. Logik för omprövning måste se till att hela databasen-transaktionen är klar eller att hela transaktionen återställs.

### <a name="other-considerations-for-retry"></a>Annat att tänka på för återförsök

- Ett batch-program som automatiskt startar efter arbetstid och är klar innan morgon har råd att vara mycket patientens med länge tidsintervall mellan dess nya försök.
- En Användargränssnittsprogrammet bör hänsyn till mänskliga tendens att ge efter för lång väntan. Lösningen måste inte gör några sekunders eftersom principen kan översvämma systemet med begäranden.

### <a name="interval-increase-between-retries"></a>Öka intervall mellan försök

Vi rekommenderar att du väntar fem sekunder innan din första återförsöket. Försöker igen efter en fördröjning som är kortare än 5 sekunder risker överväldigande Molntjänsten. För varje efterföljande återförsök fördröjningen ska växa exponentiellt, upp till högst 60 sekunder.

En beskrivning av den blockera perioden för klienter som använder ADO.NET finns [(ADO.NET) för SQL Server-anslutningspoolen](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Du kanske också vill ange ett högsta antal försök innan programmet själv avslutas.

### <a name="code-samples-with-retry-logic"></a>Kodexempel med logik för omprövning

Kodexempel med logik för omprövning finns på:

- [Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Ansluta elastiskt till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testa omprövningslogiken

Om du vill testa omprövningslogiken, måste du simulera eller orsakar ett fel som kan åtgärdas medan programmet körs.

#### <a name="test-by-disconnecting-from-the-network"></a>Testa genom att koppla bort från nätverket

Ett sätt som du kan testa omprövningslogiken är att koppla från din klientdator från nätverket när programmet körs. Felet är:

- **SqlException.Number** = 11001
- Meddelande: ”Ingen sådan värd kallas”

Som en del av det första nytt försöket kan programmet korrigera att namnet är felstavat och försök sedan att ansluta.

Om du vill göra det här testet praktiskt att koppla bort datorn från nätverket innan du startar programmet. Sedan identifierar programmet en runtime-parameter som innebär att:

- Lägg till 11001 tillfälligt i dess lista med fel att tänka på som tillfälligt.
- Försök att dess första anslutningen som vanligt.
- När felet har fastnat, kan du ta bort 11001 i listan.
- Visa ett meddelande som talar om för användaren att ansluta datorn till nätverket.
- Pausa körning genom att använda antingen den **Console.ReadLine** metod eller en dialogruta med en OK-knapp. Användaren trycker på RETUR-tangenten när datorn är ansluten till nätverket.
- Försök igen att ansluta, förväntas lyckades.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testa genom skriver namnet på databasen när du ansluter

Ditt program kan avsiktligen har fel användarnamn innan det första anslutningsförsöket. Felet är:

- **SqlException.Number** = 18456
- Meddelande: ”Inloggningen misslyckades för användaren” WRONG_MyUserName ””.

Som en del av det första nytt försöket kan programmet korrigera att namnet är felstavat och försök sedan att ansluta.

Om du vill göra det här testet praktiska identifierar programmet en runtime-parameter som innebär att:

- Lägg till 18456 tillfälligt i dess lista med fel att tänka på som tillfälligt.
- Lägg till 'WRONG_' ändamålsenligt i användarnamnet.
- När felet har fastnat, kan du ta bort 18456 i listan.
- Ta bort 'WRONG_' från användarnamnet.
- Försök igen att ansluta, förväntas lyckades.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection parametrar för återförsök för anslutning

Om klientprogrammet ansluter till SQL-databas med hjälp av .NET Framework-klassen **System.Data.SqlClient.SqlConnection**, använda .NET 4.6.1 eller senare (eller .NET Core) så att du kan använda funktionen för återförsök sin anslutning. Mer information om funktionen finns i [den här webbsidan](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

När du skapar den [anslutningssträngen](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) för din **SqlConnection** objekt, samordna värdena bland följande parametrar:

- **ConnectRetryCount**:&nbsp;&nbsp;standardvärdet är 1. Intervallet är 0 till 255.
- **ConnectRetryInterval**:&nbsp;&nbsp;standard är 1 sekund. Intervallet är 1 till 60.
- **Tidsgräns för anslutning**:&nbsp;&nbsp;standardvärdet är 15 sekunder. Intervallet är 0 och 2147483647.

Mer specifikt bör dina valda värden Se följande likhet true: Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Till exempel om antalet är lika med 3 och intervallet är lika med 10 sekunder, ge en tidsgräns på endast 29 sekunder inte systemet tillräckligt länge så att dess tredje och sista försök att ansluta: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Anslutningen jämfört med kommandot

Den **ConnectRetryCount** och **ConnectRetryInterval** -parametrar kan din **SqlConnection** objekt försök ansluta igen utan om eller stör din program, till exempel returnera kontroll till ditt program. Nya försök kan inträffa i följande situationer:

- mySqlConnection.Open metodanrop
- mySqlConnection.Execute metodanrop

Det finns en liten finess. Om ett tillfälligt fel uppstår när ditt *fråga* körs, din **SqlConnection** objekt inte försök ansluta igen. Det däremot inte köra frågan igen. Dock **SqlConnection** snabbt kontrollerar anslutningen innan du skickar din fråga för körning. Om snabb kontrollen identifierar ett anslutningsproblem **SqlConnection** försöker ansluta igen. Om åtgärden lyckas, skickas frågan för körning.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Bör ConnectRetryCount kombineras med logik för omprövning av program

Anta att ditt program har robust anpassad logik. Det kan försök ansluta igen fyra gånger. Om du lägger till **ConnectRetryInterval** och **ConnectRetryCount** = 3 till anslutningssträngen, ökar antalet återförsök och 4 * 3 = 12 återförsök. Du kan inte avser sådana ett stort antal återförsök.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Anslutningar till SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Anslutning: Anslutningssträng

Anslutningssträngen som krävs för att ansluta till SQL Database är något annorlunda från den sträng som används för att ansluta till SQL Server. Du kan kopiera anslutningssträngen för databasen från den [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Anslutning: IP-adress

Du måste konfigurera SQL Database-server för att godkänna kommunikation från IP-adressen på den dator som är värd för klientprogrammet. Om du vill konfigurera den här konfigurationen, redigera brandväggsinställningarna via den [Azure-portalen](https://portal.azure.com/).

Om du glömmer att konfigurera IP-adressen, misslyckas ditt program med en praktisk felmeddelande som säger nödvändiga IP-adressen.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Mer information finns i [konfigurera brandväggsinställningar på SQL Database](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Anslutning: Portar

Vanligtvis måste du se till att endast port 1433 är öppen för utgående kommunikation på den dator som är värd för klientprogrammet.

Till exempel när klientprogrammet finns på en Windows-dator, kan du använda Windows-brandväggen på värden för att öppna port 1433.

1. Öppna Kontrollpanelen.
2. Välj **alla styra objekt på Kontrollpanelen** > **Windows-brandväggen** > **avancerade inställningar** > **utgående regler**   >  **Åtgärder** > **ny regel**.

Om klientprogrammet finns på en Azure-dator (VM), läsa [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Mer bakgrundsinformation om konfigurationen av portar och IP-adresser, finns i [Azure SQL Database-brandvägg](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Anslutning: ADO.NET 4.6.2 eller senare

Om ditt program använder ADO.NET-klasser som **System.Data.SqlClient.SqlConnection** för att ansluta till SQL-databas, rekommenderar vi att du använder .NET Framework version 4.6.2 eller senare.

#### <a name="starting-with-adonet-462"></a>Från och med ADO.NET 4.6.2 eller senare

- Öppna anslutningsförsöket kan göras direkt för Azure SQL-databaser, vilket ger bättre prestanda för molnbaserade appar.

#### <a name="starting-with-adonet-461"></a>Från och med ADO.NET 4.6.1

- För SQL-databas, bättre tillförlitlighet när du öppnar en anslutning med hjälp av den **SqlConnection.Open** metod. Den **öppna** metoden nu införlivar mån återförsöksmekanismer som svar på tillfälliga fel för vissa fel inom tidsgränsen för anslutningen.
- Anslutningspooler stöds, som innehåller en effektiv kontroll som fungerar som anslutningsobjektet får ditt program.

När du använder ett anslutningsobjekt från en anslutningspool, rekommenderar vi att programmet tillfälligt stängs anslutningen när den inte är omedelbart används. Det är inte dyrt att öppna en anslutning, men det är att skapa en ny anslutning.

Om du använder ADO.NET 4.0 eller tidigare, rekommenderar vi att du uppgraderar till den senaste ADO.NET. Från och med augusti 2018 kan du [ladda ned ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/2018/04/30/announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostik

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostik: Testa om verktyg kan ansluta

Om ditt program inte kan ansluta till SQL Database, är en diagnostik alternativ att försöka ansluta med ett program. Vi rekommenderar att verktyget ansluter genom att använda samma bibliotek som används i programmet.

Du kan försöka verktygen på alla Windows-datorer:

- SQL Server Management Studio (ssms.exe), som ansluter med hjälp av ADO.NET
- `sqlcmd.exe`, som ansluter med hjälp av [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

När ditt program är ansluten, testa om ett kort SQL SELECT-frågan fungerar.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostik: Kontrollera öppna portar

Om du misstänker att anslutningsförsök misslyckas på grund av problem med port kan köra du ett verktyg på datorn som rapporterar om portkonfigurationer.

På Linux, kan det vara bra att ange följande verktyg:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Ändra exempelvärdet om du vill att din IP-adress.

På Windows, den [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) verktyg kan vara till hjälp. Här är en exempel-körning som efterfrågas situationen port på en SQL Database-server och som kördes på en bärbar dator:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostik: Logga din fel

Ett tillfälligt problem är ibland bäst diagnostiserats av identifiering av ett allmänt mönster över dagar eller veckor.

Klienten kan hjälpa en diagnos genom att logga alla fel som påträffas. Du kanske kan jämföra loggposterna med Feldata som SQL Database loggar sig internt.

Enterprise Library 6 (EntLib60) erbjuder hanterade .NET-klasser som hjälper till med loggning. Mer information finns i [5 – lika enkelt som att falla en logg: Använd loggning Application Block](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostik: Granska systemloggar efter fel

Här följer några Transact-SQL SELECT-instruktioner som fråga felloggar och annan information.

| Fråga loggen | Beskrivning |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Den [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) vyn innehåller information om hur enskilda händelser som innehåller vissa som kan orsaka tillfälliga fel eller anslutningsfel.<br/><br/>Vi rekommenderar du kan jämföra de **starttid** eller **end_time** värden med information om när klientprogrammet stötte på problem.<br/><br/>Du måste ansluta till den *master* databasen för att köra den här frågan. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Den [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) visa erbjuder aggregerade antalet händelsetyper för ytterligare diagnostik.<br/><br/>Du måste ansluta till den *master* databasen för att köra den här frågan. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostik: Sök efter problem händelser i loggen för SQL-databas

Du kan söka efter poster om problemet händelser i loggen för SQL-databas. Prova följande Transact-SQL SELECT-uttryck i den *master* databasen:

```sql
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

I följande exempel visas hur en returnerade rad kan se ut. Null-värden visas är ofta inte null i andra rader.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) är ett ramverk med .NET-klasser som hjälper dig att implementera robust klienter för cloud services, ett av dem är SQL Database-tjänsten. Du hittar information för varje område där EntLib60 kan hjälpa [Enterprise Library 6 – April 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logik för omprövning för hantering av tillfälliga fel är ett område som EntLib60 kan hjälpa dig. Mer information finns i [4 – Perseverance, hemligheten för alla framgångar: Använd Programblocket för hantering av tillfälliga fel](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Källkoden för EntLib60 är offentlig hämtas från den [Download Center](https://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft har inga planer på att göra ytterligare funktions- eller underhållsuppdateringar EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasser för tillfälliga fel och försök igen

Följande EntLib60 klasser är särskilt användbara för omprövning. Alla dessa klasser finns i eller under namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **RetryPolicy** klass
  - **ExecuteAction** metod
- **ExponentialBackoff** klass
- **SqlDatabaseTransientErrorDetectionStrategy** class
- **ReliableSqlConnection** klass
  - **ExecuteCommand** metod

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** klass
- **NeverTransientErrorDetectionStrategy** klass

Här följer några länkar till information om EntLib60:

- Kostnadsfria hämtning: [Utvecklarens Guide till Microsoft Enterprise Library, 2nd edition](https://www.microsoft.com/download/details.aspx?id=41145).
- Bästa praxis: [Allmänna riktlinjer för återförsök](../best-practices-retry-general.md) har en utmärkt detaljerad beskrivning av logik för omprövning.
- Ladda ned NuGet: [Enterprise Library - tillfälliga fel Handling Application Block 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Loggning-block

- Loggning block är en mycket flexibelt och kan konfigureras som du kan använda för att:
  - Skapa och lagra loggmeddelanden i en mängd olika platser.
  - Kategorisera och filtrera meddelanden.
  - Samla in sammanhangsinformation som är användbart för felsökning och spårning, samt för granskning och allmän loggning krav.
- Loggning blocket avlägsnar loggningsfunktionen från loggmålet så att programkoden är konsekvent, oavsett plats och typ av arkivet target loggning.

Mer information finns i [5 – lika enkelt som att falla en logg: Använd loggning Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Källkoden för EntLib60 IsTransient metod

Nästa från den **SqlDatabaseTransientErrorDetectionStrategy** klass, är källkoden för C# för den **IsTransient** metod. Källkoden visar vilka fel har betraktas som tillfälliga och värt för återförsök, från och med April 2013.

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

- Mer information om hur du felsöker andra vanliga anslutningsproblem för SQL Database finns i [felsöka problem med anslutningen till Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
- [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)
- [SQL Server-anslutningspoolen (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Ett nytt försök görs* är en Apache-2.0 licensierad allmänna försöker biblioteket som skrivits i Python](https://pypi.python.org/pypi/retrying) förenklar uppgiften att lägga till återförsöksbeteendet i vad som helst.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
