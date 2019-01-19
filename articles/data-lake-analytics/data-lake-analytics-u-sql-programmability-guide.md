---
title: U-SQL-Programmeringsguide för Azure Data Lake
description: Läs mer om tjänsterna i Azure Data Lake Analytics som hjälper dig att skapa en molnbaserad big dataplattform.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 9ff75cbd0a4915cdf7045be9a45d11075dda15bd
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402327"
---
# <a name="u-sql-programmability-guide"></a>U-SQL-Programmeringsguide

U-SQL är ett frågespråk som har utformats för big data-typer av arbetsbelastningar. En av de unika funktionerna för U-SQL är en kombination av SQL-liknande deklarativa språk med utökningsbarhet och programmering som tillhandahålls av C#. I den här guiden kommer vi koncentrera dig på utökningsbarhet och programmering på U-SQL-språket som aktiveras av C#.

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

Det här skriptet definierar två raduppsättningar: `@a` och `@results`. Raduppsättningen `@results` definieras från `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typer och uttryck i U-SQL-skript

Ett U-SQL-uttryck är ett C#-uttryck som kombineras med logiska U-SQL-åtgärder, till exempel `AND`, `OR`, och `NOT`. U-SQL-uttryck kan användas med särskilda, EXTRAHERA, där, med, gruppera efter och DEKLARERA. Följande skript tolkar till exempel en sträng som ett datum/tid-värde.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Följande kodavsnitt tolkar en sträng som datum/tid-värde i en DECLARE-sats.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Använda C#-uttryck för datatypkonverteringar

I följande exempel visar hur du kan göra en omvandling för datetime-data med hjälp av C#-uttryck. I det här scenariot omvandlas datetime strängdata till standard datetime i midnatt 00:00:00 tid-format.

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

### <a name="use-c-expressions-for-todays-date"></a>Använda C#-uttryck för dagens datum

Om du vill dra dagens datum, kan vi använda följande C#-uttryck: `DateTime.Now.ToString("M/d/yyyy")`

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
## <a name="using-net-assemblies"></a>Med hjälp av .NET-sammansättningar

Modell för utökningsbarhet i U-SQL beroende kraftigt möjligheten att lägga till anpassad kod från .NET-sammansättningar. 

### <a name="register-a-net-assembly"></a>Registrera en .NET-sammansättning

Använd den `CREATE ASSEMBLY` -uttrycket för att placera en .NET-sammansättning till en U-SQL-databas. Därefter U-SQL-skript kan använda dessa sammansättningar med hjälp av den `REFERENCE ASSEMBLY` instruktionen. 

Följande kod visar hur du registrerar en sammansättning:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Följande kod visar hur du refererar till en sammansättning:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Läs den [registrering monteringsanvisningar](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) som omfattar det här avsnittet i större detalj.


### <a name="use-assembly-versioning"></a>Använda sammansättningen versionshantering
För närvarande använder U-SQL .NET Framework version 4.5. Kontrollera att din egen sammansättningar är kompatibla med den versionen av plattformen.

Som tidigare nämnts, U-SQL körs koden i ett 64-bitars (x 64)-format. Så se till att din kod kompileras om du vill köra på x64. Annars kan du få felaktigt format-fel som visades tidigare.

Var och en överförs sammansättningen DLL-filen och resursfil, t.ex en annan runtime, en intern sammansättning eller en konfigurationsfil får vara högst 400 MB. Den totala storleken på distribuerade resurser, antingen via DISTRIBUERA RESURSEN eller via referenser till sammansättningar och deras ytterligare filer får inte överskrida 3 GB.

Observera slutligen att varje U-SQL-databas får bara innehålla en version av alla angivna sammansättningen. Om du behöver både version 7 och version 8 av NewtonSoft Json.Net-biblioteket, måste du registrera dem i två olika databaser. Varje skript kan bara hänvisa till en version av en viss sammansättningen DLL-filen. I detta avseende följer U-SQL C# sammansättningen hanterings- och versionshantering semantiken.

## <a name="use-user-defined-functions-udf"></a>Använda användardefinierade funktioner: UDF
U-SQL-användardefinierade funktioner eller UDF, programmerar rutiner som accepterar parametrar, utföra en åtgärd (till exempel en komplicerad beräkning) och returnera resultatet av den åtgärden som ett-värde. Returvärdet för UDF kan endast vara en enskild skalär. U-SQL-UDF kan anropas i grundläggande U-SQL-skript som andra C# skalära funktioner.

Vi rekommenderar att du initiera U-SQL-användardefinierade funktioner som **offentliga** och **statiska**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Första ska vi titta på enkelt exempel för att skapa en UDF.

Vi måste fastställa räkenskapsårets period, inklusive räkenskapskvartal och räkenskapsmånad på den första inloggningen för den specifika användaren i den här användningsfall. Den första räkenskapsmånad på år i vårt scenario är juni.

För att beräkna räkenskapsårets period, introducerar vi följande C#-funktion:

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

Bara beräknar räkenskapsmånad och kvartal och returnerar ett strängvärde. Juni används den första månaden för den första räkenskapskvartal ”Q1:P1”. Vi använder ”Q1:P2” för juli och så vidare.

Det här är en vanlig C#-funktion som vi ska använda i vår U-SQL-projekt.

Här är utseendet för avsnittet bakomliggande kod i det här scenariot:

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

Nu det dags att anropa den här funktionen från grundläggande U-SQL-skriptet. Detta gör behöver vi ett fullständigt kvalificerat namn för funktionen, inklusive namnområdet, som i det här fallet är NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Följande är det faktiska grundläggande U-SQL-skriptet:

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

Nedan följer utdatafilen för körningen av skriptet:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Det här exemplet visar en enkel användning av infogade UDF i U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Behåll tillstånd mellan UDF-anrop
U-SQL C#-programmering objekt kan vara mer sofistikerade, använda interaktivitet via de globala variablerna bakomliggande kod. Låt oss titta på följande användningsfall affärsscenariot.

Användare kan växla mellan av interna program i stora organisationer. Det kan vara Microsoft Dynamics CRM, Power BI och så vidare. Kunder kan också använda en telemetri-analys av hur användare växla mellan olika program, vilka trender för användning är, och så vidare. Målet för företaget är att optimera användningen av programmet. De kanske också vill kombinera olika program eller specifika inloggnings-rutiner.

För att åstadkomma detta måste vi fastställa sessions-ID: N och fördröjning mellan den senaste sessionen som inträffat.

Vi behöver hitta en föregående inloggning och sedan tilldela den här inloggning till alla sessioner som skapas för samma program. Den första kontrollen är att grundläggande U-SQL-skript inte kan vi använda beräkningar över redan beräknade kolumner med funktionen LAG. Andra utmaningen är att vi behöver hålla den specifika sessionen för alla sessioner inom samma tidsperiod.

Lös problemet, använder vi en global variabel inuti en bakomliggande kod: `static public string globalSession;`.

Den globala variabeln används på hela raduppsättningen under vår skriptkörningen.

Här är den bakomliggande kod delen av vårt U-SQL-program:

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

Det här exemplet visar den globala variabeln `static public string globalSession;` används inuti den `getStampUserSession` funktionen och få startas varje gång parametern Session ändras.

Grundläggande U-SQL-skriptet är följande:

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

Funktionen `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` anropas här under andra minne raduppsättningen beräkningen. Den skickar den `UserSessionTimestamp` kolumn och returnerar värdet tills `UserSessionTimestamp` har ändrats.

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

Det här exemplet visar en mer komplicerad användningsfall där vi använder en global variabel i en bakomliggande kod avsnittet som gäller för hela minne raduppsättningen.

## <a name="use-user-defined-types-udt"></a>Använda användardefinierade typer: UDT
Användardefinierade typer eller UDT, är en annan funktion för programmering i U-SQL. U-SQL-UDT fungerar som en vanlig C#-användardefinierade typen. C# är en starkt typifierad språk som tillåter användning av inbyggda och anpassade användardefinierade typer.

U-SQL kan inte implicit serialisera eller deserialisera godtyckliga UDT-typer när UDT skickas mellan hörn i raduppsättningar. Det innebär att användaren måste ange ett explicit Formateraren IFormatter-gränssnittet. Detta ger U-SQL med serialiserat och deserialisera metoder för UDT.

> [!NOTE]
> U-SQL: s inbyggda extraktorer och utdatafunktioner kan inte för närvarande serialisera eller deserialisera UDT-data till eller från filer även med IFormatter. När du skriver UDT-data till en fil med utdata-instruktion, eller läsa med en extraktor, måste du godkänna det som en sträng eller byte-matris. Sedan anropa serialisering och deserialiseringsfel uttryckligen kod (det vill säga de UDT toString ()-metod). Användardefinierade extraktorer och utdatafunktioner, å andra sidan kan läsa och skriva UDT-typer.

Om vi försöker använda UDT i EXTRAKTOR eller OUTPUTTER (av tidigare Välj), som visas här:

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

Om du vill arbeta med UDT i outputter, har vi antingen att serialisera det för att med metoden toString () eller skapa en anpassad outputter.

UDT-typer kan för närvarande inte användas i GROUP BY. Om UDT används i GROUP BY, genereras följande fel:

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

För att definiera en användardefinierad typ, måste vi:

* Lägg till följande namnrymder:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Lägg till `Microsoft.Analytics.Interfaces`, vilket krävs för UDT-gränssnitt. Dessutom `System.IO` kan behövas för att definiera IFormatter-gränssnittet.

* Definiera en användardefinierade typ med SqlUserDefinedType attribut.

**SqlUserDefinedType** används för att markera en typdefinition i en sammansättning som en användardefinierad typ (UDT) i U-SQL. Egenskaper för attributet återspeglar UDT fysiska egenskaper. Den här klassen kan ärvas.

SqlUserDefinedType är ett obligatoriskt attribut för UDT-definition.

Konstruktor i klassen:  

* SqlUserDefinedTypeAttribute (typ Formateraren)

* Skriv Formateraren: Obligatoriska parametern att definiera en UDT-formaterare--mer specifikt typ av den `IFormatter` gränssnittet måste skickas här.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Vanliga UDT kräver också definitionen av gränssnittet IFormatter, som visas i följande exempel:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Den `IFormatter` gränssnittet Serialiserar och ta bort Serialiserar ett objektdiagram med rot-typen av \<typeparamref name = ”T” >.

\<typeparam name = ”T” > rottyp för objektdiagrammet serialisera och deserialisera.

* **Deserialize**: Ta bort Serialiserar data på den angivna dataströmmen och reconstitutes diagram över objekt.

* **Serialize**: Serialiserar ett objekt eller ett diagram över objekt med den angivna roten till den angivna dataströmmen.

`MyType` Instans: Instans av typen.  
`IColumnWriter` skrivaren / `IColumnReader` läsare: Den underliggande kolumn dataströmmen.  
`ISerializationContext` Kontext: Uppräkning som definierar en uppsättning flaggor som anger käll- eller målservrar kontext för dataströmmen under serialiseringen.

* **Mellanliggande**: Anger att käll- eller målservrar kontexten inte är en bestående butik.

* **Persistence**: Anger att käll- eller målservrar kontexten är en bestående butik.

Som en vanlig C#-typ, en U-SQL-UDT-definition kan innehålla åsidosättningar för operatörer som +/ == /! =. Den kan även innehålla statiska metoderna. Till exempel om vi ska använda den här UDT som en parameter för en mängdfunktion för MIN U-SQL, vi måste definiera < operator åsidosättning.

Tidigare i den här guiden kommer vi visas ett exempel för räkenskapsårets period identifiering från ett visst datum i formatet `Qn:Pn (Q1:P10)`. I följande exempel visas hur du definierar en anpassad typ för räkenskapsårets tröskelperiodvärden.

Nedan följer ett exempel på en bakomliggande kod avsnitt med anpassade UDT och IFormatter gränssnitt:

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

Den angivna typen innehåller två tal: kvartal och månader. Operatörer `==/!=/>/<` och statisk metod `ToString()` definieras här.

Såsom nämnts tidigare UDT kan användas i SELECT-uttryck, men kan inte användas i OUTPUTTER/EXTRAKTOR utan anpassad serialisering. Det har antingen serialiseras som en sträng med `ToString()` eller användas med en egen OUTPUTTER/EXTRAKTOR.

Nu ska vi diskutera användningen av UDT. I en bakomliggande kod i avsnittet ändrat vi våra GetFiscalPeriod funktionen så här:

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

Som du ser returnerar värdet för vår FiscalPeriod-typen.

Här har ett exempel på hur du använder dem ytterligare i grundläggande U-SQL-skript. Det här exemplet visar olika former av UDT-anrop från U-SQL-skript.

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

Här är ett exempel på en fullständig bakgrundskod avsnitt:

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

## <a name="use-user-defined-aggregates-udagg"></a>Använda användardefinierade aggregeringar: UDAGG
Användardefinierade aggregeringar är aggregering-relaterade funktioner som inte levereras out-of the box med U-SQL. Exemplet kan vara en aggregering att utföra anpassad matematiska beräkningar, sträng sammanfogningar, ändringar i strängar och så vidare.

Användardefinierade sammanställd basklass definitionen är följande:

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

**SqlUserDefinedAggregate** indikerar att typen ska registreras som en användardefinierad samling. Den här klassen kan ärvas.

SqlUserDefinedType attributet är **valfria** för UDAGG definition.


Basklassen kan du skicka tre abstrakta parametrar: två som indataparametrar och en som ett resultat. Datatyperna som kan variera och ska definieras under klassarv.

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

* **Init** anropar en gång för varje grupp under beräkning. Det ger en initieringen för varje grupp med aggregering.  
* **Ackumuleras** körs en gång för varje värde. Den innehåller de flesta funktioner för algoritmen aggregering. Det kan användas för att aggregerade värden med olika datatyper som definieras under klassarv. Använder två parametrar av varierande datatyper.
* **Avsluta** körs en gång per aggregering grupp i slutet av bearbetning till utdata resultatet för varje grupp.

För att deklarera rätta indata och utdata-datatyperna, använder du klassdefinitionen enligt följande:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Den första parametern i ackumuleras
* T2: Den första parametern i ackumuleras
* TResult: Returtypen för Avsluta

Exempel:

```
public class GuidAggregate : IAggregate<string, int, int>
```

eller

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Använd UDAGG i U-SQL
Om du vill använda UDAGG, definiera den i bakomliggande kod eller referera till den från den befintliga programmering DLL som beskrivits tidigare.

Sedan använder du följande syntax:

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

Och grundläggande U-SQL-skript:

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

I den här användningsfall sammanfoga vi klass GUID för specifika användare.

## <a name="use-user-defined-objects-udo"></a>Använda användardefinierade objekt: UDO
U-SQL kan du definiera anpassade programmering objekt, som kallas användardefinierade objekt eller UDO.

Här följer en lista över UDO i U-SQL:

* Anpassade extraktorer
    * Extrahera rad för rad
    * Används för att implementera extrahering av data från anpassade strukturerade filer

* Användardefinierade utdatafunktioner
    * Utdata rad för rad
    * Utdatatyper för anpassade data eller en anpassad fil används format

* Användardefinierade processorer
    * Tar en rad och skapar en rad
    * Används för att minska antalet kolumner eller skapa nya kolumner med värden som härleds från en befintlig kolumn

* Användardefinierade appliers
    * Tar en rad och skapar 0 till n rader
    * Används med yttre/CROSS APPLY

* Användardefinierade combiners
    * Kombinerar raduppsättningar--användardefinierade kopplingar

* Användardefinierade reducerare
    * Tar n rader och skapar en rad
    * Används för att minska antalet rader

UDO kallas vanligtvis uttryckligen i U-SQL-skript som en del av följande U-SQL-uttryck:

* EXTRAHERA
* OUTPUT
* PROCESS
* KOMBINERA
* MINSKA

> [!NOTE]  
> UDOS begränsas för att använda 0,5 Gb minne.  Den här minne begränsningen gäller inte för lokala körningar.

## <a name="use-user-defined-extractors"></a>Använda anpassade extraktorer
U-SQL kan du importera externa data med hjälp av en EXTRACT-instruktion. En EXTRACT-instruktion kan använda inbyggda UDO extraktorer:  

* *Extractors.Text()*: Innehåller extrahering från avgränsade textfiler i olika kodningar.

* *Extractors.Csv()*: Innehåller extrahering från kommaavgränsade värden (CSV)-filer för olika kodningar.

* *Extractors.Tsv()*: Innehåller extrahering från fliken med kommaseparerade värden (TVS) filer med olika kodningar.

Det kan vara praktiskt att utveckla en egen extraktor. Det kan vara användbart vid dataimport om vi vill göra något av följande uppgifter:

* Ändra indata genom att dela kolumner och ändra enskilda värden. PROCESSOR-funktioner är bättre om du vill kombinera kolumner.
* Parsa Ostrukturerade data, till exempel webbsidor och e-postmeddelanden eller delvis Ostrukturerade data, till exempel XML/JSON.
* Dela upp data från kodning stöds inte.

För att definiera en användardefinierad extraktor eller Undanta, måste vi skapa en `IExtractor` gränssnitt. Alla indataparametrar till extraktor, till exempel kolumn/rad avgränsare och kodning, måste definieras i konstruktorn i klassen. Den `IExtractor` gränssnitt bör också innehålla en definition för det `IEnumerable<IRow>` åsidosätta på följande sätt:

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

Den **SqlUserDefinedExtractor** attributet anger att typen ska registreras som en användardefinierad extraktor. Den här klassen kan ärvas.

SqlUserDefinedExtractor är ett valfritt attribut för UDERA definition. Den används för att definiera AtomicFileProcessing-egenskapen för UDERA-objekt.

* bool AtomicFileProcessing   

* **SANT** = anger att den här extraktor kräver atomiska indatafilerna (JSON, XML,...)
* **FALSKT** = anger den här extraktor kan hantera delade / distribuerade filer (CSV, SEQ,...)

De huvudsakliga UDERA programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp indata som `IUnstructuredReader`. Objektet används för att ställa in datautflödet till följd av aktiviteten extraktor.

Indata är tillgängligt via `System.IO.Stream` och `System.IO.StreamReader`.

För indatakolumner uppräkningen dela vi först Indataströmmen med hjälp av en Radavgränsare.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Sedan ytterligare delats inkommande rad kolumn.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Om du vill ställa in datautflödet, använder vi den `output.Set` metoden.

Det är viktigt att förstå att egen extraktor endast matar ut kolumner och värden som definieras med utdata. Ange metodanrop.

```
output.Set<string>(count, part);
```

Faktiska extraktor utdata utlöses genom att anropa `yield return output.AsReadOnly();`.

Följande är extraktor-exempel:

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

I den här användningsfall extraktor återskapar GUID för ”guid” kolumn och konverterar värdena i kolumnen ”användare” till versal. Anpassade extraktorer kan ge mer komplicerad resultat genom att parsa indata och med den.

Följande är grundläggande U-SQL-skript som använder en egen extraktor:

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

## <a name="use-user-defined-outputters"></a>Använda användardefinierade utdatafunktioner
Användardefinierade outputter är en annan U-SQL-UDO som gör det möjligt att utöka inbyggda funktioner för U-SQL. Liknar extraktor, det finns flera inbyggda utdatafunktioner.

* *Outputters.Text()*: Skriver data till avgränsade textfiler i olika kodningar.
* *Outputters.Csv()*: Skriver data till fil med kommaavgränsade värden (CSV) filer med olika kodningar.
* *Outputters.Tsv()*: Skriver data till filer tabbavgränsade värden (TVS) av olika kodningar.

Anpassade outputter kan du skriva data i ett anpassat definierade format. Detta kan vara användbart för följande uppgifter:

* Skriver data till delvis strukturerade eller Ostrukturerade filer.
* Skrivning av data inte stöd för kodningar.
* Ändra utdata eller lägga till anpassade attribut.

För att definiera en användardefinierad outputter, måste vi skapa den `IOutputter` gränssnitt.

Följande är grundläggande `IOutputter` implementeringen av klassen:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alla indataparametrar till outputter som kolumn/rad avgränsare, kodning och så vidare, måste definieras i konstruktorn i klassen. Den `IOutputter` gränssnitt bör också innehålla en definition för `void Output` åsidosätta. Attributet `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan du kan också anges för atomiska filbearbetning. Mer information finns i följande information.

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

