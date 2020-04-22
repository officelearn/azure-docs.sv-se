---
title: Filtrera data med hjälp av azure data lake storage-frågeacceleration (förhandsversion) | Microsoft-dokument
description: Använd frågeacceleration (förhandsgranskning) för att hämta en delmängd data från ditt lagringskonto.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771851"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrera data med hjälp av Azure Data Lake Storage-frågeacceleration (förhandsgranskning)

I den här artikeln visas hur du använder frågeacceleration (förhandsversion) för att hämta en delmängd data från ditt lagringskonto. 

Frågeacceleration (förhandsversion) är en ny funktion för Azure Data Lake Storage som gör det möjligt för program och analysramverk att dramatiskt optimera databearbetning genom att hämta endast de data som de behöver för att utföra en viss åtgärd. Mer information finns i [Azure Data Lake Storage Query Acceleration (förhandsversion)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Frågeaccelerationfunktionen är i offentlig förhandsversion och är tillgänglig i regionerna Kanada Central och France Central. Information om hur du granskar begränsningar finns i artikeln [Kända problem.](data-lake-storage-known-issues.md) Om du vill registrera dig i förhandsgranskningen läser du [det här formuläret](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Krav

### <a name="net"></a>[.NET](#tab/dotnet)

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **allmänt v2-lagringskonto.** se [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **allmänt v2-lagringskonto.** se [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 eller senare.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Den här artikeln förutsätter att du har skapat ett Java-projekt med apache Maven. Ett exempel på hur du skapar ett projekt med Apache Maven finns i [Konfigurera](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Installera paket 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Hämta frågeaccelerationspaketen. Du kan hämta en komprimerad ZIP-fil som innehåller dessa [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)paket med den här länken: . 

2. Extrahera innehållet i den här filen till projektkatalogen.

3. Öppna projektfilen (*CSPROJ*) i en textredigerare och \<\> lägg till dessa paketreferenser inuti projektelementet.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Återställ förhandsgransknings-SDK-paketen. Med det här exemplet återställs förhandsgransknings-SDK-paketen `dotnet restore` med kommandot . 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Återställ alla andra beroenden från den offentliga NuGet-databasen.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Skapa katalog i projektets rot. Rotkatalogen är den katalog som innehåller **filen pom.xml.**

   > [!NOTE]
   > Exemplen i den här artikeln förutsätter att namnet på katalogen är **lib**.

2. Hämta frågeaccelerationspaketen. Du kan hämta en komprimerad ZIP-fil som innehåller dessa [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)paket med den här länken: . 

3. Extrahera filerna i zip-filen till den katalog som du skapade. I vårt exempel heter den katalogen **lib**. 

4. Öppna *filen pom.xml* i textredigeraren. Lägg till följande beroendeelement i beroendegruppen. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Lägga till utdrag


### <a name="net"></a>[.NET](#tab/dotnet)

Lägg `using` till dessa satser överst i kodfilen.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Frågeacceleration hämtar CSV- och Json-formaterade data. Se därför till att lägga till med hjälp av satser för alla CSV- eller Json-parsningsbibliotek som du väljer att använda. Exemplen som visas i den här artikeln tolkar en CSV-fil med hjälp av [CsvHelper-biblioteket](https://www.nuget.org/packages/CsvHelper/) som är tillgängligt på NuGet. Därför lägger vi `using` till dessa satser överst i kodfilen.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Om du vill sammanställa exempel som presenteras i `using` den här artikeln måste du också lägga till dessa satser också.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Lägg `import` till dessa satser överst i kodfilen.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Hämta data med hjälp av ett filter

Du kan använda SQL för att ange radfilterpredikater och kolumnprojektioner i en begäran om frågeacceleration. Följande kod frågar en CSV-fil i lagring och returnerar alla `Hemingway, Ernest`rader med data där den tredje kolumnen matchar värdet . 

- I SQL-frågan används `BlobStorage` nyckelordet för att beteckna filen som efterfrågas.

- Kolumnreferenser anges som `_N` där den `_1`första kolumnen finns . Om källfilen innehåller en rubrikrad kan du referera till kolumner med det namn som anges i rubrikraden. 

### <a name="net"></a>[.NET](#tab/dotnet)

Metoden async `BlobQuickQueryClient.QueryAsync` skickar frågan till frågeacceleration API och strömmar sedan resultaten tillbaka till programmet som ett [Stream-objekt.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoden `BlobQuickQueryClient.openInputStream()` skickar frågan till frågeacceleration API och sedan strömmar `InputStream` resultaten tillbaka till programmet som ett objekt som kan läsas som alla andra InputStream-objekt.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Hämta specifika kolumner

Du kan begränsa resultaten till en delmängd av kolumner. På så sätt hämtar du bara de kolumner som behövs för att utföra en viss beräkning. Detta förbättrar programmets prestanda och minskar kostnaderna eftersom mindre data överförs över nätverket. 

Den här koden `PublicationYear` hämtar bara kolumnen för alla böcker i datauppsättningen. Den använder också informationen från rubrikraden i källfilen för att referera till kolumner i frågan.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Följande kod kombinerar radfiltrering och kolumnprojektioner i samma fråga. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Registreringsformulär för frågeacceleration](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure Data Lake Storage-frågeacceleration (förhandsgranskning)](data-lake-storage-query-acceleration.md)
- [SQL-språkreferens för frågeacceleration (förhandsgranskning)](query-acceleration-sql-reference.md)
- REST-API-referens för frågeacceleration
