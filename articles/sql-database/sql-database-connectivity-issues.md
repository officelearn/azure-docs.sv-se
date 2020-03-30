---
title: Arbeta med tillfälliga fel
description: Lär dig hur du felsöker, diagnostiserar och förhindrar ett SQL-anslutningsfel eller tillfälliga fel i Azure SQL Database.
keywords: sql-anslutning,anslutningssträng,anslutningsproblem,tillfälligt fel,anslutningsfel
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269086"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Felsöka tillfälliga anslutningsfel till SQL Database

I den här artikeln beskrivs hur du förhindrar, felsöker, diagnostiserar och minskar anslutningsfel och tillfälliga fel som klientprogrammet stöter på när det interagerar med Azure SQL Database. Lär dig hur du konfigurerar logik för återförsök, skapar anslutningssträngen och justerar andra anslutningsinställningar.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tillfälliga fel (tillfälliga fel)

Ett tillfälligt fel, även kallat transient fel, har en underliggande orsak som snart löser sig själv. En tillfällig orsak till tillfälliga fel är när Azure-systemet snabbt flyttar maskinvaruresurser till bättre belastningsbalans olika arbetsbelastningar. De flesta av dessa omkonfigurationshändelser avslutas på mindre än 60 sekunder. Under den här omkonfigurationstiden kan du ha anslutningsproblem till SQL Database. Program som ansluter till SQL Database bör byggas för att förvänta sig dessa tillfälliga fel. Om du vill hantera dem implementerar du logik för återförsök i koden i stället för att visa dem för användare som programfel.

Om klientprogrammet använder ADO.NET får programmet veta om det tillfälliga felet genom att **kasta SqlException**. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Kommandot Anslutning kontra

Försök igen sql-anslutningen eller upprätta den igen, beroende på följande:

- **Ett tillfälligt fel uppstår under ett anslutningsförsök**

Efter en fördröjning på flera sekunder försöker du ansluta igen.

- **Ett tillfälligt fel uppstår under ett SQL-frågekommando**

Försök inte omedelbart med kommandot igen. Istället, efter en fördröjning, nyligen upprätta anslutningen. Försök sedan igen kommandot.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logik för omprövning av tillfälliga fel

Klientprogram som ibland stöter på ett tillfälligt fel är mer robusta när de innehåller logik för återförsök. När programmet kommunicerar med SQL Database via mellanprogram från tredje part frågar du leverantören om mellanmaterialet innehåller logik för återförsök för tillfälliga fel.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principer för återförsök

- Om felet är tillfälligt försöker du öppna en anslutning igen.
- Försök inte direkt med `SELECT` ett SQL-uttryck som misslyckades med ett tillfälligt fel. Upprätta i stället en ny anslutning `SELECT`och försök sedan igen .
- När ett `UPDATE` SQL-uttryck misslyckas med ett tillfälligt fel upprättar du en ny anslutning innan du försöker igen uppdateringen. Logiken för återförsök måste säkerställa att antingen hela databastransaktionen har slutförts eller att hela transaktionen återställs.

### <a name="other-considerations-for-retry"></a>Andra överväganden för återförsök

- Ett batchprogram som automatiskt startar efter arbetstid och avslutas före morgonen har råd att vara mycket tålmodig med långa tidsintervall mellan sina försök att försöka igen.
- Ett användargränssnitt program bör stå för den mänskliga tendensen att ge upp efter alltför lång väntan. Lösningen får inte försöka igen med några sekunders mellanrum, eftersom den principen kan översvämma systemet med begäranden.

### <a name="interval-increase-between-retries"></a>Intervallökning mellan återförsök

Vi rekommenderar att du väntar i 5 sekunder innan du försöker igen. Försöker du igen efter en fördröjning som är kortare än 5 sekunder riskerar att överväldiga molntjänsten. För varje efterföljande återförsök bör fördröjningen växa exponentiellt, upp till högst 60 sekunder.