* `Output` kallas för varje inkommande rad. Returnerar den `IUnstructuredWriter output` raduppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade outputter.
* `Close` används för att du kan också åsidosätta för att frigöra dyra tillstånd eller fastställa när den sista raden har skrivits.

**SqlUserDefinedOutputter** attributet anger att typen ska registreras som en användardefinierad outputter. Den här klassen kan ärvas.

SqlUserDefinedOutputter är ett valfritt attribut för en definition av en användardefinierad outputter. Den används för att definiera egenskapen AtomicFileProcessing.

* bool AtomicFileProcessing   

* **SANT** = anger att den här outputter kräver atomiska utdatafilerna (JSON, XML,...)
* **FALSKT** = anger den här outputter kan hantera delade / distribuerade filer (CSV, SEQ,...)

De huvudsakliga programmering objekten är **rad** och **utdata**. Den **rad** objektet används för att räkna upp utdata som `IRow` gränssnitt. **Utdata** används för att ställa in datautflödet till målfilen.

Utdata är tillgängligt via den `IRow` gränssnitt. Utdata skickas en rad i taget.

Enskilda värden räknas upp genom att anropa Get-metoden i gränssnittet IRow:

```
row.Get<string>("column_name")
```

Enskilda kolumnnamn kan fastställas genom att anropa `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Den här metoden kan du skapa en flexibel outputter för alla metadata-schemat.

Utdata som skrivs till filen med hjälp av `System.IO.StreamWriter`. Stream-parametern anges till `output.BaseStream` som en del av `IUnstructuredWriter output`.

Observera att det är viktigt att tömma Databufferten för filen efter varje iteration av raden. Dessutom kan den `StreamWriter` objekt måste användas med disponibla attributet aktiverad (standard) och med den **med** nyckelord:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Annars kan anropa uttryckligen Flush() metoden efter varje iteration. Detta visar vi i följande exempel.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ställ in sidhuvuden och sidfötter för användardefinierade outputter
Använd enkel iteration körning flödet för att ange en rubrik.

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

Koden i först `if (isHeaderRow)` blocket körs bara en gång.

För sidfotsetiketten använder du referensen till instansen av `System.IO.Stream` objekt (`output.BaseStream`). Skriva sidfoten i metoden Close() i den `IOutputter` gränssnitt.  (Mer information finns i följande exempel.)

Nedan följer ett exempel på en användardefinierad outputter:

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

Och grundläggande U-SQL-skript:

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

Det här är en HTML-outputter, vilket skapar en HTML-fil med tabelldata.

### <a name="call-outputter-from-u-sql-base-script"></a>Anropa outputter från grundläggande U-SQL-skript
För att anropa en anpassad outputter från grundläggande U-SQL-skriptet, har den nya instansen av outputter-objekt som ska skapas.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Om du vill undvika att skapa en instans av objektet i basskriptet, kan vi skapa en funktion-omslutning enligt vårt tidigare exempel:

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

I det här fallet det ursprungliga anropet ser ut som följande:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Använda användardefinierade processorer
Användardefinierade är UDP, en typ av U-SQL-UDO som gör det möjligt att bearbeta inkommande rader genom att använda programmeringsfunktioner. UDP kan du kombinera kolumner, ändra värden och lägga till nya kolumner om det behövs. I praktiken, hjälper det för att bearbeta en raduppsättning för att skapa nödvändiga dataelement.

För att definiera en UDP, måste vi skapa en `IProcessor` gränssnitt med den `SqlUserDefinedProcessor` attribut, som är valfritt för UDP.

Det här gränssnittet ska innehålla definitionen för den `IRow` gränssnittet raduppsättningen åsidosätta, enligt följande exempel:

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

**SqlUserDefinedProcessor** indikerar att typen ska registreras som en användardefinierad. Den här klassen kan ärvas.

Attributet SqlUserDefinedProcessor **valfritt** för UDP-definition.

De huvudsakliga programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp kolumner som indata och utdata och för att ställa in datautflödet till följd av processoraktiviteten.

Indatakolumnerna-uppräkningen, använder vi den `input.Get` metoden.

```
string column_name = input.Get<string>("column_name");
```

Parametern för `input.Get` metoden är en kolumn som skickas som en del av den `PRODUCE` -satsen i den `PROCESS` av grundläggande U-SQL-skriptet. Vi måste du använda rätt datatyp här.

Utdata kan använda den `output.Set` metoden.

Det är viktigt att Observera att anpassade producent returnerar endast kolumner och värden som definieras med den `output.Set` metodanrop.

```
output.Set<string>("mycolumn", mycolumn);
```

Faktiska utdata utlöses genom att anropa `return output.AsReadOnly();`.

Följande är en processor-exempel:

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

I den här användningsfall genererar processorn en ny kolumn med namnet ”full_description” genom att kombinera befintliga kolumner – i det här fallet ”användare” i versaler och ”des”. Den genererar ett GUID och returnerar de ursprungliga och nya GUID-värdena.

Som du ser i föregående exempel kan du anropa metoder C# under `output.Set` metodanrop.

Nedan följer ett exempel på grundläggande U-SQL-skript som använder en anpassad processor:

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
Ett U-SQL-användardefinierade applier kan du anropa en anpassad C#-funktion för varje rad som returneras av det yttre tabelluttrycket av en fråga. Den högra indatasporten utvärderas för varje rad från den vänstra indataporten och de rader som produceras kombineras för det slutgiltiga resultatet. Listan över kolumner som genereras av APPLY-operatorn är en kombination av en uppsättning kolumner i vänstra och högra indatasporten.

Användardefinierade applier anropas som en del av USQL Välj-uttryck.

Vanliga anropet till den användardefinierade applier ser ut som följande:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Läs mer om hur du använder appliers i ett väljer uttryck [U-SQL väljer att välja från CROSS APPLY och OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Den användardefinierade applier basklass-definitionen är följande:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

För att definiera en användardefinierad applier, måste vi skapa den `IApplier` gränssnitt med den [`SqlUserDefinedApplier`] attribut, som är valfritt för en användardefinierad applier definition.

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

* Tillämpa anropas för varje rad i tabellen yttre. Returnerar den `IUpdatableRow` utdata raduppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade applier.

**SqlUserDefinedApplier** indikerar att typen ska registreras som en användardefinierad applier. Den här klassen kan ärvas.

**SqlUserDefinedApplier** är **valfritt** användardefinierade applier definieras.


De huvudsakliga programmering objekten är följande:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Inkommande raduppsättningar skickas som `IRow` indata. Utdata-rader genereras som `IUpdatableRow` -gränssnitt.

Enskilda kolumnnamn kan fastställas genom att anropa den `IRow` Schema-metoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Att hämta värden för faktiska data från inkommande `IRow`, vi använda metoden Get() i `IRow` gränssnitt.

```
mycolumn = row.Get<int>("mycolumn")
```

Eller använder vi kolumnnamnet schema:

```
row.Get<int>(row.Schema[0].Name)
```

Utdatavärden måste anges med `IUpdatableRow` utdata:

```
output.Set<int>("mycolumn", mycolumn)
```

Det är viktigt att förstå att anpassade appliers endast utdata kolumner och värden som definieras med `output.Set` metodanrop.

Faktiska utdata utlöses genom att anropa `yield return output.AsReadOnly();`.

Användardefinierade applier parametrar kan skickas till konstruktorn. Applier kan returnera ett variabelt antal kolumner som måste definieras under applier samtalet i grundläggande U-SQL-skript.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Här är en användardefinierad applier exempel:

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

Följande är grundläggande U-SQL-skriptet för den här användardefinierade applier:

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

I den här användningsfall fungerar användardefinierade applier som en kommaavgränsad parser för bil vagnpark egenskaper. Indatafilen rader ut så här:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Det är en typisk tabbavgränsad TSV-fil med en kolumn för egenskaper som innehåller bil egenskaper, till exempel märke och modell. Dessa egenskaper måste parsas till tabellkolumnerna. Applier som har angetts kan du skapa dynamiska flera egenskaper i resultatet raduppsättningen, baserat på den parameter som skickas. Du kan skapa alla egenskaper eller en specifik uppsättning enbart egenskaper.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Den användardefinierade applier kan anropas som en ny instans av applier objekt:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Eller med av en standardmetod för omslutning:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Använda användardefinierade combiners
Användardefinierade combiner eller UDC, kan du kombinera rader från vänster och höger raduppsättningar, baserat på anpassad logik. Användardefinierade combiner används med KOMBINERA uttryck.

En combiner anropas med KOMBINERA-uttryck som ger nödvändig information om både den inkommande raduppsättningar, grupperade kolumner, förväntat resultat schemat och ytterligare information.

För att anropa en combiner i ett grundläggande U-SQL-skript kan använda vi följande syntax:

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

Mer information finns i [KOMBINERA uttryck (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

För att definiera en användardefinierad combiner, måste vi skapa den `ICombiner` gränssnitt med den [`SqlUserDefinedCombiner`] attribut, som är valfritt för en användardefinierad Combiner definition.

Base `ICombiner` klassdefinitionen:

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

Anpassade implementeringen av en `ICombiner` gränssnitt ska innehålla definitionen för en `IEnumerable<IRow>` kombinera åsidosättning.

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

Den **SqlUserDefinedCombiner** attributet anger att typen ska registreras som en användardefinierad combiner. Den här klassen kan ärvas.

**SqlUserDefinedCombiner** används för att definiera egenskapen Combiner läge. Det är ett valfritt attribut för en definition av en användardefinierad combiner.

CombinerMode läge

CombinerMode enum kan ha följande värden:

* Fullständig (0) varje rad i utdata potentiellt beror på alla inkommande rader från vänster och höger med samma nyckelvärde.

* Vänster (1) varje rad i utdata är beroende av en rad från vänster (och eventuellt alla rader från höger med samma nyckelvärde) som indata.

* Höger (2) varje rad i utdata är beroende av en rad från höger (och eventuellt alla rader från vänster med samma nyckelvärde) som indata.

* Inre (3) varje rad i utdata är beroende av en enskild inkommande rad från vänster och höger med samma värde.

Exempel: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


De huvudsakliga programmering objekt är:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Inkommande raduppsättningar skickas som **vänstra** och **rätt** `IRowset` typ av gränssnitt. Båda raduppsättningar måste räknas upp för bearbetning. Du kan bara räkna upp varje gränssnitt en gång, så att vi behöver räkna upp och cachelagra dem. Om det behövs.

För cachelagring, kan vi skapa en lista\<T\> typ av minne struktur som ett resultat av en LINQ Frågekörningen, särskilt lista <`IRow`>. Anonym datatypen kan användas under uppräkning samt.

Se [introduktion till LINQ-frågor (C#)](https://msdn.microsoft.com/library/bb397906.aspx) för mer information om LINQ-frågor och [IEnumerable\<T\> gränssnittet](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) mer information om IEnumerable\<T\> gränssnitt.

Att hämta värden för faktiska data från inkommande `IRowset`, vi använda metoden Get() i `IRow` gränssnitt.

```
mycolumn = row.Get<int>("mycolumn")
```

Enskilda kolumnnamn kan fastställas genom att anropa den `IRow` Schema-metoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Eller genom att använda kolumnen schemanamnet:

```
c# row.Get<int>(row.Schema[0].Name)
```

Allmän uppräkning med LINQ ser ut som följande:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Efter att räkna upp både raduppsättningar, ska vi gå igenom alla rader. För varje rad i den vänstra raduppsättningen vi hitta alla rader som uppfyller villkoret för vår combiner.

Utdatavärden måste anges med `IUpdatableRow` utdata.

```
output.Set<int>("mycolumn", mycolumn)
```

Faktiska utdata utlöses genom att anropa till `yield return output.AsReadOnly();`.

Nedan följer ett exempel på combiner:

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

I den här användningsfall bygger vi en analysrapporten för återförsäljaren. Målet är att hitta alla produkter som kostar mer än 20 000 och som sälj via webbplatsen för snabbare än via vanlig återförsäljare inom ett bestämt tidsintervall.

Här är det grundläggande U-SQL-skriptet. Du kan jämföra logiken mellan en vanlig koppling och en combiner:

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

Du kan anropa en användardefinierad combiner som en ny instans av objektet applier:

```
USING new MyNameSpace.MyCombiner();
```


Eller med av en standardmetod för omslutning:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Använda användardefinierade reducerare

U-SQL kan du skriva anpassad raduppsättningen reducerare i C# med hjälp av ramverket användardefinierade operatorn utökningsbarhet och implementera ett IReducer-gränssnitt.

Användardefinierade reducer eller UDR, kan användas för att eliminera onödiga raderna under extrahering av data (import). Det kan också användas att manipulera och utvärdera rader och kolumner. Baserat på logik för programmering, kan det också definiera vilka rader behöver ska extraheras.

För att definiera en UDR-klass, måste vi skapa en `IReducer` gränssnitt med en valfri `SqlUserDefinedReducer` attribut.

Den här klassgränssnitt bör innehålla en definition för det `IEnumerable` gränssnittet raduppsättningen åsidosätta.

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

Den **SqlUserDefinedReducer** attributet anger att typen ska registreras som en användardefinierad reducer. Den här klassen kan ärvas.
**SqlUserDefinedReducer** är ett valfritt attribut för en definition av en användardefinierad reducer. Den används för att definiera IsRecursive egenskapen.

* bool IsRecursive    
* **SANT** = anger om den här Reducer är kopplat och kommutativa

De huvudsakliga programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp indatarader. Utdata används för att ange utdata rader på grund av att minska aktivitet.

Indatarader-uppräkningen, använder vi den `Row.Get` metoden.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametern för den `Row.Get` metoden är en kolumn som skickas som en del av den `PRODUCE` klassen för den `REDUCE` av grundläggande U-SQL-skriptet. Vi behöver använda rätt datatyp så här.

Utdata kan använda den `output.Set` metoden.

Det är viktigt att förstå den anpassade reducer endast matar ut värden som definieras med den `output.Set` metodanrop.

```
output.Set<string>("mycolumn", guid);
```

Faktiska reducer utdata utlöses genom att anropa `yield return output.AsReadOnly();`.

Nedan följer ett exempel på reducer:

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

I den här användningsfall reducer hoppar över rader med ett tomt användarnamn. Den läser varje obligatorisk kolumn för varje rad i raduppsättningen sedan utvärderar längden på användarnamnet. Den visar den faktiska raden bara om värdet för användarnamnet är större än 0.

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
