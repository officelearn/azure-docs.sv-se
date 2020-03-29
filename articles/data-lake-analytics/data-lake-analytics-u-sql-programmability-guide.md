---
title: U-SQL-programmerbarhetsguide för Azure Data Lake
description: Lär dig mer om uppsättningen tjänster i Azure Data Lake Analytics som gör att du kan skapa en molnbaserad stordataplattform.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162574"
---
# <a name="u-sql-programmability-guide"></a>U-SQL-programmerbarhetsguide

U-SQL är ett frågespråk som är utformat för stordatatyp av arbetsbelastningar. En av de unika funktionerna i U-SQL är kombinationen av SQL-liknande deklarativa språk med utökningsbarhet och programmerbarhet som tillhandahålls av C #. I den här guiden koncentrerar vi oss på utökningsbarheten och programmerbarheten för det U-SQL-språk som är aktiverat av C#.

## <a name="requirements"></a>Krav

Ladda ned och installera [Azure Data Lake Tools för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Kom igång med U-SQL  

Titta på följande U-SQL-skript:

```
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

Det här skriptet definierar `@a` `@results`två Raduppsättningar: och . RowSet `@results` definieras `@a`från .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typer och uttryck i U-SQL-skript

Ett U-SQL-uttryck är ett C#-uttryck kombinerat `OR`med `NOT`logiska åtgärder för U-SQL, till exempel `AND`, och . U-SQL-uttryck kan användas med SELECT, EXTRACT, WHERE, HAVING, GROUP BY och DECLARE. Följande skript tolkar till exempel en sträng som ett DateTime-värde.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Följande kodavsnitt tolkar en sträng som DateTime-värde i en DECLARE-sats.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Använda C#-uttryck för datatypskonverteringar

I följande exempel visas hur du kan göra en datetime-datakonvertering med hjälp av C#-uttryck. I det här scenariot konverteras strängdatumtidsdata till standarddatumtid med midnatt 00:00:00 tidsantering.

```
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

För att dra dagens datum, kan vi använda följande C # uttryck:`DateTime.Now.ToString("M/d/yyyy")`

Här är ett exempel på hur du använder det här uttrycket i ett skript:

```
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

U-SQL:s utökningsbarhetsmodell är starkt beroende av möjligheten att lägga till anpassad kod från .NET-sammansättningar. 

### <a name="register-a-net-assembly"></a>Registrera en .NET-sammansättning

Använd `CREATE ASSEMBLY` uttrycket för att placera en .NET-sammansättning i en U-SQL-databas. Därefter kan U-SQL-skript använda dessa sammansättningar `REFERENCE ASSEMBLY` med hjälp av uttrycket. 

Följande kod visar hur du registrerar en sammansättning:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Följande kod visar hur du refererar till en sammansättning:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Läs [monteringsregistreringsinstruktionerna](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) som beskriver det här avsnittet mer i detalj.


### <a name="use-assembly-versioning"></a>Använda monteringsversionering
För närvarande använder U-SQL .NET Framework version 4.5. Så se till att dina egna sammansättningar är kompatibla med den versionen av körningen.

Som tidigare nämnts kör U-SQL kod i ett 64-bitars (x64) format. Så se till att din kod är kompilerad för att köras på x64. Annars får du fel formatfel som visas tidigare.

Varje uppladdad monterings-DLL och resursfil, till exempel en annan körning, en inbyggd sammansättning eller en config-fil, kan vara högst 400 MB. Den totala storleken på distribuerade resurser, antingen via DEPLOY RESOURCE eller via referenser till sammansättningar och deras ytterligare filer, får inte överstiga 3 GB.

Observera slutligen att varje U-SQL-databas bara kan innehålla en version av en viss sammansättning. Om du till exempel behöver både version 7 och version 8 av NewtonSoft Json.NET-biblioteket måste du registrera dem i två olika databaser. Dessutom kan varje skript bara referera till en version av en viss monterings-DLL. I detta avseende följer U-SQL C# monteringshantering och versionshantering semantik.

## <a name="use-user-defined-functions-udf"></a>Använda användardefinierade funktioner: UDF
U-SQL-användardefinierade funktioner, eller UDF, är programmeringsrutiner som accepterar parametrar, utför en åtgärd (till exempel en komplex beräkning) och returnerar resultatet av åtgärden som ett värde. Returvärdet för UDF kan bara vara ett enda skalär. U-SQL UDF kan anropas i U-SQL-basskript som alla andra C# skalär funktion.

Vi rekommenderar att du initierar U-SQL-användardefinierade funktioner som **offentliga** och **statiska**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Låt oss först titta på det enkla exemplet på att skapa en UDF.

I det här användningsfallsscenariot måste vi bestämma räkenskapsperioden, inklusive räkenskapsåret och räkenskapsmånaden för den första inloggningen för den specifika användaren. Årets första räkenskapsmånad i vårt scenario är juni.

För att beräkna räkenskapsperioden introducerar vi följande C#-funktion:

```
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

Den beräknar helt enkelt räkenskapsårets månad och kvartal och returnerar ett strängvärde. För juni, den första månaden i det första räkenskapsåret, använder vi "Q1:P1". För juli använder vi "Q1:P2", och så vidare.

Detta är en vanlig C# funktion som vi kommer att använda i vårt U-SQL-projekt.

Så här ser avsnittet bakom kod i det här scenariot ut:

```
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

Nu ska vi anropa den här funktionen från bas-U-SQL-skriptet. För att göra detta måste vi ange ett fullständigt kvalificerat namn för funktionen, inklusive namnområdet, som i det här fallet är NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Följande är det faktiska U-SQL-basskriptet:

```
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

Följande är utdatafilen för skriptkörningen:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Det här exemplet visar en enkel användning av infogad UDF i U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Håll tillstånd mellan UDF-anrop
U-SQL C# programmability objekt kan vara mer sofistikerade, med hjälp av interaktivitet genom de bakomliggande globala variabler. Låt oss titta på följande affärsanvändningsscenario.

I stora organisationer kan användare växla mellan varianter av interna program. Dessa kan inkludera Microsoft Dynamics CRM, PowerBI och så vidare. Kunder kanske vill använda en telemetrianalys av hur användare växlar mellan olika program, vilka användningstrender som finns och så vidare. Målet för verksamheten är att optimera programanvändningen. De kanske också vill kombinera olika program eller specifika inloggningsrutiner.

För att uppnå detta mål måste vi bestämma sessions-ID och fördröjning mellan den senaste sessionen som inträffade.

Vi måste hitta en tidigare inloggning och sedan tilldela den här inloggningen till alla sessioner som genereras till samma program. Den första utmaningen är att U-SQL-basskript inte tillåter oss att tillämpa beräkningar över redan beräknade kolumner med LAG-funktion. Den andra utmaningen är att vi måste hålla den specifika sessionen för alla sessioner inom samma tidsperiod.

För att lösa detta problem använder vi en global `static public string globalSession;`variabel i ett bakomkodavsnitt: .

Den här globala variabeln tillämpas på hela raduppsättningen under skriptkörningen.

Här är den kod-bakom delen av vårt U-SQL-program:

```
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

I det här `static public string globalSession;` exemplet visas `getStampUserSession` den globala variabel som används i funktionen och få initierade om varje gång parametern Session ändras.

U-SQL-basskriptet är följande:

```
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

Funktionen `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` anropas här under den andra minnesradsuppsättningsberäkningen. Den skickar `UserSessionTimestamp` kolumnen och returnerar värdet tills `UserSessionTimestamp` det har ändrats.

Utdatafilen är följande:

```
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

Det här exemplet visar ett mer komplicerat användningsfall-scenario där vi använder en global variabel i ett bakomkodavsnitt som tillämpas på hela minnesraduppsättningen.

## <a name="use-user-defined-types-udt"></a>Använda användardefinierade typer: UDT
Användardefinierade typer, eller UDT, är en annan programmerabilitetsfunktion i U-SQL. U-SQL UDT fungerar som en vanlig C# användardefinierad typ. C# är ett starkt maskinskrivet språk som gör det möjligt att använda inbyggda och anpassade användardefinierade typer.

U-SQL kan inte implicit serialisera eller av serialisera godtyckliga UDT:er när UDT skickas mellan hörn i raduppsättningar. Det innebär att användaren måste ange en explicit formatter med hjälp av IFormatter-gränssnittet. Detta ger U-SQL med serialisera och av serialisera metoder för UDT.

> [!NOTE]
> U-SQL:s inbyggda utsug och utdatarar kan för närvarande inte serialisera eller av serialisera UDT-data till eller från filer även med IFormatter-uppsättningen. Så när du skriver UDT-data till en fil med OUTPUT-satsen, eller läser den med en extractor, måste du skicka den som en sträng eller byte array. Sedan kan du anropa serialisering och avserialisering kod (det vill säga UDT: s ToString() metod) uttryckligen. Användardefinierade extractors och utdatarar, å andra sidan, kan läsa och skriva UDTs.

Om vi försöker använda UDT i EXTRACTOR eller OUTPUTTER (av tidigare SELECT), som visas här:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Vi får följande felmeddelande:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Om du vill arbeta med UDT i utdatater måste vi antingen serialisera den för att stränga med metoden ToString() eller skapa en anpassad utdata.

UDT:er kan för närvarande inte användas i GROUP BY. Om UDT används i GROUP BY genereras följande fel:

```
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

* Lägg till följande namnområden:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Lägg `Microsoft.Analytics.Interfaces`till , vilket krävs för UDT-gränssnitten. Dessutom `System.IO` kan behövas för att definiera IFormatter-gränssnittet.

* Definiera en begagnad typ med Attributet SqlUserDefinedType.

**SqlUserDefinedType** används för att markera en typdefinition i en sammansättning som en användardefinierad typ (UDT) i U-SQL. Egenskaperna för attributet återspeglar UDT:s fysiska egenskaper. Det går inte att ärv den här klassen.

SqlUserDefinedType är ett obligatoriskt attribut för UDT-definition.

Konstruktören av klassen:  

* SqlUserDefinedTypeAttribute (typformatter)

* Typformatter: Obligatorisk parameter för att definiera en UDT-formatter `IFormatter` - specifikt måste typen av gränssnittet skickas här.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typisk UDT kräver också definition av IFormatter-gränssnittet, som visas i följande exempel:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Gränssnittet `IFormatter` serialiserar och av serialiserar ett objektdiagram med \<rottypen typeparamref name="T">.

\<typeparam name="T">Rottypen för objektdiagrammet att serialisera och av serialisera.

* **Deserialize**: Av-serialiserar data på den angivna strömmen och rekonstituerar grafen av objekt.

* **Serialisera**: Serialiserar ett objekt, eller diagram över objekt, med den angivna roten till den medföljande strömmen.

`MyType`instans: Instans av typen.  
`IColumnWriter`författare `IColumnReader` / läsare: Den underliggande kolumnströmmen.  
`ISerializationContext`kontext: Uppräkning som definierar en uppsättning flaggor som anger käll- eller målkontexten för strömmen under serialisering.

* **Mellanliggande**: Anger att käll- eller målkontexten inte är ett lagrat sparat.

* **Persistens**: Anger att käll- eller målkontexten är ett beständigt arkiv.

Som en vanlig C#-typ kan en U-SQL UDT-definition innehålla åsidosättningar för operatorer som +/==/!=. Det kan också innehålla statiska metoder. Om vi till exempel ska använda den här UDT som en parameter till en U-SQL MIN-mängdfunktion måste vi definiera < operatörsidosättning.

Tidigare i den här guiden visade vi ett exempel för räkenskapsperiodidentifiering från det specifika datumet i formatet `Qn:Pn (Q1:P10)`. I följande exempel visas hur du definierar en anpassad typ för räkenskapsperiodvärden.

Följande är ett exempel på ett bakomkodavsnitt med anpassat UDT- och IFormatter-gränssnitt:

```
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

Den definierade typen innehåller två tal: kvartal och månad. Operatorer `==/!=/>/<` och `ToString()` statisk metod definieras här.

Som tidigare nämnts kan UDT användas i SELECT-uttryck, men kan inte användas i OUTPUTTER/EXTRACTOR utan anpassad serialisering. Det måste antingen serialiseras som `ToString()` en sträng med eller användas med en anpassad OUTPUTTER / EXTRACTOR.

Nu ska vi diskutera användningen av UDT. I ett bakomföljande avsnitt ändrade vi vår GetFiscalPeriod-funktion till följande:

```
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

Som du kan se returnerar den värdet för vår FiscalPeriod-typ.

Här ger vi ett exempel på hur du använder den ytterligare i U-SQL-basskript. Det här exemplet visar olika former av UDT-anrop från U-SQL-skript.

```
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

Här är ett exempel på ett fullständigt avsnitt med bakomkod:

```
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
        }



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

