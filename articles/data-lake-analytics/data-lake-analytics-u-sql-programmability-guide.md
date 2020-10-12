---
title: Guide för U-SQL-programmering för Azure Data Lake
description: Lär dig mer om den uppsättning tjänster i Azure Data Lake Analytics som gör att du kan skapa en molnbaserad stor data plattform.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 1c22aa9fb91b0a86704b95586afc1779023e85b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288941"
---
# <a name="u-sql-programmability-guide"></a>Programmerings guide för U-SQL

U-SQL är ett frågespråk som är utformat för stora data typer av arbets belastningar. En av de unika funktionerna i U-SQL är kombinationen av SQL-liknande deklarativ språk med utöknings barhet och programmerbar het som tillhandahålls av C#. I den här guiden fokuserar vi på utöknings barhet och programmerbar het för det U-SQL-språk som har Aktiver ATS av C#.

## <a name="requirements"></a>Krav

Hämta och installera [Azure Data Lake verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Kom igång med U-SQL  

Titta på följande U-SQL-skript:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Det här skriptet definierar två rad uppsättningar: `@a` och `@results` . Rad uppsättningen `@results` definieras från `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typer och-uttryck i U-SQL-skript

Ett U-SQL-uttryck är ett C#-uttryck kombinerat med logiska U-SQL-åtgärder `AND` , `OR` och `NOT` . U-SQL-uttryck kan användas med SELECT, EXTRACT, WHERE, HAVING, GROUP BY och DECLARE. Till exempel parsar följande skript en sträng som ett DateTime-värde.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Följande kodfragment parsar en sträng som DateTime-värde i en DECLARE-instruktion.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Använda C#-uttryck för data typ konverteringar

Följande exempel visar hur du kan göra en datetime-datakonvertering med hjälp av C#-uttryck. I det här specifika scenariot konverteras sträng DatumTid-data till standard-datetime med tids notation midnatt 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Använd C#-uttryck för dagens datum

För att hämta dagens datum kan vi använda följande C#-uttryck: `DateTime.Now.ToString("M/d/yyyy")`

Här är ett exempel på hur du använder det här uttrycket i ett skript:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Använda .NET-sammansättningar

U-SQL: s utöknings modell är mycket beroende av möjligheten att lägga till anpassad kod från .NET-sammansättningar. 

### <a name="register-a-net-assembly"></a>Registrera en .NET-sammansättning

Använd `CREATE ASSEMBLY` instruktionen för att placera en .net-sammansättning i en U-SQL Database. Därefter kan U-SQL-skript använda dessa sammansättningar med hjälp av `REFERENCE ASSEMBLY` instruktionen. 

Följande kod visar hur du registrerar en sammansättning:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Följande kod visar hur du refererar till en sammansättning:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Mer information finns i [anvisningarna för monterings registrering](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) som täcker det här avsnittet.


### <a name="use-assembly-versioning"></a>Använd sammansättnings version
För närvarande använder U-SQL .NET Framework-versionen 4.7.2. Se till att dina egna sammansättningar är kompatibla med den versionen av körnings miljön.

Som tidigare nämnts kör U-SQL kod i ett 64-bitars format (x64). Se därför till att din kod kompileras för att köras på x64. Annars får du ett felaktigt format fel som visas tidigare.

Varje Uppladdad sammansättnings-DLL och resurs fil, till exempel en annan körning, en intern sammansättning eller en konfigurations fil, kan vara högst 400 MB. Den totala storleken på distribuerade resurser, antingen via distribuera resurs eller via referenser till sammansättningar och deras ytterligare filer, får inte överstiga 3 GB.

Observera att varje U-SQL-databas bara kan innehålla en version av en specifik sammansättning. Om du till exempel behöver både version 7 och version 8 av NewtonSoft Json.NET-biblioteket måste du registrera dem i två olika databaser. Dessutom kan varje skript bara referera till en version av en specifik sammansättnings-DLL. I detta avseende är U-SQL följande semantik för sammansättnings hantering och versions hantering i C#.

## <a name="use-user-defined-functions-udf"></a>Använd användardefinierade funktioner: UDF
U-SQL-användardefinierade funktioner eller UDF, är programmerings rutiner som accepterar parametrar, utför en åtgärd (till exempel en komplex beräkning) och returnerar resultatet av åtgärden som ett värde. Returvärdet för UDF kan bara vara en enda skalär. U-SQL UDF kan anropas i U-SQL Base-skript, precis som vilken annan C#-skalär funktion som helst.

Vi rekommenderar att du initierar U-SQL-användardefinierade funktioner som **offentliga** och **statiska**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Först ska vi titta på det enkla exemplet med att skapa en UDF.

I det här scenariot för användnings fall måste vi fastställa räkenskaps perioden, inklusive räkenskaps kvartal och räkenskaps månad för den första inloggningen för den aktuella användaren. Den första räkenskaps månaden för året i vårt scenario är juni.

För att beräkna räkenskaps perioden introducerar vi följande C#-funktion:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Den beräknar bara räkenskaps månad och kvartal och returnerar ett sträng värde. I juni, den första månaden i det första räkenskaps kvartalet använder vi "Q1: P1". För juli använder vi "Q1: P2" och så vidare.

Det här är en vanlig C#-funktion som vi ska använda i vårt U-SQL-projekt.

Så här ser avsnittet bakomliggande kod ut i det här scenariot:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Nu ska vi anropa den här funktionen från det grundläggande U-SQL-skriptet. För att göra detta måste vi tillhandahålla ett fullständigt kvalificerat namn för funktionen, inklusive namn området, som i det här fallet är namnrymd. class. function (parameter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Följande är det faktiska U-SQL-bas skriptet:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Följande är utdatafilen för skript körningen:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Det här exemplet visar en enkel användning av infogad UDF i U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Behåll tillstånd mellan UDF-anrop
U-SQL C# programmerbara objekt kan vara mer avancerade och använda interaktivitet genom bakomliggande globala variabler i bakomliggande kod. Nu ska vi titta på följande scenario för affärs användnings fall.

I stora organisationer kan användare byta mellan olika sorters interna program. Dessa kan omfatta Microsoft Dynamics CRM, Power BI och så vidare. Kunder kanske vill använda en telemetri analys av hur användare växlar mellan olika program, vad användnings trender är och så vidare. Målet för verksamheten är att optimera program användningen. De kan också vilja kombinera olika program eller speciella inloggnings rutiner.

För att uppnå det här målet måste vi fastställa sessions-ID: n och fördröjnings tiden mellan den senaste sessionen som inträffat.

Vi måste hitta en tidigare inloggning och sedan tilldela den här inloggningen till alla sessioner som genereras till samma program. Den första utmaningen är att bas skriptet U-SQL inte tillåter att vi använder beräkningar över redan beräknade kolumner med funktionen fördröjning. Den andra utmaningen är att vi måste behålla den aktuella sessionen för alla sessioner inom samma tids period.

För att lösa det här problemet använder vi en global variabel i ett avsnitt med bakomliggande kod: `static public string globalSession;` .

Den här globala variabeln tillämpas på hela rad uppsättningen vid skript körningen.

Här är avsnittet bakomliggande kod i vårt U-SQL-program:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

I det här exemplet visas den globala variabel `static public string globalSession;` som används i `getStampUserSession` funktionen och ominitieras varje gång parametern session ändras.

U-SQL Base-skriptet är följande:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Funktionen `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` anropas här under beräkningen av den andra minnes rad uppsättningen. `UserSessionTimestamp`Kolumnen skickas och värdet returneras tills `UserSessionTimestamp` har ändrats.

Utdatafilen är följande:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Det här exemplet visar en mer komplicerad användnings Falls situation där vi använder en global variabel i ett bakomliggande kod-avsnitt som tillämpas på hela minnes rad uppsättningen.

## <a name="use-user-defined-types-udt"></a>Använd användardefinierade typer: UDT
Användardefinierade typer, eller UDT, är en annan programmerings funktion i U-SQL. U-SQL UDT fungerar som en vanlig C#-användardefinierad typ. C# är ett starkt skrivet språk som gör det möjligt att använda inbyggda och anpassade användardefinierade typer.

U-SQL kan inte implicit serialisera eller deserialisera godtyckliga UDTs när UDT skickas mellan formhörn i rad uppsättningar. Det innebär att användaren måste ange en explicit formatering med hjälp av IFormatter-gränssnittet. Detta ger U-SQL med metoderna serialisera och deserialisera för UDT.

> [!NOTE]
> U-SQL: s inbyggda Extraherare och utvinnare kan för närvarande inte serialisera eller deserialisera UDT-data till eller från filer även med IFormatter-uppsättningen. När du skriver UDT-data till en fil med OUTPUT-instruktionen eller läser den med en Extractor måste du skicka den som en sträng eller en byte mat ris. Sedan anropar du serialisering och avserialiserings kod (det vill säga UDT: s ToString ()-metod) explicit. Användardefinierade avgränsare och utvinnare, å andra sidan, kan läsa och skriva UDTs.

Om vi försöker använda UDT i EXTRACTOR eller ett uttrycks värde (utanför föregående val), som du ser här:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Vi får följande fel meddelande:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

För att kunna arbeta med UDT i en utlösare måste du antingen serialisera den till en sträng med metoden ToString () eller skapa en anpassad utlösare.

UDTs kan för närvarande inte användas i GROUP BY. Om UDT används i GROUP BY, genereras följande fel:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

För att definiera en UDT måste vi:

* Lägg till följande namn rymder:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Lägg till `Microsoft.Analytics.Interfaces` , vilket krävs för UDT-gränssnitten. `System.IO`Du kan också behöva definiera IFormatter-gränssnittet.

* Definiera en Använd definierad typ med attributet SqlUserDefinedType.

**SqlUserDefinedType** används för att markera en typ definition i en sammansättning som en användardefinierad typ (UDT) i U-SQL. Egenskaperna för attributet reflekterar de fysiska egenskaperna för UDT. Det går inte att ärva den här klassen.

SqlUserDefinedType är ett obligatoriskt attribut för UDT-definitionen.

Klassens konstruktor:  

* SqlUserDefinedTypeAttribute (typ Formatter)

* Typ Formatter: obligatorisk parameter för att definiera en UDT-formatering – särskilt måste typ av `IFormatter` gränssnitt skickas här.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typisk UDT kräver också definition av IFormatter-gränssnittet, som visas i följande exempel:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Gränssnittet serialiserar och deserialiserar ett objekt diagram med rot typen \<typeparamref name="T"> .

\<typeparam name="T">Objekt diagramets rottyp för att serialisera och deserialisera.

* **Deserialisering**: deserialiserar data på den angivna strömmen och utbildar grafen över objekt.

* **Serialisera**: serialiserar ett objekt eller diagram med objekt, med den angivna roten till den angivna data strömmen.

`MyType` instans: instans av typen.  
`IColumnWriter` skribent/ `IColumnReader` läsare: den underliggande kolumn data strömmen.  
`ISerializationContext` Context: Enum som definierar en uppsättning flaggor som anger käll-eller mål kontexten för data strömmen under serialisering.

* **Mellan**: anger att käll-eller mål kontexten inte är ett beständigt arkiv.

* **Persistence**: anger att käll-eller mål kontexten är en sparad lagrings plats.

Som en vanlig C#-typ kan en U-SQL UDT-definition innehålla åsidosättningar för operatorer som +/= =/! =. Den kan även innehålla statiska metoder. Om vi till exempel ska använda denna UDT som en parameter till en f-SQL minsta mängd funktion, måste vi definiera < operatörs åsidosättning.

Tidigare i den här hand boken demonstrerade vi ett exempel för räkenskaps period identifiering från det angivna datumet i formatet `Qn:Pn (Q1:P10)` . I följande exempel visas hur du definierar en anpassad typ för värden för räkenskaps perioden.

Följande är ett exempel på ett avsnitt med bakomliggande kod med anpassade UDT-och IFormatter-gränssnitt:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Den definierade typen innehåller två siffror: kvartal och månad. Operatorer `==/!=/>/<` och statiska metoder `ToString()` definieras här.

Som nämnts tidigare kan UDT användas i SELECT-uttryck, men det går inte att använda den i utrycke/EXTRACTOR utan anpassad serialisering. Den måste antingen serialiseras som en sträng med `ToString()` eller använda med en anpassad utskickare/Extractor.

Nu ska vi diskutera användningen av UDT. I ett avsnitt med bakomliggande kod ändrade vi vår GetFiscalPeriod-funktion till följande:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Som du kan se returneras värdet för vår FiscalPeriod-typ.

Här ger vi ett exempel på hur du kan använda det ytterligare i grundläggande skript i U-SQL. Det här exemplet visar olika former av UDT-anrop från U-SQL-skript.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Här är ett exempel på ett fullständigt bakomliggande kod avsnitt:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Använd användardefinierade agg regeringar: UDAGG
Användardefinierade agg regeringar är alla agg regerings funktioner som inte levereras direkt med U-SQL. Exemplet kan vara en agg regering för att utföra anpassade matematiska beräkningar, sträng sammanfogningar, modifieringar med strängar och så vidare.

Den användardefinierade agg regerings Bask lass definitionen är följande:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** anger att typen ska registreras som en användardefinierad mängd. Det går inte att ärva den här klassen.

Attributet SqlUserDefinedType är **valfritt** för UDAGG-definitionen.


Med Bask Lassen kan du skicka tre abstrakta parametrar: två som indataparametrar och en som resultat. Data typerna är variabla och bör definieras under klass arvet.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** anropar en gång för varje grupp under beräkning. Den innehåller en initierings rutin för varje agg regerings grupp.  
* **Ackumulerad** utförs en gång för varje värde. Den tillhandahåller huvud funktionerna för agg regerings algoritmen. Den kan användas för att aggregera värden med olika data typer som definieras vid klass arvet. Den kan acceptera två parametrar för variabla data typer.
* **Avsluta** utförs en gång per sammansättnings grupp i slutet av bearbetningen för att generera resultatet för varje grupp.

Om du vill deklarera rätt data typer för indata och utdata använder du klass definitionen enligt följande:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: första parametern som ska ackumuleras
* T2: den andra parametern som ska samlas
* TResult: retur typen Avbryt

Exempel:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

eller

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Använd UDAGG i U-SQL
Om du vill använda UDAGG ska du först definiera den i bakomliggande eller referera till den från den befintliga programmerings-DLL: en som beskrivs ovan.

Använd sedan följande syntax:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Här är ett exempel på UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

Och basera U-SQL-skript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

I det här användnings fallet sammanfogar vi klass-GUID för de aktuella användarna.

## <a name="use-user-defined-objects-udo"></a>Använd användardefinierade objekt: UDO
Med U-SQL kan du definiera anpassade programmerings objekt, som kallas användardefinierade objekt eller UDO.

Följande är en lista över UDO i U-SQL:

* Användardefinierade Extraherare
    * Extrahera rad per rad
    * Används för att implementera data extrahering från anpassade strukturerade filer

* Användardefinierade utsparatillfil
    * Mata ut rad per rad
    * Används för att mata ut anpassade data typer eller anpassade fil format

* Användardefinierade processorer
    * Ta en rad och skapa en rad
    * Används för att minska antalet kolumner eller skapa nya kolumner med värden som härleds från en befintlig kolumn uppsättning

* Användardefinierade appliers
    * Ta en rad och producera 0 till n rader
    * Används med yttre/kors koppling

* Användardefinierade kombinations
    * Kombinerar rad uppsättningar – användardefinierade kopplingar

* Användardefinierade reducerare
    * Ta n rader och skapa en rad
    * Används för att minska antalet rader

UDO anropas vanligt vis explicit i U-SQL-skript som en del av följande U-SQL-uttryck:

* EXTRACT
* UTDATAPARAMETRAR
* UPPGRADERINGEN
* KOMBINERA
* MINSKA

> [!NOTE]  
> UDO är begränsade till att förbruka 0,5 GB minne.  Den här minnes begränsningen gäller inte för lokala körningar.

## <a name="use-user-defined-extractors"></a>Använd användardefinierade Extraherare
Med U-SQL kan du importera externa data med hjälp av en EXTRACT-instruktion. En EXTRACT-instruktion kan använda inbyggda UDO-Extraherare:  

* *Reextractrs. text ()*: tillhandahåller extrahering från avgränsade textfiler med olika kodningar.

* *Extractors.Csv ()*: tillhandahåller extrahering från CSV-filer (kommaavgränsade värden) med olika kodningar.

* *Reextractrs. tsv ()*: tillhandahåller extrahering av TSV-filer (Tabbavgränsade värden) med olika kodningar.

Det kan vara användbart att utveckla en anpassad Extractor. Detta kan vara till hjälp vid data import om vi vill utföra någon av följande uppgifter:

* Ändra indata genom att dela upp kolumner och ändra enskilda värden. PROCESSOR funktionerna är bättre för att kombinera kolumner.
* Parsa ostrukturerade data, till exempel webb sidor och e-postmeddelanden eller delvis ostrukturerade data, till exempel XML/JSON.
* Parsa data i kodning som inte stöds.

Vi måste skapa ett gränssnitt för att definiera en användardefinierad Extractor-eller OMMANDOT-Beskrivning `IExtractor` . Alla indataparametrar till Extractor, till exempel kolumn-och rad avgränsare och kodning, måste definieras i klassens konstruktor. `IExtractor`Gränssnittet bör också innehålla en definition för `IEnumerable<IRow>` åsidosättningen enligt följande:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Attributet **SqlUserDefinedExtractor** anger att typen ska registreras som en användardefinierad Extractor. Det går inte att ärva den här klassen.

SqlUserDefinedExtractor är ett valfritt attribut för OMMANDOT-definitionen. Den används för att definiera egenskapen AtomicFileProcessing för objektet OMMANDOT.

* bool AtomicFileProcessing   

* **True** = anger att denna Extractor kräver Atom-indatafiler (JSON, XML,...)
* **false** = anger att denna Extractor kan hantera delade/distribuerade filer (CSV, seq,...)

De huvudsakliga OMMANDOT-objekten är **indata** och **utdata**. Indata-objektet används för att räkna upp indata som `IUnstructuredReader` . Objektet utdata används för att ange utdata som ett resultat av aktiviteten Extractor.

Indata nås via `System.IO.Stream` och `System.IO.StreamReader` .

För uppräkning av indata-kolumner delar vi först upp indataströmmen med hjälp av en rad avgränsare.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Sedan kan du ytterligare dela upp indatamängden i kolumn delar.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Vi använder-metoden för att ange utdata `output.Set` .

Det är viktigt att förstå att den anpassade extraheringen bara matar ut kolumner och värden som har definierats med utdata. Ange metod anrop.

```csharp
output.Set<string>(count, part);
```

Det faktiska extraherings resultatet utlöses genom att anropa `yield return output.AsReadOnly();` .

Följande är exempel på Extractor:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

I det här användnings fallet genererar extraheringen GUID för kolumnen "GUID" och konverterar värdena för kolumnen "användare" till versaler. Anpassade extraktioner kan producera mer komplicerade resultat genom att parsa indata och ändra dem.

Följande är grundläggande U-SQL-skript som använder en anpassad Extractor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Använd användardefinierade utsparatillfil
Användardefinierad utskickare är en annan U-SQL-UDO som gör att du kan utöka de inbyggda U-SQL-funktionerna. Precis som i Extractor finns det flera inbyggda uppslags grupper.

* *Sparatillfils. text ()*: skriver data till avgränsade textfiler med olika kodningar.
* *Outputters.Csv ()*: skriver data till filer med kommaavgränsade värden (CSV) med olika kodningar.
* *Sparatillfils. tsv ()*: skriver data till TSV-filer (tabbavgränsad värde) med olika kodningar.

Med anpassad utmatnings fil kan du skriva data i ett anpassat definierat format. Detta kan vara användbart för följande uppgifter:

* Skriva data till halv strukturerade eller ostrukturerade filer.
* Skrivning av data stöder inte kodningar som stöds.
* Ändra utdata eller lägga till anpassade attribut.

Vi måste skapa gränssnittet för att definiera en användardefinierad utsparatillfile `IOutputter` .

Följande är Bask `IOutputter` lass implementeringen:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alla indataparametrar till utdataporten, till exempel kolumn-och rad avgränsare, kodning och så vidare, måste definieras i klassens konstruktor. `IOutputter`Gränssnittet bör också innehålla en definition för `void Output` åsidosättning. Attributet `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan alternativt anges för atomisk fil bearbetning. Mer information finns i följande information.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` anropas för varje indataparameter. Den returnerar `IUnstructuredWriter output` rad uppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade uppslags listan.
* `Close` används för att åsidosätta kostsamma tillstånd eller fastställa när den sista raden skrevs.

Attributet **SqlUserDefinedOutputter** anger att typen ska registreras som en användardefinierad Utskicks text. Det går inte att ärva den här klassen.

SqlUserDefinedOutputter är ett valfritt attribut för en användardefinierad utuppsättnings definition. Den används för att definiera egenskapen AtomicFileProcessing.

* bool AtomicFileProcessing   

* **True** = anger att den här utmatnings filen kräver Atom-utdatafiler (JSON, XML,...)
* **false** = anger att den här utlösaren kan hantera delade/distribuerade filer (CSV, seq,...)

Huvud programmerings objekt är **rad** och **utdata**. **Row** -objektet används för att räkna upp utdata som `IRow` gränssnitt. **Utdata** används för att ange utdata för mål filen.

Utdata nås via `IRow` gränssnittet. Utdata skickas till en rad i taget.

De enskilda värdena räknas upp genom anrop av Get-metoden för IRow-gränssnittet:

```csharp
row.Get<string>("column_name")
```

Enskilda kolumn namn kan fastställas genom att anropa `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Med den här metoden kan du bygga en flexibel utvinnare för alla metadata-scheman.

Utdata skrivs till filen med hjälp av `System.IO.StreamWriter` . Data Ströms parametern anges `output.BaseStream` som en del av `IUnstructuredWriter output` .

Observera att det är viktigt att tömma databufferten till filen efter varje rad iteration. Dessutom `StreamWriter` måste objektet användas med attributet disponibelt aktiverat (standard) och med nyckelordet **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Annars anropar du flush ()-metoden direkt efter varje iteration. Vi visar detta i följande exempel.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ange sidhuvud och sidfot för användardefinierad uttrycks List
Om du vill ange ett sidhuvud använder du ett enda iteration körnings flöde.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Koden i det första `if (isHeaderRow)` blocket körs bara en gång.

Använd referensen till instansen av `System.IO.Stream` objektet () för sidfoten `output.BaseStream` . Skriv sidfoten i Close ()-metoden för `IOutputter` gränssnittet.  (Mer information finns i följande exempel.)

Följande är ett exempel på en användardefinierad utsparatillfil:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

Och U-SQL Base-skript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Detta är en HTML-fil som skapar en HTML-fil med tabell data.

### <a name="call-outputter-from-u-sql-base-script"></a>Anropa upscriptor från U-SQL Base-skriptet
Om du vill anropa en anpassad utbildare från det grundläggande U-SQL-skriptet, måste den nya instansen av ett utstils objekt skapas.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

För att undvika att skapa en instans av objektet i bas skriptet, kan vi skapa ett funktions tecken, som du ser i vårt tidigare exempel:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

I det här fallet ser det ursprungliga anropet ut så här:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Använd användardefinierade processorer
Användardefinierad processor, eller UDP, är en typ av U-SQL-UDO som gör att du kan bearbeta inkommande rader genom att tillämpa programmerings funktioner. Med UDP kan du kombinera kolumner, ändra värden och lägga till nya kolumner om det behövs. I princip hjälper det att bearbeta en rad uppsättning för att producera nödvändiga data element.

För att definiera en UDP måste vi skapa ett `IProcessor` gränssnitt med `SqlUserDefinedProcessor` attributet, vilket är valfritt för UDP.

Det här gränssnittet ska innehålla definitionen för `IRow` åsidosättningen av gränssnitts rad uppsättningen, som visas i följande exempel:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** anger att typen ska registreras som en användardefinierad processor. Det går inte att ärva den här klassen.

Attributet SqlUserDefinedProcessor är **valfritt** för UDP-definition.

Huvud programmerings objekt är **indata** och **utdata**. Indata-objektet används för att räkna upp inmatnings kolumner och utdata och för att ange utdata som ett resultat av processor aktiviteten.

För uppräkning av indatamängds kolumner använder vi `input.Get` metoden.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametern för `input.Get` metoden är en kolumn som har skickats som en del av `PRODUCE` satsen i `PROCESS` instruktionen för U-SQL Base-skriptet. Vi måste använda rätt datatyp här.

För utdata använder du- `output.Set` metoden.

Det är viktigt att notera att den anpassade producenten endast matar ut kolumner och värden som definieras med `output.Set` metod anropet.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Faktisk processor utmatning utlöses genom att anropa `return output.AsReadOnly();` .

Följande är ett processor exempel:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

I det här användnings fallet genererar processorn en ny kolumn med namnet "full_description" genom att kombinera befintliga kolumner, i det här fallet "användare" i versaler och "des". Den återskapar också ett GUID och returnerar de ursprungliga och nya GUID-värdena.

Som du kan se i föregående exempel kan du anropa C#-metoder under `output.Set` metod anrop.

Följande är ett exempel på ett grundläggande U-SQL-skript som använder en anpassad processor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Använd användardefinierad appliers
Med ett U-SQL-användardefinierat applier kan du anropa en anpassad C#-funktion för varje rad som returneras av det yttre tabell uttrycket i en fråga. Den högra inmatningen utvärderas för varje rad från den vänstra inmatningen och de rader som skapas kombineras för slutliga utdata. Listan över kolumner som skapas av operatorn APPLY är en kombination av kolumn uppsättningen i vänster och höger Indatatyp.

En användardefinierad applier anropas som en del av USQL SELECT-uttrycket.

Det vanligaste anropet till den användardefinierade applier ser ut så här:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Mer information om hur du använder appliers i ett SELECT-uttryck finns i [U-SQL väljer du väljer från kors Apply och Outer Apply](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Den användardefinierade applier Bask Class-definitionen är följande:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

För att definiera en användardefinierad applier, måste vi skapa `IApplier` gränssnittet med `SqlUserDefinedApplier` attributet [], vilket är valfritt för en användardefinierad applier-definition.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply anropas för varje rad i den yttre tabellen. Den returnerar `IUpdatableRow` utmatnings rad uppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade applier.

**SqlUserDefinedApplier** anger att typen ska registreras som en användardefinierad applier. Det går inte att ärva den här klassen.

**SqlUserDefinedApplier** är **valfritt** för en användardefinierad applier-definition.


Huvud programmerings objekt är följande:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Uppsättningar med indatamängdar skickas som inloggade `IRow` . De utgående raderna genereras som `IUpdatableRow` utdata-gränssnitt.

Du kan bestämma enskilda kolumn namn genom att anropa `IRow` schema metoden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

För att hämta de faktiska datavärdena från det inkommande `IRow` använder vi metoden Get () för `IRow` gränssnittet.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Eller så använder vi schema kolumn namnet:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Värdena för utdata måste anges med `IUpdatableRow` output:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Det är viktigt att förstå att anpassade appliers endast innehåller utdatakolumner och värden som definieras med `output.Set` metod anrop.

Faktiska utdata utlöses genom att anropa `yield return output.AsReadOnly();` .

De användardefinierade applier-parametrarna kan skickas till konstruktorn. Applier kan returnera ett variabel antal kolumner som måste definieras under applier-anropet i bas U-SQL-skriptet.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Här är det användardefinierade applier-exemplet:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Följande är det grundläggande U-SQL-skriptet för den här användardefinierade applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

I det här scenariot för användnings fall fungerar användardefinierade applier som en kommaavgränsad värde tolkare för egenskaperna för Car-flottan. Raderna i indatafilen ser ut ungefär så här:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Det är en typisk tabbavgränsad TSV-fil med en egenskaps kolumn som innehåller bil egenskaper som märke och modell. Dessa egenskaper måste parsas till tabell kolumnerna. Med applier som tillhandahålls kan du också generera ett dynamiskt antal egenskaper i resultat rad uppsättningen, baserat på den parameter som skickades. Du kan endast generera alla egenskaper eller en speciell uppsättning egenskaper.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Användardefinierade applier kan anropas som en ny instans av applier-objektet:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Eller med anropet till en omslutnings fabriks metod:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Använd användardefinierade kombinations
Med användardefinierad kombinations-eller UDC-uppsättning kan du kombinera rader från vänster och höger rad uppsättningar, baserat på anpassad logik. Användardefinierad kombinerare används med kombinera-uttryck.

En kombinerare anropas med ett KOMBINATIONs uttryck som innehåller den information som krävs om båda uppsättningarna med indata, grupper, resultat schema och ytterligare information.

För att anropa en kombinerare i ett bas U-SQL-skript, använder vi följande syntax:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Mer information finns i [kombinera uttryck (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

För att definiera en användardefinierad kombinations samling måste vi skapa `ICombiner` gränssnittet med `SqlUserDefinedCombiner` attributet [], vilket är valfritt för en användardefinierad kombinations definition.

Definition av bas `ICombiner` klass:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Den anpassade implementeringen av ett `ICombiner` gränssnitt ska innehålla definitionen för en `IEnumerable<IRow>` kombinerad åsidosättning.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Attributet **SqlUserDefinedCombiner** anger att typen ska registreras som en användardefinierad kombinations samling. Det går inte att ärva den här klassen.

**SqlUserDefinedCombiner** används för att definiera egenskapen kombinations läge. Det är ett valfritt attribut för en användardefinierad kombinations definition.

CombinerMode-läge

CombinerMode Enum kan ha följande värden:

* Fullständig (0) varje utgående rad kan vara beroende av alla indata-rader från vänster och höger med samma nyckel värde.

* Left (1) varje utgående rad är beroende av en enda inmatnings rad från vänster (och eventuellt alla rader från höger med samma nyckel värde).

* Höger (2) varje utgående rad är beroende av en enda inmatnings rad från den högra (och eventuellt alla rader från vänster med samma nyckel värde).

* Inre (3) varje utgående rad är beroende av en enda inmatnings rad från vänster och höger med samma värde.

Exempel: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


De huvudsakliga programmerings objekt är:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Uppsättningar med indatakälla skickas som **vänster** och **höger** `IRowset` typ av gränssnitt. Båda rad uppsättningarna måste räknas upp för bearbetning. Du kan bara räkna upp varje gränssnitt en gång, så vi måste räkna upp och cachelagra det om det behövs.

För cachelagring kan vi skapa en lista \<T\> över minnes strukturer som ett resultat av en LINQ-frågekörningen, särskilt List<`IRow`>. Den anonyma data typen kan bara användas vid uppräkning.

Se [Introduktion till LINQ-frågor (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) för mer information om LINQ-frågor och [IEnumerable- \<T\> gränssnitt](/dotnet/api/system.collections.generic.ienumerable-1) för mer information om IEnumerable- \<T\> gränssnitt.

För att hämta de faktiska datavärdena från det inkommande `IRowset` använder vi metoden Get () för `IRow` gränssnittet.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Du kan bestämma enskilda kolumn namn genom att anropa `IRow` schema metoden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Eller med hjälp av namnet på schema kolumnen:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Den allmänna uppräkningen med LINQ ser ut så här:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

När du har uppräknat båda rad uppsättningarna kommer vi att gå igenom alla rader. För varje rad i den vänstra rad uppsättningen kommer vi att hitta alla rader som uppfyller villkoret för vår kombinations samling.

Värdena för utdata måste anges med `IUpdatableRow` output.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Faktiska utdata utlöses genom att anropa till `yield return output.AsReadOnly();` .

Följande är ett kombinations exempel:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

I det här scenariot för användnings fall skapar vi en analys rapport för åter försäljaren. Målet är att hitta alla produkter som kostar mer än $20 000 och som säljer via webbplatsen snabbare än via den vanliga åter försäljaren inom en viss tids period.

Här är det grundläggande U-SQL-skriptet. Du kan jämföra logiken mellan en vanlig koppling och en kombinerare:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

En användardefinierad kombinations rutan kan anropas som en ny instans av applier-objektet:

```csharp
USING new MyNameSpace.MyCombiner();
```


Eller med anropet till en omslutnings fabriks metod:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Använd användardefinierade avgränsare

Med U-SQL kan du skriva anpassade rad uppsättnings Defiler i C# med hjälp av det användardefinierade ramverket för operatörs utökning och implementera ett IReducer-gränssnitt.

Användardefinierad minsknings punkt eller UDR kan användas för att eliminera onödiga rader under data extrahering (import). Den kan också användas för att manipulera och utvärdera rader och kolumner. Baserat på programmerings logik kan det också definiera vilka rader som ska extraheras.

För att kunna definiera en UDR-klass måste vi skapa ett `IReducer` gränssnitt med ett valfritt `SqlUserDefinedReducer` attribut.

Det här klass gränssnittet ska innehålla en definition för `IEnumerable` åsidosättningen av gränssnitts rad uppsättningen.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Attributet **SqlUserDefinedReducer** anger att typen ska vara registrerad som användardefinierad minskning. Det går inte att ärva den här klassen.
**SqlUserDefinedReducer** är ett valfritt attribut för en användardefinierad minsknings definition. Den används för att definiera IsRecursive-egenskapen.

* bool IsRecursive    
* **True**  = anger om denna minskning är associativ och commutative

Huvud programmerings objekt är **indata** och **utdata**. Det inmatade objektet används för att räkna upp ingående rader. Utdata används för att ange utmatnings rader som ett resultat av en minskning av aktiviteten.

Vi använder-metoden för uppräkning av inmatade rader `Row.Get` .

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametern för `Row.Get` metoden är en kolumn som har skickats som en del av `PRODUCE` `REDUCE` satsen i instruktionen U-SQL Base. Vi behöver använda rätt datatyp här även.

För utdata använder du- `output.Set` metoden.

Det är viktigt att förstå att anpassad minskning bara matar ut värden som definieras med `output.Set` metod anropet.

```csharp
output.Set<string>("mycolumn", guid);
```

Den faktiska minskningens utdata utlöses genom att anropa `yield return output.AsReadOnly();` .

Följande är ett exempel på en minsknings exempel:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

I det här scenariot för användnings fall hoppar avminskningen av rader med ett tomt användar namn. För varje rad i rad uppsättningen läser den varje obligatorisk kolumn och utvärderar sedan användar namnets längd. Den returnerar den faktiska raden endast om värdets längd för användar namn är större än 0.

Följande är grundläggande U-SQL-skript som använder en anpassad minskning:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
