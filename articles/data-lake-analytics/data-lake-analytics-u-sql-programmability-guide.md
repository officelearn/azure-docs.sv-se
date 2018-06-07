---
title: U-SQL-programmering guide för Azure Data Lake
description: Mer information om en uppsättning tjänster i Azure Data Lake Analytics att skapa en molnbaserad stordataplattform.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 9ebbecc22acf4be007672f3b52d30f0fec32a47d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623680"
---
# <a name="u-sql-programmability-guide"></a>U-SQL-programmering guide

U-SQL är ett frågespråk som är utformad för stora datatypen för arbetsbelastningar. En av de unika funktionerna i U-SQL är en kombination av SQL-liknande deklarativ språk med utökningsbarhet och programmering som tillhandahålls av C#. I den här handboken inriktas på utökningsbarhet och U-SQL-språket som är aktiverad som C#-programmering.

## <a name="requirements"></a>Krav

Hämta och installera [Azure Data Lake-verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

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

Ett U-SQL-uttryck är ett C#-uttryck i kombination med U-SQL logiska operationer sådana `AND`, `OR`, och `NOT`. U-SQL-uttryck kan användas med SELECT-, EXTRAHERA, där, med, gruppera efter och DEKLARERA. Följande skript Parsar till exempel en sträng som ett DateTime-värde.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Följande kodavsnitt Parsar en sträng som DateTime-värde i en DECLARE-sats.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Använd C#-uttryck för datatypkonverteringar

Exemplet nedan visar hur du kan göra en konvertering för datetime-data med hjälp av C#-uttryck. I det här scenariot konverteras datetime strängdata till standard datetime med Tidformatet för midnatt 00:00:00.

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

Hämta dagens datum, använder vi följande C#-uttryck: `DateTime.Now.ToString("M/d/yyyy")`

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

U-SQL-modellen för utökning beroende kraftigt möjligheten att lägga till anpassad kod från .NET-sammansättningar. 

### <a name="register-a-net-assembly"></a>Registrera en .NET-sammansättning

Använd den `CREATE ASSEMBLY` -instruktionen för att placera en .NET-sammansättning i en U-SQL-databas. Därefter kan U-SQL-skript kan använda dessa sammansättningar med hjälp av den `REFERENCE ASSEMBLY` instruktionen. 

Följande kod visar hur du registrerar en sammansättning:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Följande kod visar hur du refererar till en sammansättning:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Läs den [sammansättningen instruktioner](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) som omfattar det här avsnittet i detalj.


### <a name="use-assembly-versioning"></a>Använd sammansättningen versionshantering
U-SQL används för närvarande, .NET Framework version 4.5. Kontrollera att din egen sammansättningar är kompatibla med den här versionen av körningsmiljön.

Som tidigare nämnts U-SQL kör kod i en 64-bitars (x 64)-format. Kontrollera så att din kod kompileras om du vill köra x64. Annars felmeddelande felaktigt format visades tidigare.

Varje överförs sammansättningen DLL-filen och resursfilen som en annan runtime, en inbyggd sammansättning eller en .config-fil kan innehålla högst 400 MB. Den totala storleken på distribuerade resurser via DISTRIBUERA resurs eller via referenser till sammansättningar och deras ytterligare filer får inte överstiga 3 GB.

Slutligen, Observera att varje U-SQL-databas kan bara innehålla en version av alla angivna sammansättningen. Om du behöver både 7 och versionen 8 NewtonSoft Json.Net bibliotekets måste registrera dem i två olika databaser. Varje skript kan bara hänvisa till en version av en angiven sammansättning DLL-filen. I detta avseende följer U-SQL C# sammansättningen hantering och versionshantering semantiken.

## <a name="use-user-defined-functions-udf"></a>Använda användardefinierade funktioner: UDF
U-SQL-användardefinierade funktioner eller UDF, programmerar rutiner som accepterar parametrar, utföra en åtgärd (till exempel en komplicerad beräkning) och returnerar resultatet av den åtgärden som ett-värde. Returvärdet för UDF kan endast vara en enskild skalär. U-SQL-UDF kan anropas i grundläggande U-SQL-skript som andra C# skalära funktioner.

Vi rekommenderar att du initiera U-SQL-användardefinierade funktioner som **offentliga** och **statiska**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Först ska vi titta på det enkelt exemplet på hur du skapar en UDF.

I detta scenario användningsfall måste fastställa räkenskapsårets perioden, inklusive räkenskapskvartal och räkenskapsmånad på den första inloggningen för den angivna användaren. Räkenskapsårets första månaden på året i vårt scenario är juni.

Om du vill beräkna räkenskapsårets period, introducerar vi följande C#-funktion:

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

Bara beräknar räkenskapsmånad och kvartal och returnerar ett strängvärde. Juni använda en månad efter de första räkenskapskvartal vi ”Q1:P1”. Vi använder ”Q1:P2” för juli och så vidare.

Detta är en vanlig C#-funktion som vi ska använda i vår U-SQL-projekt.

Här är hur avsnittet bakomliggande kod visas i det här scenariot:

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

Nu det dags att anropa funktionen från grundläggande U-SQL-skriptet. För att göra detta, måste vi du ange ett fullständigt kvalificerat namn för funktionen, inklusive namnområdet, som i det här fallet är NameSpace.Class.Function(parameter).

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

Nedan följer utdatafilen för körning av skript:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Det här exemplet visar en enkel användning av infogade UDF i U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Behåll tillstånd mellan UDF-anrop
U-SQL C#-programmering objekt kan vara mer förfinade använder interaktivitet via bakomliggande kod globala variabler. Nu ska vi titta på följande användningsfall affärsscenariot.

Användare kan växla mellan av interna program i stora organisationer. Det kan vara Microsoft Dynamics CRM, PowerBI och så vidare. Kunder kan också använda en telemetri analys av hur användarna växlar mellan olika program, vilka användningstrender är, och så vidare. Målet för företag är att optimera användning av programmet. De kan också kombinera olika program eller specifika inloggning rutiner.

För att åstadkomma detta behöver vi fastställa sessions-ID. och fördröjning mellan den sista sessionen som inträffat.

Vi behöver söka efter en tidigare inloggning och tilldela sedan den här inloggningen till alla sessioner som skapas för samma program. Den första kontrollen är att inte grundläggande U-SQL-skript kan vi använda beräkningar över redan beräknade kolumner med funktionen LAG. Andra utmaningen är att vi behöver ha viss session för alla sessioner inom samma tidsperiod.

Lös problemet, vi använder en global variabel inuti en bakomliggande kod: `static public string globalSession;`.

Den globala variabeln används för hela raduppsättningen under våra körning av skript.

Här är avsnittet bakomliggande kod i vårt U-SQL-program:

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

Det här exemplet visar den globala variabeln `static public string globalSession;` används inuti den `getStampUserSession` funktionen och få initieras på nytt varje gång parametern Session ändras.

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

Funktionen `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` anropas här under andra minne raduppsättningen beräkningen. Passerar den `UserSessionTimestamp` kolumn och returnerar värdet tills `UserSessionTimestamp` har ändrats.

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

Det här exemplet visas en mer komplicerad användningsfall scenario där vi använder en global variabel inuti en bakomliggande kod som gäller för hela minne raduppsättningen.

## <a name="use-user-defined-types-udt"></a>Använda användardefinierade typer: UDT
Användardefinierade typer eller UDT, är en annan programmeringsfunktionen av U-SQL. U-SQL-UDT fungerar som en vanlig C# användardefinierad datatyp. C# är ett starkt typifierad språk som tillåter användning av inbyggda och anpassade användardefinierade typer.

U-SQL kan inte implicit serialisera eller deserialisera godtycklig UDT när UDT överförs mellan formhörnen i raduppsättningar. Det innebär att användaren måste ange ett explicit formaterare IFormatter-gränssnittet. Detta ger U-SQL med serialiserat och deserialisera metoder för UDT.

> [!NOTE]
> U-SQL kan inte inbyggda juicepressar outputters för närvarande serialisera och deserialisera UDT data till eller från filer även med IFormatter. Så när du skriver UDT data till en fil med instruktionen utdata, eller läsa med en extraktor, måste du skicka den som en sträng eller byte-matris. Sedan anropa serialisering och avserialisering kod (det vill säga den UDT metoden ToString()) explicit. Användardefinierade juicepressar och outputters, å andra sidan kan läsa och skriva UDT-typer.

Om vi försöker använda UDT i EXTRAKTOR eller OUTPUTTER (från föregående SELECT-), som visas här:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Vi får du följande felmeddelande:

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

Om du vill arbeta med UDT i outputter, har vi antingen serialisera den sträng med metoden ToString() eller skapa en anpassad outputter.

Användardefinierade typer som kan för närvarande inte användas i GROUP BY. Om UDT används i GROUP BY, returneras följande fel:

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

Om du vill definiera en UDT behöver vi:

* Lägg till följande namnområden:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Lägg till `Microsoft.Analytics.Interfaces`, vilket krävs för UDT-gränssnitt. Dessutom `System.IO` kan behövas för att definiera IFormatter-gränssnittet.

* Definiera en användardefinierade typ med SqlUserDefinedType attribut.

**SqlUserDefinedType** används för att markera en typdefinition i en sammansättning som en användardefinierad typ (UDT) i U-SQL. Egenskaper för attributet avspeglar UDT fysiska egenskaper. Den här klassen kan inte ärvas.

SqlUserDefinedType är ett obligatoriskt attribut för UDT-definition.

Konstruktorn för klassen:  

* SqlUserDefinedTypeAttribute (typ formaterare)

* Skriv formaterare: krävs för parametern för att definiera en UDT-formaterare--specifikt typ av den `IFormatter` interface måste överföras här.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Vanliga UDT kräver även definitionen av gränssnittet IFormatter som visas i följande exempel:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Den `IFormatter` gränssnittet Serialiserar och frigör Serialiserar ett objektdiagram med rot \<typeparamref name = ”T” >.

\<typeparam name = ”T” > rottyp för objektdiagrammet serialisera och deserialisera.

* **Deserialisera**: Frigör Serialiserar data på den angivna dataströmmen och reconstitutes diagram över objekt.

* **Serialisera**: Serialiserar ett objekt eller diagram över objekt med den angivna roten till den angivna dataströmmen.

`MyType` instans: instans av typen.  
`IColumnWriter` skrivaren / `IColumnReader` reader: den underliggande kolumn strömmen.  
`ISerializationContext` kontexten: uppräkning som definierar en uppsättning flaggor som anger källan eller målet kontext för dataströmmen under serialiseringen.

* **Mellanliggande**: Anger att kontexten källan eller målet inte är beständiga arkivet.

* **Beständiga**: Anger att kontexten källan eller målet är en beständiga arkivet.

Som en vanlig C# typ, en definition av U-SQL-UDT kan omfatta åsidosättningar för operatörer som +/ == /! =. Det kan också innefatta statiska metoder. Till exempel om vi ska använda den här UDT som en parameter för en mängdfunktion för U-SQL MIN vi behöver ange < operatorn åsidosättning.

Tidigare i den här handboken vi visas ett exempel på räkenskapsårets period identifiering från ett visst datum i formatet `Qn:Pn (Q1:P10)`. I följande exempel visas hur du definierar en anpassad typ för räkenskapsårets tidsperioden.

Följande är ett exempel på en bakomliggande kod avsnitt med anpassade UDT och IFormatter gränssnitt:

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

Som tidigare nämnts kan användas i SELECT-uttryck UDT, men kan inte användas i OUTPUTTER-EXTRAKTOR utan anpassad serialisering. Den måste antingen serialiseras som en sträng med `ToString()` eller används tillsammans med en anpassad OUTPUTTER/EXTRAKTOR.

Nu ska vi diskutera användning av UDT. I en bakomliggande kod avsnittet ändrat vi våra GetFiscalPeriod-funktionen på följande:

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

Som du ser returnerar värdet för våra FiscalPeriod-typen.

Här kan vi ge ett exempel på hur du använder den ytterligare i grundläggande U-SQL-skript. Det här exemplet visar olika former av UDT-anrop från U-SQL-skript.

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
Användardefinierade aggregeringar är aggregeringen-relaterade funktioner som inte levereras out-of-the-box med U-SQL. Exemplet kan vara en aggregering att utföra anpassad matematiska beräkningar, sträng sammanfogningar, ändringar med strängar och så vidare.

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

**SqlUserDefinedAggregate** anger att typen ska registreras som en användardefinierad funktion. Den här klassen kan inte ärvas.

Attributet SqlUserDefinedType **valfria** UDAGG definition.


Basklassen kan du skicka abstrakt tre parametrar: två som indataparametrar och en som ett resultat. Datatyperna är variabeln och ska definieras under klassarv.

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

* **Init** anropar en gång för varje grupp under beräkningen. Det ger en initieringsrutinen för varje grupp aggregering.  
* **Ackumulera** körs en gång för varje värde. Den innehåller de flesta funktioner för algoritmen aggregering. Den kan användas för att Aggregera värden med olika datatyper som definieras under klassarv. Använder två parametrar av varierande datatyper.
* **Avsluta** körs en gång per aggregering grupp i slutet av bearbetning till utdata resultatet för varje grupp.

Använd klassdefinitionen enligt följande för att deklarera rätt indata och utdata datatyper:

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
Om du vill använda UDAGG definieras i bakomliggande kod eller refereras till från den befintliga programmering DLL enligt tidigare diskussion.

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

I detta scenario användningsfall sammanfoga vi klass GUID för specifika användare.

## <a name="use-user-defined-objects-udo"></a>Använda användardefinierade objekt: UDO
U-SQL kan du definiera anpassade programmering objekt, vilket kallas användardefinierade objekt eller UDO.

Följande är en lista över UDO i U-SQL:

* Användardefinierade juicepressar
    * Extrahera rad för rad
    * Används för att implementera extrahering av data från anpassade strukturerade filer

* Användardefinierade outputters
    * Utdata rad för rad
    * Används för att anpassade utdatatyper eller anpassade format

* Användardefinierade processorer
    * Tar en rad och skapar en rad
    * Används för att minska antalet kolumner eller skapa nya kolumner med värden som härleds från en befintlig kolumn

* Användardefinierade appliers
    * Tar en rad och skapar 0 till n rader
    * Används med yttre/mellan Använd

* Användardefinierade combiners
    * Kombinerar raduppsättningar--användardefinierade kopplingar

* Användardefinierade förminskningsapparater
    * Tar n rader och skapar en rad
    * Används för att minska antalet rader

UDO kallas vanligtvis explicit i U-SQL-skript som en del av följande U-SQL-uttryck:

* EXTRAHERA
* UTDATA
* PROCESSEN
* KOMBINERA
* MINSKA

> [!NOTE]  
> UDOS är begränsad för att använda 0,5 Gb minne.  Den här minne begränsningen gäller inte för lokala körningar.

## <a name="use-user-defined-extractors"></a>Använda användardefinierade juicepressar
U-SQL kan du importera externa data med hjälp av en EXTRAHERA-instruktion. Uttrycket EXTRAHERA kan använda inbyggda UDO juicepressar:  

* *Extractors.Text()*: ger extrahering från avgränsade textfiler i olika kodningar.

* *Extractors.Csv()*: ger extrahering från CSV-filer (CSV) av olika kodningar.

* *Extractors.Tsv()*: ger extrahering från fliken med kommaseparerade värden (TVS) filer med olika kodningar.

Det kan vara praktiskt att utveckla anpassade extraktor. Det kan vara användbart när data importeras om vi vill göra något av följande uppgifter:

* Ändra inkommande data genom att dela kolumner och ändra enskilda värden. PROCESSOR-funktioner är bättre om du vill kombinera kolumner.
* Parsa Ostrukturerade data, till exempel webbsidor och e-postmeddelanden eller delvis Ostrukturerade data, till exempel XML/JSON.
* Tolka data i kodning stöds inte.

Om du vill definiera en användardefinierad extraktor eller Undanta måste vi skapa en `IExtractor` gränssnitt. Alla indataparametrar till extraktor, till exempel kolumn/rad avgränsare och kodning, måste definieras i konstruktorn för klassen. Den `IExtractor` interface måste även innehålla en definition för den `IEnumerable<IRow>` åsidosätta på följande sätt:

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

Den **SqlUserDefinedExtractor** attributet anger att typen ska registreras som en användardefinierad extraktor. Den här klassen kan inte ärvas.

SqlUserDefinedExtractor är ett valfritt attribut för Undanta definition. Den används för att definiera AtomicFileProcessing-egenskapen för Undanta.

* bool AtomicFileProcessing   

* **SANT** = anger att den här extraktor kräver atomiska indatafiler (JSON, XML,...)
* **FALSKT** = anger den här extraktor kan hantera delade / distribuerade filer (CSV, SEQ,...)

De huvudsakliga Undanta programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp indata som `IUnstructuredReader`. Utdata-objektet används för att ange utdata till följd av aktiviteten extraktor.

Indata är tillgänglig via `System.IO.Stream` och `System.IO.StreamReader`.

För indatakolumnerna uppräkningen dela vi först Indataströmmen med hjälp av en avgränsare för en rad.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Sedan ytterligare delats inkommande raden kolumnen.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Om du vill ställa in datautflödet vi använder den `output.Set` metoden.

Det är viktigt att förstå att anpassade extraktor endast matar ut kolumner och värden som definieras med utdata. Ange metodanrop.

```
output.Set<string>(count, part);
```

Den faktiska extraktor utdatan utlöses genom att anropa `yield return output.AsReadOnly();`.

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

Extraktor återskapar GUID för kolumnen ”guid” i detta scenario användningsfall och konverterar värdena i kolumnen ”användare” till versaler. Anpassade juicepressar kan ge mer komplicerad resultat genom att parsa indata och ändrar den.

Nedan följer grundläggande U-SQL-skript som använder en anpassad extraktor:

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

## <a name="use-user-defined-outputters"></a>Använda användardefinierade outputters
Användardefinierade outputter är en annan U-SQL-UDO som gör det möjligt att utöka inbyggda funktioner för U-SQL. Liknar extraktor, det finns flera inbyggda outputters.

* *Outputters.Text()*: skriver data till avgränsade textfiler i olika kodningar.
* *Outputters.Csv()*: skriver data till en fil med kommaavgränsade värden (CSV) filer med olika kodningar.
* *Outputters.Tsv()*: skriver data till fliken med kommaseparerade värden (TVS) filer med olika kodningar.

Anpassade outputter kan du skriva data i ett anpassat definierade format. Detta kan vara användbart för följande uppgifter:

* Data skrivs till delvis strukturerade eller Ostrukturerade filer.
* Stöd för skrivning av data inte kodningar.
* Ändra utdata eller lägga till anpassade attribut.

Om du vill definiera användardefinierade outputter måste vi skapa den `IOutputter` gränssnitt.

Följande är grundläggande `IOutputter` klassen implementering:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alla indataparametrar för outputter som kolumn/rad avgränsare, kodning och så vidare måste definieras i konstruktorn för klassen. Den `IOutputter` interface måste även innehålla en definition för `void Output` åsidosätta. Attributet `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` (valfritt) kan anges för behandling av atomiska. Mer information finns i följande uppgifter.

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
* Klassen konstruktor används för att skicka parametrar till användardefinierade outputter.
* `Close` används för att åsidosätta (valfritt) om du vill frigöra dyra tillstånd eller kontrollera när den sista raden har skrivits.

**SqlUserDefinedOutputter** attributet anger att typen ska registreras som en användardefinierad outputter. Den här klassen kan inte ärvas.

SqlUserDefinedOutputter är ett valfritt attribut för en användardefinierad outputter definition. Den används för att definiera egenskapen AtomicFileProcessing.

* bool AtomicFileProcessing   

* **SANT** = anger att den här outputter kräver atomiska utdatafilerna (JSON, XML,...)
* **FALSKT** = anger den här outputter kan hantera delade / distribuerade filer (CSV, SEQ,...)

De huvudsakliga programmering objekten är **raden** och **utdata**. Den **raden** objektet används för att räkna upp utdata som `IRow` gränssnitt. **Utdata** används för att ställa in datautflödet målfilen.

Utdata är tillgänglig via den `IRow` gränssnitt. Utdata skickas en rad i taget.

Enskilda värden räknas upp genom att anropa metoden Get för IRow-gränssnittet:

```
row.Get<string>("column_name")
```

Enskilda kolumnnamn kan fastställas genom att anropa `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Den här metoden låter dig skapa en flexibel outputter för alla metadata-scheman.

Utdata skrivs till filen med hjälp av `System.IO.StreamWriter`. Stream-parametern anges till `output.BaseStrea` som en del av `IUnstructuredWriter output`.

Observera att det är viktigt att tömma Databufferten för filen efter varje iteration raden. Dessutom kan den `StreamWriter` objektet måste användas med tillgänglig attributet aktiverad (standard) och den **med** nyckelordet:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Annars kan anropa uttryckligen Flush() metoden efter varje iteration. Detta visar vi i följande exempel.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ange sidhuvuden och sidfötter för användardefinierade outputter
Använd enstaka upprepning körning flödet för att ange en rubrik.

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

Koden i först `if (isHeaderRow)` block körs bara en gång.

Använd för sidfoten referensen till instansen av `System.IO.Stream` objekt (`output.BaseStream`). Skriva sidfoten i metoden Close() i den `IOutputter` gränssnitt.  (Mer information finns i följande exempel.)

Följande är ett exempel på en användardefinierad outputter:

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

Det här är en HTML-outputter som skapar en HTML-fil med tabelldata.

### <a name="call-outputter-from-u-sql-base-script"></a>Anropa outputter från grundläggande U-SQL-skript
För att anropa en anpassad outputter från grundläggande U-SQL-skript, har den nya instansen av outputter-objektet skapas.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

För att undvika att skapa en instans av objektet i grundläggande skript kan skapa vi en funktion omslutning som visas i det tidigare exemplet:

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

I det här fallet ursprungliga anropet ser ut som följande:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Använda användardefinierade processorer
Användardefinierade är UDP, en typ av U-SQL-UDO som gör det möjligt att bearbeta inkommande rader genom att använda programmeringsfunktioner. UDP kan du kombinera kolumner, ändra värdena och lägga till nya kolumner, om det behövs. I princip hjälper det för att bearbeta en raduppsättning för att skapa nödvändiga dataelement.

Om du vill definiera en UDP måste vi skapa en `IProcessor` gränssnitt med den `SqlUserDefinedProcessor` attribut som är valfritt för UDP.

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

**SqlUserDefinedProcessor** anger att typen ska registreras som en användardefinierad processor. Den här klassen kan inte ärvas.

Attributet SqlUserDefinedProcessor **valfria** för UDP-definition.

De huvudsakliga programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp indatakolumnerna och utdata och för att ange utdata till följd av processoraktiviteten.

För indatakolumnerna uppräkningen vi använder den `input.Get` metoden.

```
string column_name = input.Get<string>("column_name");
```

Parametern för `input.Get` metoden är en kolumn som skickas som en del av den `PRODUCE` -satsen i den `PROCESS` instruktionen av grundläggande U-SQL-skriptet. Vi behöver använda rätt datatyp här.

Utdata, använda den `output.Set` metoden.

Det är viktigt att Observera att anpassade producent matar ut endast kolumner och värden som definieras med de `output.Set` metodanrop.

```
output.Set<string>("mycolumn", mycolumn);
```

Den faktiska utdatan utlöses genom att anropa `return output.AsReadOnly();`.

Följande är ett exempel på processor:

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

I detta scenario användningsfall genererar processorn en ny kolumn som kallas ”full_description” genom att kombinera de befintliga kolumnerna – i det här fallet ”användare” i versaler och ”des”. Den genererar ett GUID och returnerar de ursprungliga och nya GUID-värdena.

Som du ser i exemplet ovan, kan du anropa C#-metoder under `output.Set` metodanrop.

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
Ett U-SQL-användardefinierade applier kan du anropa en anpassad C#-funktion för varje rad som returneras av det yttre tabelluttrycket i en fråga. Rätt indata ska utvärderas för varje rad från den vänstra indata och de rader som produceras kombineras för det slutgiltiga resultatet. Listan över kolumner som genereras av APPLY-operatorn är en kombination av en uppsättning kolumner i vänstra och högra indata.

Användardefinierade applier anropas som en del av uttrycket MARKERAR du USQL.

Vanliga anropet till de användardefinierade applier ser ut ungefär så här:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Mer information om hur du använder appliers i ett SELECT-uttryck finns [U-SQL väljer att välja mellan tillämpa och yttre tillämpa](https://msdn.microsoft.com/library/azure/mt621307.aspx).

Användardefinierade applier basklass definition är följande:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Om du vill definiera en användardefinierad applier måste vi skapa den `IApplier` gränssnitt med den [`SqlUserDefinedApplier`]-attribut som är valfri för en användardefinierad applier definition.

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
* Klassen konstruktor används för att skicka parametrar till användardefinierade applier.

**SqlUserDefinedApplier** anger att typen ska registreras som en användardefinierad applier. Den här klassen kan inte ärvas.

**SqlUserDefinedApplier** är **valfria** för en användardefinierad applier definition.


De huvudsakliga programmering objekten är följande:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Inkommande raduppsättningar skickas `IRow` indata. Utdata rader genereras som `IUpdatableRow` utdata-gränssnittet.

Enskilda kolumnnamn kan fastställas genom att anropa den `IRow` Schema-metoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Att hämta värden för faktiska data från inkommande `IRow`, vi använda metoden Get() för `IRow` gränssnitt.

```
mycolumn = row.Get<int>("mycolumn")
```

Eller vi använda kolumnnamn schemat:

```
row.Get<int>(row.Schema[0].Name)
```

Utdatavärden måste anges med `IUpdatableRow` utdata:

```
output.Set<int>("mycolumn", mycolumn)
```

Det är viktigt att förstå att anpassade appliers endast utgående kolumner och värden som definieras med `output.Set` metodanrop.

Den faktiska utdatan utlöses genom att anropa `yield return output.AsReadOnly();`.

Användardefinierade applier parametrar kan skickas till konstruktorn. Applier kan returnera en variabel antalet kolumner som måste definieras under applier-anropet i grundläggande U-SQL-skript.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Här är det användardefinierade applier exemplet:

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

Nedan följer grundläggande U-SQL-skriptet för den här användardefinierade applier:

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

I den här Användarscenario fungerar användardefinierade applier som en kommaavgränsad parser för bil flottan egenskaper. Indatafilen rader ut ungefär så här:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Shevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Det är en typisk tabbavgränsad TVS fil med en kolumn för egenskaper som innehåller bil egenskaper, till exempel märke och modell. Dessa egenskaper måste parsas till tabellkolumner. Applier som har angetts kan du generera en dynamisk antal egenskaper i raduppsättningen resultat baserat på den parameter som skickas. Du kan skapa alla egenskaper eller en specifik uppsättning endast egenskaper.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Användardefinierade applier kan anropas som en ny instans av applier objekt:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Eller med anrop av en wrapper standardmetod:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Använda användardefinierade combiners
Användardefinierade combiner eller UDC, kan du kombinera rader från vänster och höger raduppsättningar baserat på egen kod. Användardefinierade combiner används med KOMBINERA uttryck.

En combiner anropas med KOMBINERA uttryck som innehåller den nödvändiga informationen om både den inkommande raduppsättningar, grupperade kolumner, förväntat resultat schemat och ytterligare information.

För att anropa en combiner i ett grundläggande U-SQL-skript, använder vi följande syntax:

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

Om du vill definiera en användardefinierad combiner måste vi skapa den `ICombiner` gränssnitt med den [`SqlUserDefinedCombiner`]-attribut som är valfri för en användardefinierad Combiner definition.

Base `ICombiner` klassen definition:

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

Den anpassa implementeringen av en `ICombiner` gränssnitt ska innehålla definitionen för en `IEnumerable<IRow>` kombinera åsidosättning.

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

Den **SqlUserDefinedCombiner** attributet anger att typen ska registreras som en användardefinierad combiner. Den här klassen kan inte ärvas.

**SqlUserDefinedCombiner** används för att definiera egenskapen Combiner läge. Det är ett valfritt attribut för en användardefinierad combiner definition.

CombinerMode läge

CombinerMode uppräkning kan ha följande värden:

* Fullständig (0) varje rad i utdata potentiellt beror på alla inkommande rader från vänster och höger med samma nyckelvärde.

* Vänster (1) varje rad i utdata är beroende av en rad från vänster (och potentiellt alla rader från höger med samma nyckelvärde) som indata.

* Höger (2) varje rad i utdata är beroende av en rad från höger (och potentiellt alla rader från vänster med samma nyckelvärde) som indata.

* Den inre (3) varje rad i utdata beror på en inkommande rad från vänster och höger med samma värde.

Exempel: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


De huvudsakliga programmering objekten är:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Inkommande raduppsättningar skickas **vänstra** och **rätt** `IRowset` typ av gränssnitt. Båda raduppsättningar måste räknas upp för bearbetning. Du kan räkna upp alla gränssnitt en gång, så att vi räknar upp och cachelagra den vid behov.

För cachelagring, kan vi skapa en lista\<T\> typ av minne som ett resultat av en LINQ Frågekörningen, specifikt lista <`IRow`>. Anonym datatypen kan användas under uppräkning samt.

Se [introduktion till LINQ-frågor (C#)](https://msdn.microsoft.com/library/bb397906.aspx) för mer information om LINQ-frågor och [IEnumerable\<T\> gränssnittet](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) för mer information om IEnumerable\<T\> gränssnitt.

Att hämta värden för faktiska data från inkommande `IRowset`, vi använda metoden Get() för `IRow` gränssnitt.

```
mycolumn = row.Get<int>("mycolumn")
```

Enskilda kolumnnamn kan fastställas genom att anropa den `IRow` Schema-metoden.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Eller med hjälp av kolumnen schemanamnet:

```
c# row.Get<int>(row.Schema[0].Name)
```

Allmän uppräkning med LINQ ser ut ungefär så här:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Efter att räkna upp båda raduppsättningar ska vi gå igenom alla rader. För varje rad i raduppsättningen vänstra ska vi söka efter alla rader som uppfyller villkoret för våra combiner.

Utdatavärden måste anges med `IUpdatableRow` utdata.

```
output.Set<int>("mycolumn", mycolumn)
```

Den faktiska utdatan utlöses av anrop till `yield return output.AsReadOnly();`.

Följande är ett exempel på combiner:

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

I detta scenario användningsfall skapar vi en analytics-rapport för återförsäljaren. Målet är att hitta alla produkter som kostar mer än 20 000 och som sälja via webbplatsen snabbare än via vanliga återförsäljare inom ett bestämt tidsintervall.

Det här är det grundläggande U-SQL-skriptet. Du kan jämföra logiken mellan en vanlig koppling och en combiner:

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

En användardefinierad combiner kan anropas som en ny instans av objektet applier:

```
USING new MyNameSpace.MyCombiner();
```


Eller med anrop av en wrapper standardmetod:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Använda användardefinierade förminskningsapparater

U-SQL kan du skriva anpassade raduppsättningen förminskningsapparater i C# med hjälp av ramverket användardefinierade operatorn utökningsbarhet och implementera ett IReducer gränssnitt.

Användardefinierade reducer eller UDR, kan användas till att eliminera onödiga raderna under Extraheringen av data (importera). Det kan också användas att manipulera och utvärdera rader och kolumner. Baserat på logik för programmering, kan det också definiera vilka rader måste ska extraheras.

Om du vill definiera en UDR klass måste vi skapa en `IReducer` gränssnitt med en valfri `SqlUserDefinedReducer` attribut.

Den här klassen-gränssnittet ska innehålla en definition för det `IEnumerable` gränssnittet raduppsättningen åsidosätta.

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

Den **SqlUserDefinedReducer** attributet anger att typen ska registreras som en användardefinierad reducer. Den här klassen kan inte ärvas.
**SqlUserDefinedReducer** är ett valfritt attribut för en användardefinierad reducer definition. Den används för att definiera IsRecursive egenskap.

* bool IsRecursive    
* **SANT** = anger om den här Reducer är kopplat och kommutativa

De huvudsakliga programmering objekten är **inkommande** och **utdata**. Indataobjektet används för att räkna upp inkommande rader. Utdata används för att ange utdata rader på grund av att minska aktivitet.

För inkommande rader uppräkningen vi använder den `Row.Get` metoden.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Parametern för den `Row.Get` metoden är en kolumn som skickas som en del av den `PRODUCE` klassen för den `REDUCE` instruktionen av grundläggande U-SQL-skriptet. Vi behöver använda rätt datatyp som här.

Utdata, använda den `output.Set` metoden.

Det är viktigt att förstå den anpassade reducer endast matar ut värden som definieras med de `output.Set` metodanrop.

```
output.Set<string>("mycolumn", guid);
```

Den faktiska reducer utdatan utlöses genom att anropa `yield return output.AsReadOnly();`.

Följande är ett exempel på reducer:

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

I detta scenario användningsfall reducer hoppar över rader med ett tomt användarnamn. För varje rad i raduppsättningen läser varje obligatorisk kolumn, därefter utvärderar längden på användarnamnet. Den matar ut den faktiska raden bara om värdet för användarnamnet är större än 0.

Nedan följer grundläggande U-SQL-skript som använder en anpassad reducer:

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