## <a name="use-user-defined-aggregates-udagg"></a>Använd användardefinierade aggregat: UDAGG
Användardefinierade aggregat är alla aggregeringsrelaterade funktioner som inte levereras direkt med U-SQL. Exemplet kan vara en mängd för att utföra anpassade matematiska beräkningar, strängsammanfogningar, manipuleringar med strängar och så vidare.

Den användardefinierade definitionen av aggregerad basklass är följande:

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

**SqlUserDefinedAggregate** anger att typen ska registreras som ett användardefinierat aggregat. Det går inte att ärv den här klassen.

SqlUserDefinedType-attributet är **valfritt** för UDAGG-definition.


Basklassen låter dig skicka tre abstrakta parametrar: två som indataparametrar och en som resultat. Datatyperna är varierande och bör definieras under klassarv.

```
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

* **Init** anropar en gång för varje grupp under beräkning. Det ger en initieringsrutin för varje aggregeringsgrupp.  
* **Ackumuleras** utförs en gång för varje värde. Det ger huvudfunktionerna för aggregeringsalgoritmen. Den kan användas för att aggregera värden med olika datatyper som definieras under klassarv. Den kan acceptera två parametrar för variabla datatyper.
* **Avsluta** körs en gång per aggregeringsgrupp i slutet av bearbetningen för att mata ut resultatet för varje grupp.

Om du vill deklarera korrekta in- och utdatatyper använder du klassdefinitionen på följande sätt:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Första parametern att ackumulera
* T2: Andra parametern att ackumulera
* TResult: Returtyp för avsluta

Ett exempel:

```
public class GuidAggregate : IAggregate<string, int, int>
```

eller

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Använda UDAGG i U-SQL
Om du vill använda UDAGG definierar du det först i kod bakom eller refererar till den från den befintliga programmability-DLL:en som diskuterats tidigare.

Använd sedan följande syntax:

```
AGG<UDAGG_functionname>(param1,param2)
```

Här är ett exempel på UDAGG:

```
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

