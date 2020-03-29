---
title: Överföra data med dataförflyttningsbiblioteket för .NET
titleSuffix: Azure Storage
description: Använd dataflyttningsbiblioteket för att flytta eller kopiera data till eller från blob- och filinnehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942910"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Överföra data med dataflyttbiblioteket

Azure Storage Data Movement-biblioteket är ett bibliotek med öppen källkod som är utformat för hög prestandauppladdning, nedladdning och kopiering av blobbar och filer. Dataförflyttningsbiblioteket innehåller praktiska metoder som inte är tillgängliga i Azure Storage-klientbiblioteket för .NET. Dessa metoder ger möjlighet att ange antalet parallella operationer, spåra överföringsförloppet, enkelt återuppta en avbruten överföring och mycket mer.

Det här biblioteket använder också .NET Core, vilket innebär att du kan använda den när du skapar .NET-appar för Windows, Linux och macOS. Mer information om .NET Core finns i [.NET Core-dokumentationen](https://dotnet.github.io/). Det här biblioteket fungerar även för traditionella .NET Framework-appar för Windows.

Det här dokumentet visar hur du skapar ett .NET Core-konsolprogram som körs på Windows, Linux och macOS och utför följande scenarier:

- Ladda upp filer och kataloger till Blob Storage.
- Definiera antalet parallella operationer vid överföring av data.
- Spåra förloppet för dataöverföring.
- Återuppta avbruten dataöverföring.
- Kopiera filen från URL till Blob Storage.
- Kopiera från Blob Storage till Blob Storage.

## <a name="prerequisites"></a>Krav

- [Visual Studio-kod](https://code.visualstudio.com/)
- Ett [Azure-lagringskonto](storage-account-create.md)

## <a name="setup"></a>Installation

1. Besök [.NET Core Installation Guide](https://www.microsoft.com/net/core) för att installera .NET Core. När du väljer din miljö väljer du kommandoradsalternativet.
2. Skapa en katalog för projektet på kommandoraden. Navigera till den här `dotnet new console -o <sample-project-name>` katalogen och skriv sedan för att skapa ett C#-konsolprojekt.
3. Öppna den här katalogen i Visual Studio-kod. Det här steget kan snabbt göras via `code .` kommandoraden genom att skriva i Windows.
4. Installera [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) från Visual Studio Code Marketplace. Starta om Visual Studio-koden.
5. Nu bör du se två uppmaningar. En är för att lägga till "nödvändiga tillgångar för att bygga och felsöka." Klicka på "ja". En annan uppmaning är att återställa olösta beroenden. Klicka på "återställ".
6. Ändra `launch.json` `.vscode` under för att använda extern terminal som konsol. Den här inställningen ska läsas som`"console": "externalTerminal"`
7. Med Visual Studio Code kan du felsöka .NET Core-program. Tryck `F5` för att köra ditt program och kontrollera att din setup fungerar. Du skulle se "Hello World!" skrivs ut på konsolen.

## <a name="add-the-data-movement-library-to-your-project"></a>Lägga till dataförflyttningsbiblioteket i projektet

1. Lägg till den senaste versionen `dependencies` av dataförflyttningsbiblioteket i avsnittet i `<project-name>.csproj` filen. I skrivande stund skulle denna version`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. En uppmaning bör visas för att återställa projektet. Klicka på knappen "återställ". Du kan också återställa projektet från kommandoraden `dotnet restore` genom att skriva kommandot i roten på projektkatalogen.

Ändra: `<project-name>.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Ställ in skelettet av din ansökan

Det första vi gör är att ställa in "skelett" koden för vår ansökan. Den här koden uppmanar oss att ange ett lagringskontonamn `CloudStorageAccount` och en kontonyckel och använder dessa autentiseringsuppgifter för att skapa ett objekt. Det här objektet används för att interagera med vårt lagringskonto i alla överföringsscenarier. Koden uppmanar oss också att välja vilken typ av överföring operation vi vill utföra.

Ändra: `Program.cs`

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Ladda upp en lokal fil till en blob

Lägg till `GetSourcePath` `GetBlob` metoderna och till: `Program.cs`

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Ändra `TransferLocalFileToAzureBlob` metoden:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Den här koden uppmanar oss att söka efter sökvägen till en lokal fil, namnet på en ny eller befintlig behållare och namnet på en ny blob. Metoden `TransferManager.UploadAsync` utför överföringen med den här informationen.

Tryck `F5` för att köra ditt program. Du kan kontrollera att överföringen skedde genom att visa ditt lagringskonto med [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ange antalet parallella operationer

En funktion som erbjuds av dataflyttbiblioteket är möjligheten att ange antalet parallella åtgärder för att öka dataöverföringsdataflödet. Som standard anger dataförflyttningsbiblioteket antalet parallella åtgärder till 8 * antalet kärnor på datorn.

Tänk på att många parallella åtgärder i en miljö med låg bandbredd kan överbelasta nätverksanslutningen och faktiskt förhindra att åtgärder slutförs helt. Du måste experimentera med den här inställningen för att avgöra vad som fungerar bäst baserat på din tillgängliga nätverksbandbredd.

Låt oss lägga till lite kod som tillåter oss att ställa in antalet parallella operationer. Låt oss också lägga till kod som gånger hur lång tid det tar för överföringen att slutföras.

Lägg `SetNumberOfParallelOperations` till `Program.cs`en metod i:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändra `ExecuteChoice` den metod `SetNumberOfParallelOperations`som ska användas:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Ändra `TransferLocalFileToAzureBlob` metoden för att använda en timer:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Spåra överföringsstatus

Att veta hur lång tid det tog för data att överföra är till hjälp. Att kunna se hur överföringen fortskrider *under* överföringen skulle dock vara ännu bättre. För att uppnå detta scenario `TransferContext` måste vi skapa ett objekt. Objektet `TransferContext` finns i två `SingleTransferContext` `DirectoryTransferContext`former: och . Den förra är för överföring av en enda fil och den senare är för att överföra en katalog med filer.

Lägg till `GetSingleTransferContext` `GetDirectoryTransferContext` metoderna och till: `Program.cs`

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Ändra `TransferLocalFileToAzureBlob` den metod `GetSingleTransferContext`som ska användas:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Återuppta en avbruten överföring

En annan bekväm funktion som erbjuds av Data Movement-biblioteket är möjligheten att återuppta en avbruten överföring. Låt oss lägga till lite kod som tillåter oss `c`att tillfälligt avbryta överföringen genom att skriva , och sedan återuppta överföringen 3 sekunder senare.

Ändra: `TransferLocalFileToAzureBlob`

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Hittills har vårt `checkpoint` värde alltid varit `null`satt till . Om vi avbryter överföringen, hämtar vi den sista kontrollpunkten för vår överföring och använder den här nya kontrollpunkten i vårt transferkontext.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Överföra en lokal katalog till Blob-lagring

Det skulle vara en besvikelse om Data Movement biblioteket bara kunde överföra en fil i taget. Lyckligtvis är detta inte fallet. Dataförflyttningsbiblioteket ger möjlighet att överföra en katalog med filer och alla dess underkataloger. Låt oss lägga till några kod som tillåter oss att göra just det.

Lägg först till `GetBlobDirectory` `Program.cs`metoden i:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Ändra `TransferLocalDirectoryToAzureBlobDirectory`sedan:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Det finns några skillnader mellan den här metoden och metoden för att ladda upp en enda fil. Vi använder `TransferManager.UploadDirectoryAsync` nu och `getDirectoryTransferContext` den metod vi skapade tidigare. Dessutom ger vi nu `options` ett värde till vår uppladdningsoperation, vilket gör att vi kan indikera att vi vill inkludera underkataloger i vår uppladdning.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiera en fil från URL till en blob

Nu ska vi lägga till kod som tillåter oss att kopiera en fil från en URL till en Azure Blob.

Ändra: `TransferUrlToAzureBlob`

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ett viktigt användningsfall för den här funktionen är när du behöver flytta data från en annan molntjänst (t.ex. Så länge du har en URL som ger dig åtkomst till resursen kan du `TransferManager.CopyAsync` enkelt flytta resursen till Azure Blobbar med hjälp av metoden. Den här metoden introducerar också en ny boolesk parameter. Om du `true` anger den här parametern anger att vi vill göra en asynkron serverkopia. Ställa in `false` den här parametern för att ange en synkron kopia - vilket innebär att resursen hämtas till vår lokala dator först och sedan överförs till Azure Blob. Synkron kopia är dock för närvarande endast tillgänglig för kopiering från en Azure Storage-resurs till en annan.

## <a name="copy-a-blob"></a>Kopiera en blob

En annan funktion som unikt tillhandahålls av Data Movement-biblioteket är möjligheten att kopiera från en Azure Storage-resurs till en annan.

Ändra: `TransferAzureBlobToAzureBlob`

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

I det här exemplet ställer vi `TransferManager.CopyAsync` `false` in den booleska parametern så att den anger att vi vill göra en synkron kopia. Det innebär att resursen först hämtas till vår lokala dator och sedan överförs till Azure Blob. Alternativet synkron kopiering är ett bra sätt att se till att kopieringen har en konsekvent hastighet. Däremot beror hastigheten på en asynkron serverkopia på den tillgängliga nätverksbandbredden på servern, som kan variera. Synkron kopia kan dock generera ytterligare utgående kostnader jämfört med asynkron kopia. Den rekommenderade metoden är att använda synkron kopia i en Azure VM som finns i samma region som ditt källlagringskonto för att undvika utgående kostnad.

Programmet för dataflyttning är nu slutfört. [Det fullständiga kodexemplet är tillgängligt på GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Nästa steg

[Referensdokumentation för Azure Storage Data Movement-bibliotek](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
