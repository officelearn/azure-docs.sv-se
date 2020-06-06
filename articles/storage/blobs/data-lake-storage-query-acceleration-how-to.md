---
title: Filtrera data med hjälp av Azure Data Lake Storage fråga acceleration (förhands granskning) | Microsoft Docs
description: Använd Query acceleration (för hands version) om du vill hämta en delmängd av data från ditt lagrings konto.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465872"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrera data med hjälp av Azure Data Lake Storage fråga acceleration (för hands version)

Den här artikeln visar hur du använder Query acceleration (för hands version) för att hämta en delmängd av data från ditt lagrings konto. 

Fråga acceleration (för hands version) är en ny funktion för Azure Data Lake Storage som gör det möjligt för program-och analys ramverk att dramatiskt optimera data bearbetningen genom att bara hämta de data som de behöver för att utföra en specifik åtgärd. Läs mer i [Azure Data Lake Storage Query acceleration (för hands version)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Funktionen för acceleration av frågor finns i offentlig för hands version och är tillgänglig i regionerna Kanada, centrala och Frankrike, centrala. Information om hur du granskar begränsningar finns i artikeln om [kända problem](data-lake-storage-known-issues.md) . Information om hur du registrerar i för hands versionen finns i [det här formuläret](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Krav

### <a name="net"></a>[.NET](#tab/dotnet)

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **Allmänt-syfte v2-** lagrings konto. Se [skapa ett lagrings konto](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **Allmänt-syfte v2-** lagrings konto. Se [skapa ett lagrings konto](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 eller senare.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > I den här artikeln förutsätter vi att du har skapat ett Java-projekt med Apache Maven. Ett exempel på hur du skapar ett projekt med hjälp av Apache maven finns i [Konfigurera](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Installera paket 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Hämta accelerations paketen för frågor. Du kan hämta en komprimerad ZIP-fil som innehåller paketen med hjälp av den här länken: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Extrahera innehållet i den här filen till projekt katalogen.

3. Öppna projekt filen (*. CSPROJ*) i en text redigerare och Lägg till dessa paket referenser inuti \<Project\> elementet.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Återställa SDK-paket för för hands versionen. I det här exempel kommandot återställs för hands versionen av SDK-paketen med hjälp av `dotnet restore` kommandot. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Återställ alla andra beroenden från den offentliga NuGet-lagringsplatsen.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Skapa en katalog i projektets rot. Rot katalogen är den katalog som innehåller filen **Pom. XML** .

   > [!NOTE]
   > I exemplen i den här artikeln förutsätter vi att namnet på katalogen är **lib**.

2. Hämta accelerations paketen för frågor. Du kan hämta en komprimerad ZIP-fil som innehåller paketen med hjälp av den här länken: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Extrahera filerna i zip-filen till den katalog som du har skapat. I vårt exempel heter katalogen **lib**. 

4. Öppna filen *Pom. XML* i text redigeraren. Lägg till följande beroende element i gruppen med beroenden. 

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

## <a name="add-statements"></a>Lägg till instruktioner


### <a name="net"></a>[.NET](#tab/dotnet)

Lägg till dessa `using` uttryck överst i din kod fil.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Frågans acceleration hämtar CSV-och JSON-formaterade data. Därför bör du se till att lägga till instruktioner för alla CSV-eller JSON-parsningsfel som du väljer att använda. I exemplen som visas i den här artikeln analyseras en CSV-fil med hjälp av [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -biblioteket som finns på NuGet. Därför skulle vi lägga till dessa `using` instruktioner överst i kod filen.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

För att kunna kompilera exempel som presenteras i den här artikeln måste du också lägga till dessa `using` instruktioner.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Lägg till dessa `import` uttryck överst i din kod fil.

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

Du kan använda SQL för att ange rad filter-predikat och kolumn projektioner i en begäran om acceleration av frågor. Följande kod frågar en CSV-fil i Storage och returnerar alla rader med data där den tredje kolumnen matchar värdet `Hemingway, Ernest` . 

- I SQL-frågan används nyckelordet `BlobStorage` för att beteckna filen som efter frågas.

- Kolumn referenser anges som `_N` den första kolumnen `_1` . Om käll filen innehåller en rubrik rad kan du referera till kolumner med det namn som anges i rubrik raden. 

### <a name="net"></a>[.NET](#tab/dotnet)

Metoden async `BlobQuickQueryClient.QueryAsync` skickar frågan till API för frågans acceleration och skickar sedan tillbaka resultatet till programmet som ett [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) -objekt.

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
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
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

Metoden `BlobQuickQueryClient.openInputStream()` skickar frågan till API för frågans acceleration och skickar sedan tillbaka resultatet till programmet som ett `InputStream` objekt som kan läsas som andra InputStream-objekt.

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

## <a name="retrieve-specific-columns"></a>Hämta vissa kolumner

Du kan begränsa resultaten till en delmängd av kolumnerna. På så sätt hämtar du bara de kolumner som behövs för att utföra en specifik beräkning. Detta förbättrar programmets prestanda och minskar kostnaden eftersom mindre data överförs över nätverket. 

Den här koden hämtar endast `PublicationYear` kolumnen för alla böcker i data uppsättningen. Den använder också informationen från rubrik raden i käll filen för att referera till kolumner i frågan.


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

Följande kod kombinerar rad filtrering och kolumn prognoser till samma fråga. 

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

- [Registrerings formulär för fråga om acceleration](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage fråga om acceleration (för hands version)](data-lake-storage-query-acceleration.md)
- [Språk referens för Query acceleration SQL (för hands version)](query-acceleration-sql-reference.md)