Och bas U-SQL-skript:

```
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

I det här användningsfallsscenariot sammanfogar vi klass-GUID:er för specifika användare.

## <a name="use-user-defined-objects-udo"></a>Använda användardefinierade objekt: UDO
U-SQL kan du definiera anpassade programmerabilitet objekt, som kallas användardefinierade objekt eller UDO.

Följande är en lista över UDO i U-SQL:

* Användardefinierade utsug
    * Extrahera rad för rad
    * Används för att implementera dataextrahering från anpassade strukturerade filer

* Användardefinierade utdatarar
    * Utmatningsrad för rad
    * Används för att mata ut anpassade datatyper eller anpassade filformat

* Användardefinierade processorer
    * Ta en rad och producera en rad
    * Används för att minska antalet kolumner eller skapa nya kolumner med värden som härleds från en befintlig kolumnuppsättning

* Användardefinierade appliers
    * Ta en rad och producera 0 till n rader
    * Används med YTTRE/KORS APPLY

* Användardefinierade kombiner
    * Kombinerar raduppsättningar - användardefinierade JOINs

* Användardefinierade reducerar
    * Ta n rader och producera en rad
    * Används för att minska antalet rader

UDO kallas vanligtvis uttryckligen i U-SQL-skript som en del av följande U-SQL-uttryck:

* EXTRACT
* Produktionen
* Process
* Kombinera
* Minska

> [!NOTE]  
> UDO: s är begränsade till att konsumera 0.5Gb minne.  Den här minnesbegränsningen gäller inte för lokala körningar.

## <a name="use-user-defined-extractors"></a>Använda användardefinierade utsug
U-SQL kan du importera externa data med hjälp av en EXTRACT-sats. Ett EXTRACT-utdrag kan använda inbyggda UDO-utsug:  

* *Extractors.Text()*: Ger extrahering från avgränsade textfiler av olika kodningar.

* *Extractors.Csv()*: Ger extrahering från CSV-filer (kommaavgränsat värde) i olika kodningar.

* *Extractors.Tsv()*: Ger extrahering från tsv-filer (tab-separated value) av olika kodningar.

Det kan vara användbart att utveckla en anpassad extractor. Detta kan vara användbart vid dataimport om vi vill utföra något av följande:

* Ändra indata genom att dela upp kolumner och ändra enskilda värden. Processorfunktionen är bättre för att kombinera kolumner.
* Tolka ostrukturerade data som webbsidor och e-postmeddelanden eller halvostrukturerade data som XML/JSON.
* Tolka data i kodning som inte stöds.

För att definiera en användardefinierad extractor, eller `IExtractor` UDE, måste vi skapa ett gränssnitt. Alla indataparametrar till utsugsutsuget, till exempel kolumn-/radavgränsning och kodning, måste definieras i konstruktorn för klassen. Gränssnittet `IExtractor` bör också innehålla en `IEnumerable<IRow>` definition för åsidosättningen enligt följande:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**Attributet SqlUserDefinedExtractor** anger att typen ska registreras som en användardefinierad extractor. Det går inte att ärv den här klassen.

SqlUserDefinedExtractor är ett valfritt attribut för UDE-definition. Den används för att definiera egenskapen AtomicFileProcessing för UDE-objektet.

* bool AtomicFileProcessing   

* **true** = Anger att denna utsugen kräver atominmatningsfiler (JSON, XML, ...)
* **false** = Indikerar att denna extractor kan hantera delade / distribuerade filer (CSV, SEQ, ...)

De viktigaste UDE-programmerbarhetsobjekten är **in-** och **utdata**. Indataobjektet används för att räkna `IUnstructuredReader`upp indata som . Utdataobjektet används för att ange utdata som ett resultat av utsugsaktiviteten.

Indata nås via `System.IO.Stream` och `System.IO.StreamReader`.

För indatakolumneruppräkning delar vi först indataströmmen med hjälp av en radavgränsare.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Sedan, ytterligare dela indataraden i kolumndelar.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

För att ställa in `output.Set` utdata använder vi metoden.

Det är viktigt att förstå att den anpassade utsugsanordningen bara matar ut kolumner och värden som definieras med utdata. Ställ in metodanrop.

```
output.Set<string>(count, part);
```

Den faktiska utdata för utsug utlöses genom anrop `yield return output.AsReadOnly();`.

Följande är utdragsexempel:

```
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