En diskussion om blockeringsperioden för klienter som använder ADO.NET finns i [SQL Server-anslutningspoolning (ADO.NET).](https://msdn.microsoft.com/library/8xx3tyca.aspx)

Du kanske också vill ange ett maximalt antal försök innan programmet avslutas.

### <a name="code-samples-with-retry-logic"></a>Kodexempel med logik för återförsök

Kodexempel med logik för återförsök finns på:

- [Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Ansluta elastiskt till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testa logiken för återförsök

Om du vill testa logiken för återförsök måste du simulera eller orsaka ett fel som kan korrigeras medan programmet fortfarande körs.

#### <a name="test-by-disconnecting-from-the-network"></a>Testa genom att koppla från nätverket

Ett sätt att testa logiken för återförsök är att koppla bort klientdatorn från nätverket medan programmet körs. Felet är:

- **SqlException.Number** = 11001
- Meddelande: "Ingen sådan värd är känd"

Som en del av det första försöket att försöka igen kan du återansluta klientdatorn till nätverket och sedan försöka ansluta.

Om du vill göra det här testet praktiskt kopplar du från datorn från nätverket innan du startar programmet. Sedan känner programmet igen en körningsparameter som gör att programmet:

- Lägg tillfälligt till 11001 i listan över fel som ska anse vara tillfälliga.
- Försök dess första anslutning som vanligt.
- När felet har fångats tar du bort 11001 från listan.
- Visa ett meddelande som talar om för användaren att ansluta datorn till nätverket.
- Pausa ytterligare körning med hjälp av metoden **Console.ReadLine** eller en dialogruta med knappen OK. Användaren trycker på Retur när datorn har anslutits till nätverket.
- Försök igen att ansluta, förväntar framgång.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testa genom att felstava databasnamnet vid anslutning

Ditt program kan avsiktligt felstava användarnamnet före det första anslutningsförsöket. Felet är:

- **SqlException.Number** = 18456
- Meddelande: "Inloggning misslyckades för användaren 'WRONG_MyUserName'."

Som en del av det första försöket att försöka igen kan programmet korrigera felstavningen och sedan försöka ansluta.

Om du vill göra det här testet praktiskt känner programmet igen en körningsparameter som gör att programmet:

- Lägg tillfälligt till 18456 i listan över fel som ska anse vara tillfälliga.
- Lägg avsiktligt till "WRONG_" i användarnamnet.
- När felet har fångats tar du bort 18456 från listan.
- Ta bort "WRONG_" från användarnamnet.
- Försök igen att ansluta, förväntar framgång.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection-parametrar för återförsök för anslutningar

Om klientprogrammet ansluter till SQL Database med klassen .NET Framework **System.Data.SqlClient.SqlConnection**använder du .NET 4.6.1 eller senare (eller .NET Core) så att du kan använda funktionen för återförsök för anslutning. Mer information om funktionen finns på den [här webbsidan](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

När du skapar [anslutningssträngen](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) för **SqlConnection-objektet** koordinerar du värdena mellan följande parametrar:

- **ConnectRetryCount:**&nbsp;&nbsp;Standard är 1. Räckvidden är 0 till 255.
- **ConnectRetryInterval**&nbsp;&nbsp;: Standard är 10 sekunder. Räckvidden är 1 till 60.
- **Timeout för anslutning:**&nbsp;&nbsp;Standardvärdet är 15 sekunder. Räckvidden är 0 till 2147483647.

Specifikt bör dina valda värden göra följande likhet sant: Anslutningstidsgräns = ConnectRetryCount * ConnectionRetryInterval

Om antalet till exempel är lika med 3 och intervallet är lika med 10 sekunder, ger en timeout på bara 29 sekunder inte systemet tillräckligt med tid för sitt tredje och sista försök att ansluta: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Kommandot Anslutning kontra

Med parametrarna **ConnectRetryCount** och **ConnectRetryInterval** kan **sqlconnection-objektet** försöka ansluta igen utan att tala om eller störa programmet, till exempel returnera kontrollen till ditt program. Återförsöken kan förekomma i följande situationer:

- mySqlConnection.Open-metodanrop
- mySqlConnection.Execute-metodanrop

Det finns en finess. Om ett tillfälligt fel inträffar medan *frågan* körs försöker **sqlConnection-objektet** inte ansluta igen. Det verkligen inte försöka din fråga. **SqlConnection** kontrollerar dock anslutningen mycket snabbt innan du skickar frågan för körning. Om snabbkontrollen upptäcker ett anslutningsproblem försöker **SqlConnection** ansluta. Om återförsöket lyckas skickas frågan för körning.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Bör ConnectRetryCount kombineras med logik för återförsök för program

Anta att ditt program har robust logik för att försöka igen. Det kan försöka ansluta igen fyra gånger. Om du lägger till **ConnectRetryInterval** och **ConnectRetryCount** =3 i anslutningssträngen ökar du antalet återförsök till 4 * 3 = 12 återförsök. Du kanske inte har för avsikt att göra så många försök.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Anslutningar till SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Anslutning: Anslutningssträng

Anslutningssträngen som krävs för att ansluta till SQL Database skiljer sig något från strängen som används för att ansluta till SQL Server. Du kan kopiera anslutningssträngen för databasen från [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Anslutning: IP-adress

Du måste konfigurera SQL Database-servern så att kommunikationen accepteras från IP-adressen för den dator som är värd för klientprogrammet. Om du vill konfigurera den här konfigurationen redigerar du brandväggsinställningarna via [Azure-portalen](https://portal.azure.com/).

Om du glömmer att konfigurera IP-adressen misslyckas programmet med ett praktiskt felmeddelande som anger den nödvändiga IP-adressen.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Mer information finns i [Konfigurera brandväggsinställningar på SQL Database](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Anslutning: Portar

Vanligtvis måste du se till att endast port 1433 är öppen för utgående kommunikation på den dator som är värd för klientprogrammet.

När klientprogrammet till exempel finns på en Windows-dator kan du använda Windows-brandväggen på värden för att öppna port 1433.

1. Öppna Kontrollpanelen.
2. Välj **alla objekt på Kontrollpanelen i** > **Windows-brandväggen** > Avancerade**inställningar** > **för utgående regler** > **Åtgärder Ny** > **regel**.

Om klientprogrammet finns på en virtuell Azure-dator (VM) läser du [Portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Bakgrundsinformation om konfiguration av portar och IP-adresser finns i [Azure SQL Database-brandväggen](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Anslutning: ADO.NET 4.6.2 eller senare

Om programmet använder ADO.NET klasser som **System.DataClient.SqlConnection** för att ansluta till SQL Database rekommenderar vi att du använder .NET Framework version 4.6.2 eller senare.

#### <a name="starting-with-adonet-462"></a>Från och med ADO.NET 4.6.2

- Anslutningen öppen försök att göras om omedelbart för Azure SQL-databaser, vilket förbättrar prestanda för molnaktiverade appar.

#### <a name="starting-with-adonet-461"></a>Från och med ADO.NET 4.6.1

- För SQL Database förbättras tillförlitligheten när du öppnar en anslutning med hjälp av metoden **SqlConnection.Open.** Metoden **Open** innehåller nu mekanismer för återförsök som svar på tillfälliga fel för vissa fel inom timeout-perioden för anslutning.
- Anslutningspoolning stöds, vilket inkluderar en effektiv verifiering av att anslutningsobjektet som det ger ditt program fungerar.

När du använder ett anslutningsobjekt från en anslutningspool rekommenderar vi att anslutningen tillfälligt stängs när den inte används omedelbart. Det är inte dyrt att öppna en anslutning igen, men det är att skapa en ny anslutning.

Om du använder ADO.NET 4.0 eller tidigare rekommenderar vi att du uppgraderar till den senaste ADO.NET. Från och med augusti 2018 kan du [ladda ner ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostik

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostik: Testa om verktyg kan ansluta

Om programmet inte kan ansluta till SQL Database är ett diagnostiskt alternativ att försöka ansluta till ett verktygsprogram. Helst ansluter verktyget med samma bibliotek som programmet använder.

På alla Windows-datorer kan du prova följande verktyg:

- SQL Server Management Studio (ssms.exe), som ansluter med hjälp av ADO.NET
- `sqlcmd.exe`, som ansluter med hjälp av [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

När programmet har anslutits testar du om en kort SQL SELECT-fråga fungerar.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostik: Kontrollera öppna portar

Om du misstänker att anslutningsförsök misslyckas på grund av portproblem kan du köra ett verktyg på datorn som rapporterar om portkonfigurationerna.

På Linux kan följande verktyg vara till hjälp:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Ändra exempelvärdet så att det blir din IP-adress.

I Windows kan verktyget [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) vara användbart. Här är ett exempel körning som ifrågasatte portsituationen på en SQL Database-server och som kördes på en bärbar dator:

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

### <a name="diagnostics-log-your-errors"></a>Diagnostik: Logga dina fel

Ett återkommande problem ibland bäst diagnostiseras genom detektion av ett allmänt mönster under dagar eller veckor.

Din klient kan hjälpa till med en diagnos genom att logga alla fel som den stöter på. Du kanske kan korrelera loggposterna med feldata som SQL Database loggar själv internt.

Enterprise Library 6 (EntLib60) erbjuder .NET-hanterade klasser för att hjälpa till med loggning. Mer information finns i [5 - Så enkelt som att falla av en logg: Använd loggningsprogramblocket](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostik: Undersök systemloggar för fel

Här är några Transact-SQL SELECT-satser som frågar felloggar och annan information.

| Fråga om logg | Beskrivning |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Vyn sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) innehåller information om enskilda händelser, vilket inkluderar vissa som kan orsaka tillfälliga fel eller anslutningsfel.<br/><br/>Helst kan du korrelera **start_time** eller **end_time** värden med information om när klientprogrammet upplevde problem.<br/><br/>Du måste ansluta till *huvuddatabasen* för att kunna köra den här frågan. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Vyn sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) erbjuder aggregerade antal händelsetyper för ytterligare diagnostik.<br/><br/>Du måste ansluta till *huvuddatabasen* för att kunna köra den här frågan. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostik: Sök efter problemhändelser i SQL Database-loggen

Du kan söka efter poster om problemhändelser i SQL Database-loggen. Prova följande Transact-SQL SELECT-uttryck i *huvuddatabasen:*

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

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Några returnerade rader från sys.fn_xe_telemetry_blob_target_read_file

I följande exempel visas hur en returnerad rad kan se ut. Null-värdena som visas är ofta inte null i andra rader.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Företagsbibliotek 6

Enterprise Library 6 (EntLib60) är ett ramverk för .NET-klasser som hjälper dig att implementera robusta klienter för molntjänster, varav en är SQL Database-tjänsten. Information om hur du hittar ämnen som är avsedda för varje område där EntLib60 kan hjälpa till finns i [Företagsbibliotek 6 - april 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Återförsökslogik för hantering av tillfälliga fel är ett område där EntLib60 kan hjälpa till. För mer information, se [4 - Uthållighet, hemlighet av alla triumfer: Använd Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Källkoden för EntLib60 är tillgänglig för offentlig nedladdning från [Download Center](https://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft har inga planer på att göra ytterligare funktionsuppdateringar eller underhållsuppdateringar till EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasser för tillfälliga fel och försök igen

Följande EntLib60-klasser är särskilt användbara för logik för återförsök. Alla dessa klasser finns i eller under namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **Klassen RetryPolicy**
  - **Metoden Köråtgärd**
- **Klassen ExponentialBackoff**
- **Klassen SqlDatabaseTransientErrorDetectionStrategy**
- **ReliableSqlConnection** klass
  - **Metoden ExecuteCommand**

I namnområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport:**

- **Klassen AlwaysTransientErrorDetectionStrategy**
- **Klassen NeverTransientErrorDetectionStrategy**

Här är några länkar till information om EntLib60:

- Gratis nedladdning av böcker: [Utvecklarguide till Microsoft Enterprise Library, 2:a utgåvan](https://www.microsoft.com/download/details.aspx?id=41145).
- Metodtips: [Återförsök allmän vägledning](../best-practices-retry-general.md) har en utmärkt djupgående diskussion om återförsök logik.
- NuGet nedladdning: [Enterprise Library - Transient Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Loggningsblocket

- Loggningsblocket är en mycket flexibel och konfigurerbar lösning som du kan använda för att:
  - Skapa och lagra loggmeddelanden på en mängd olika platser.
  - Kategorisera och filtrera meddelanden.
  - Samla in kontextuell information som är användbar för felsökning och spårning, samt för granskning och allmänna loggningskrav.
- Loggningsblocket abstraherar loggningsfunktionen från loggmålet så att programkoden är konsekvent, oavsett plats och typ av målloggningsarkivet.

Mer information finns i [5 - Så enkelt som att falla av en logg: Använd loggningsprogramblocket](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metod källkod

Från klassen **SqlDatabaseTransientErrorDetectionStrategy** är C#-källkoden för metoden **IsTransient.** Källkoden klargör vilka fel som ansågs vara övergående och värda ett nytt försök från och med april 2013.

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

- [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)
- [SQL Server-anslutningspoolning (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Försöker igen* är ett Apache 2.0-licensierat bibliotek för återförsök, skrivet i Python,](https://pypi.python.org/pypi/retrying) för att förenkla uppgiften att lägga till beteende för återförsök i nästan vad som helst.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