I det här användningsfallsscenariot återskapar utsugsvärdet GUID för kolumnen "guid" och konverterar värdena för kolumnen "användare" till versaler. Anpassade extractors kan ge mer komplicerade resultat genom att tolka indata och manipulera den.

Följande är grundläggande U-SQL-skript som använder en anpassad extractor:

```
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

## <a name="use-user-defined-outputters"></a>Använda användardefinierade utdatarar
Användardefinierad utdatater är en annan U-SQL UDO som låter dig utöka inbyggda U-SQL-funktioner. I likhet med utsug, det finns flera inbyggda outputters.

* *Outputters.Text()*: Skriver data till avgränsade textfiler av olika kodningar.
* *Outputters.Csv()*: Skriver data till csv-filer (kommaavgränsade värde) med olika kodningar.
* *Outputters.Tsv()*: Skriver data till TSV-filer (Tab-separated value) med olika kodningar.

Med anpassad utdata kan du skriva data i ett anpassat definierat format. Detta kan vara användbart för följande uppgifter:

* Skriva data till halvstrukturerade eller ostrukturerade filer.
* Att skriva data som inte stöds kodningar.
* Ändra utdata eller lägga till anpassade attribut.

För att definiera användardefinierade utdata, `IOutputter` måste vi skapa gränssnittet.

Följande är `IOutputter` basklassimplementeringen:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alla indataparametrar till utdatatern, till exempel kolumn-/radavgränsare, kodning och så vidare, måste definieras i konstruktorn för klassen. Gränssnittet `IOutputter` bör också innehålla `void Output` en definition för åsidosättning. Attributet `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan eventuellt ställas in för atomär filbearbetning. Mer information finns i följande information.

```
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

* `Output`kallas för varje indatarad. Den returnerar raduppsättningen. `IUnstructuredWriter output`
* Klassen Constructor används för att skicka parametrar till den användardefinierade utdataören.
* `Close`används för att eventuellt åsidosätta för att frigöra dyrt tillstånd eller avgöra när den sista raden skrevs.

**SqlUserDefinedOutputter-attributet** anger att typen ska registreras som en användardefinierad utdata. Det går inte att ärv den här klassen.

SqlUserDefinedOutputter är ett valfritt attribut för en användardefinierad utdataterdefinition. Den används för att definiera egenskapen AtomicFileProcessing.

* bool AtomicFileProcessing   

* **true** = Anger att den här utdatararen kräver atomära utdatafiler (JSON, XML, ...)
* **false** = Indikerar att denna utdatater kan hantera delade / distribuerade filer (CSV, SEQ, ...)

De viktigaste programmerbarhetsobjekten är **rad** och **utdata**. **Radobjektet** används för att räkna upp `IRow` utdata som gränssnitt. **Utdata** används för att ange utdata till målfilen.

Utdata nås via `IRow` gränssnittet. Utdata skickas en rad åt gången.

De enskilda värdena räknas upp genom att anropa Get-metoden för IRow-gränssnittet:

```
row.Get<string>("column_name")
```

Enskilda kolumnnamn kan bestämmas genom att anropa: `row.Schema`

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Med den här metoden kan du skapa en flexibel utdatater för alla metadatascheman.

Utdata skrivs till fil `System.IO.StreamWriter`med hjälp av . Parametern stream är `output.BaseStream` inställd `IUnstructuredWriter output`på som en del av .

Observera att det är viktigt att tömma databufferten till filen efter varje raditering. Dessutom måste `StreamWriter` objektet användas med attributet Disponibel aktiverat (standard) och med **nyckelordet using:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Annars anropar metoden Flush() uttryckligen efter varje iteration. Det visar vi i följande exempel.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ange sidhuvuden och sidfötter för användardefinierade utdata
Om du vill ange ett huvud använder du ett körningsflöde för en iteration.

```
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

Koden i det `if (isHeaderRow)` första blocket körs bara en gång.

För sidfoten använder du referensen `System.IO.Stream` till`output.BaseStream`förekomsten av objekt ( ). Skriv sidfoten i metoden Close(i `IOutputter` gränssnittet.  (Mer information finns i följande exempel.)

Följande är ett exempel på en användardefinierad utdata:

```
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

Och U-SQL-basskript:

```
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

Det här är en HTML-utdata, som skapar en HTML-fil med tabelldata.

### <a name="call-outputter-from-u-sql-base-script"></a>Anropa utdata från U-SQL-basskript
Om du vill anropa en anpassad utdata från det grundläggande U-SQL-skriptet måste den nya instansen av utdataobjektet skapas.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

För att undvika att skapa en förekomst av objektet i basskriptet kan vi skapa ett funktionsomslag, som visas i vårt tidigare exempel:

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

I det här fallet ser det ursprungliga samtalet ut så här:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Använda användardefinierade processorer
Användardefinierad processor, eller UDP, är en typ av U-SQL UDO som gör att du kan bearbeta inkommande rader genom att använda programmerbarhetsfunktioner. Med UDP kan du kombinera kolumner, ändra värden och lägga till nya kolumner om det behövs. I grund och botten hjälper det att bearbeta en raduppsättning för att producera nödvändiga dataelement.

För att definiera en UDP `IProcessor` måste vi `SqlUserDefinedProcessor` skapa ett gränssnitt med attributet, vilket är valfritt för UDP.

Det här gränssnittet bör `IRow` innehålla definitionen för åsidosättning av gränssnittsraduppsättningen, vilket visas i följande exempel:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** anger att typen ska registreras som en användardefinierad processor. Det går inte att ärv den här klassen.

Attributet SqlUserDefinedProcessor är **valfritt** för UDP-definition.

De viktigaste programmera objekt är **in-** och **utdata**. Indataobjektet används för att räkna upp indatakolumner och utdata och för att ange utdata som ett resultat av processoraktiviteten.

För uppräkning av indatakolumner använder vi `input.Get` metoden.

```
string column_name = input.Get<string>("column_name");
```

Parametern `input.Get` för metoden är en kolumn som `PRODUCE` skickas som `PROCESS` en del av satsen i uttrycket för U-SQL-basskriptet. Vi måste använda rätt datatyp här.

Använd metoden för `output.Set` utdata.

Det är viktigt att notera att anpassad producent endast utdatakolumner och värden som definieras med metodanropet. `output.Set`

```
output.Set<string>("mycolumn", mycolumn);
```

Den faktiska processorutdata utlöses genom att anropa `return output.AsReadOnly();`.

Följande är ett processorexempel:

```
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

I det här användningsfallsscenariot genererar processorn en ny kolumn som heter "full_description" genom att kombinera de befintliga kolumnerna – i det här fallet "användare" i versaler och "des". Den återskapar också ett GUID och returnerar de ursprungliga och nya GUID-värdena.

Som du kan se i föregående exempel kan `output.Set` du anropa C#-metoder under metodanrop.

Följande är ett exempel på grundläggande U-SQL-skript som använder en anpassad processor:

```
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

## <a name="use-user-defined-appliers"></a>Använda användardefinierade appliers
Med en U-SQL-användardefinierad applier kan du anropa en anpassad C#-funktion för varje rad som returneras av fråge yttre tabelluttryck. Den högra indata utvärderas för varje rad från den vänstra ingången, och de rader som produceras kombineras för den slutliga utdata. Listan över kolumner som produceras av operatorn APPLY är en kombination av uppsättningen kolumner i vänster och höger indata.

Användardefinierad applier anropas som en del av USQL SELECT-uttrycket.

Det typiska anropet till den användardefinierade applier ser ut så här:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Mer information om hur du använder appliers i ett SELECT-uttryck finns i [U-SQL SELECT Välja från KORSANKNKANDE OCH YTTRE APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Den användardefinierade applier basklassdefinitionen är följande:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Om du vill definiera en användardefinierad `IApplier` applier måste`SqlUserDefinedApplier`vi skapa gränssnittet med attributet [ ] som är valfritt för en användardefinierad applier-definition.

```
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

* Ansök kallas för varje rad i den yttre tabellen. Den returnerar utdataraduppsättningen. `IUpdatableRow`
* Klassen Constructor används för att skicka parametrar till den användardefinierade applier.

**SqlUserDefinedApplier** anger att typen ska registreras som en användardefinierad applier. Det går inte att ärv den här klassen.

**SqlUserDefinedApplier** är **valfritt** för en användardefinierad applier definition.


De viktigaste programmera objekt är följande:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Indataraduppsättningar skickas `IRow` som indata. Utdataraderna genereras `IUpdatableRow` som utdatagränssnitt.

Enskilda kolumnnamn kan bestämmas `IRow` genom att anropa schemametoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

För att hämta de faktiska `IRow`datavärdena från den `IRow` inkommande använder vi metoden Get() för gränssnittet.

```
mycolumn = row.Get<int>("mycolumn")
```

Eller så använder vi schemakolumnens namn:

```
row.Get<int>(row.Schema[0].Name)
```

Utdatavärdena måste `IUpdatableRow` ställas in med utdata:

```
output.Set<int>("mycolumn", mycolumn)
```

Det är viktigt att förstå att anpassade appliers endast `output.Set` utdatakolumner och värden som definieras med metodanrop.

Den faktiska utdata utlöses genom att anropa `yield return output.AsReadOnly();`.

De användardefinierade applierparametrarna kan skickas till konstruktorn. Applier kan returnera ett varierande antal kolumner som måste definieras under applier-anropet i bas-U-SQL Script.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Här är det användardefinierade applier-exemplet:

```
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

```
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

I det här användningsfallsscenariot fungerar användardefinierad applier som en kommaavgränsad värdetolk för egenskaperna för bilparken. Indatafilraderna ser ut så här:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Det är en typisk flikavgränsad TSV-fil med en egenskapskolumn som innehåller bilegenskaper som märke och modell. Dessa egenskaper måste tolkas i tabellkolumnerna. Den applier som tillhandahålls kan du också generera ett dynamiskt antal egenskaper i resultatraduppsättningen, baserat på parametern som har skickats. Du kan bara generera alla egenskaper eller en viss uppsättning egenskaper.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Den användardefinierade applier kan anropas som en ny instans av applier objekt:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Eller med åkallan av en omslagsfabriksmetod:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Använda användardefinierade kombiner
Med användardefinierad combiner, UDC, kan du kombinera rader från vänster och höger raduppsättningar, baserat på anpassad logik. Användardefinierad kom combiner används med COMBINE-uttryck.

En kombinerare anropas med COMBINE-uttrycket som ger nödvändig information om både indataraduppsättningar, grupperingskolumnerna, det förväntade resultatschemat och ytterligare information.

Om du vill anropa en kombinerare i ett bas-U-SQL-skript använder vi följande syntax:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Mer information finns i [KOMBINERA Uttryck (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

För att definiera en användardefinierad kombinerare `ICombiner` måste vi`SqlUserDefinedCombiner`skapa gränssnittet med attributet [ ] som är valfritt för en användardefinierad Combiner-definition.

Definition `ICombiner` av basklass:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Den anpassade implementeringen av ett `ICombiner` gränssnitt `IEnumerable<IRow>` bör innehålla definitionen för en Combine-åsidosättning.

```
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

**Attributet SqlUserDefinedCombiner** anger att typen ska registreras som en användardefinierad kompare. Det går inte att ärv den här klassen.

**SqlUserDefinedCombiner** används för att definiera egenskapen Combiner mode. Det är ett valfritt attribut för en användardefinierad kombinationsdefinition.

KombinerarMode-läge

CombinerMode-uppräkning kan ta följande värden:

* Fullständig (0) Varje utdatarad beror eventuellt på alla indatarader från vänster och höger med samma nyckelvärde.

* Vänster (1) Varje utdatarad beror på en enda indatarad från vänster (och eventuellt alla rader från höger med samma nyckelvärde).

* Höger (2) Varje utdatarad beror på en enda indatarad från höger (och eventuellt alla rader från vänster med samma nyckelvärde).

* Inre (3) Varje utdatarad beror på en enda indatarad från vänster och höger med samma värde.

Exempel:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


De viktigaste programmera objekt är:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Indataraduppsättningar skickas som **vänster** och **höger** `IRowset` typ av gränssnitt. Båda raduppsättningarna måste räknas upp för bearbetning. Du kan bara räkna upp varje gränssnitt en gång, så vi måste räkna upp och cachelagra det om det behövs.

I cachelagringssyfte kan vi skapa\<\> en lista T-typ av minnesstruktur som ett resultat av `IRow` en LINQ-frågekörning, särskilt List<>. Den anonyma datatypen kan också användas under uppräkningen.

Mer information om LINQ-frågor och IEnumerable\<T\> [Introduction to LINQ Queries (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) Interface finns i Introduktion till LINQ-frågor (C#) för mer information om LINQ-frågor och [I-10-kompatibel T-gränssnitt.\<\> ](/dotnet/api/system.collections.generic.ienumerable-1)

För att hämta de faktiska `IRowset`datavärdena från den `IRow` inkommande använder vi metoden Get() för gränssnittet.

```
mycolumn = row.Get<int>("mycolumn")
```

Enskilda kolumnnamn kan bestämmas `IRow` genom att anropa schemametoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Eller genom att använda schemakolumnnamnet:

```
c# row.Get<int>(row.Schema[0].Name)
```

Den allmänna uppräkningen med LINQ ser ut så här:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Efter uppräkning av båda raduppsättningarna kommer vi att slinga igenom alla rader. För varje rad i den vänstra raduppsättningen kommer vi att hitta alla rader som uppfyller villkoren för vår combiner.

Utdatavärdena måste `IUpdatableRow` ställas in med utdata.

```
output.Set<int>("mycolumn", mycolumn)
```

Den faktiska utdata utlöses `yield return output.AsReadOnly();`genom att anropa till .

Följande är ett combiner exempel:

```
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

I det här användningsfallsscenariot skapar vi en analysrapport för återförsäljaren. Målet är att hitta alla produkter som kostar mer än $ 20.000 och som säljer via webbplatsen snabbare än genom den vanliga återförsäljaren inom en viss tidsram.

Här är basen U-SQL-skript. Du kan jämföra logiken mellan en vanlig JOIN och en combiner:

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

En användardefinierad kombinare kan anropas som en ny instans av applier-objektet:

```
USING new MyNameSpace.MyCombiner();
```


Eller med åkallan av en omslagsfabriksmetod:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Använda användardefinierade reducerar

U-SQL kan du skriva anpassade raduppsättningsreducerare i C# med hjälp av det användardefinierade operatörsutökningsramverket och implementera ett IReducer-gränssnitt.

Användardefinierad reducer, eller UDR, kan användas för att eliminera onödiga rader under dataextrahering (import). Det kan också användas för att manipulera och utvärdera rader och kolumner. Baserat på programmerbarhetslogik kan den också definiera vilka rader som behöver extraheras.

För att definiera en UDR-klass `IReducer` måste vi `SqlUserDefinedReducer` skapa ett gränssnitt med ett valfritt attribut.

Det här klassgränssnittet bör `IEnumerable` innehålla en definition för åsidosättning av gränssnittsraduppsättningen.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**Attributet SqlUserDefinedReducer** anger att typen ska registreras som en användardefinierad reducer. Det går inte att ärv den här klassen.
**SqlUserDefinedReducer** är ett valfritt attribut för en användardefinierad reducerdefinition. Den används för att definiera egenskapen IsRecursive.

* bool ÄrFörsörskad    
* **sant** = Anger om denna Reducer är associativ och kommutativ

De viktigaste programmera objekt är **in-** och **utdata**. Indataobjektet används för att räkna upp indatarader. Utdata används för att ange utdatarader som ett resultat av minskad aktivitet.

För indatarader uppräkning använder `Row.Get` vi metoden.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametern för `Row.Get` metoden är en kolumn som skickas `PRODUCE` som `REDUCE` en del av klassen för uttrycket för U-SQL-basskriptet. Vi måste använda rätt datatyp här också.

Använd metoden för `output.Set` utdata.

Det är viktigt att förstå att anpassade reducer endast `output.Set` utdatavärden som definieras med metodanropet.

```
output.Set<string>("mycolumn", guid);
```

Den faktiska reducerutdata utlöses genom att anropa `yield return output.AsReadOnly();`.

Följande är ett reducerexempel:

```
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

I det här användningsfallsscenariot hoppar reducerar över rader med ett tomt användarnamn. För varje rad i raduppsättningen läser den varje obligatorisk kolumn och utvärderar sedan användarnamnets längd. Den matar ut den faktiska raden endast om användarnamnets värdelängd är mer än 0.

Följande är grundläggande U-SQL-skript som använder en anpassad reducer:

```
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
