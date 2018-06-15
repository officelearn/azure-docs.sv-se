---
title: Överföra Data med Microsoft Azure Storage Data Movement Library | Microsoft Docs
description: Använd Data Movement Library att flytta eller kopiera data till och från blob- och innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Enkelt migrera dina data till Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.openlocfilehash: eb96f3697d5369ba96a1b0c491e3eacf09e7aac4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
ms.locfileid: "27564747"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Överföra Data med Microsoft Azure Storage Data Movement Library

## <a name="overview"></a>Översikt
Microsoft Azure Storage Data Movement Library är ett bibliotek med öppen källkod för flera plattformar som är utformat för högpresterande överföringen, hämtar och kopiering av Azure Storage-Blobbar och filer. Det här biblioteket är core data movement framework som används av [AzCopy](../storage-use-azcopy.md). Data Movement Library ger praktiska metoder som inte är tillgängliga i vår traditionella [.NET Azure Storage-klientbibliotek](../blobs/storage-dotnet-how-to-use-blobs.md). Detta inkluderar möjligheten att ange antalet parallella åtgärder, spåra Överföringsförlopp, enkelt återuppta en annullerad överföring och mycket mer.  

.NET Core, vilket innebär att du kan använda den när du skapar .NET appar för Windows, Linux och macOS används också i det här biblioteket. Om du vill veta mer om .NET Core kan referera till den [.NET Core-dokumentation](https://dotnet.github.io/). Det här biblioteket fungerar även för traditionella .NET Framework-appar för Windows. 

Det här dokumentet visar hur du skapar ett .NET Core-konsolprogram som körs på Windows, Linux och macOS och utför följande scenarier:

- Ladda upp filer och kataloger till Blob Storage.
- Definiera antalet parallella åtgärder vid överföring av data.
- Spåra förloppet för data transfer.
- Återuppta avbrutna dataöverföring. 
- Kopiera filen från URL: en till Blob Storage. 
- Kopiera från Blob Storage till Blob Storage.

**Vad du behöver:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Ett [Azure Storage-konto](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> Den här handboken förutsätts att du redan är bekant med [Azure Storage](https://azure.microsoft.com/services/storage/). Om inte, läser den [introduktion till Azure Storage](storage-introduction.md) dokumentation är bra. Viktigast av allt du behöver [skapa ett lagringskonto](storage-create-storage-account.md#create-a-storage-account) att börja använda Data Movement Library.
> 
> 

## <a name="setup"></a>Konfiguration  

1. Besök den [.NET Core installationsguiden](https://www.microsoft.com/net/core) att installera .NET Core. När du väljer din miljö, väljer du kommandoradsalternativet. 
2. Skapa en katalog för ditt projekt från kommandoraden. Navigera till den här katalogen, Skriv `dotnet new console -o <sample-project-name>` att skapa en C# console-projekt.
3. Öppna den här katalogen i Visual Studio-koden. Det här steget kan utföras snabbt via kommandoraden genom att skriva `code .` i Windows.  
4. Installera den [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) från Visual Studio Code Marketplace. Starta om Visual Studio-koden. 
5. Du bör nu se två frågor. En är för att lägga till ”nödvändiga resurser för att bygga och debug”. Klicka på ”Ja”. En annan frågan är för att återställa Olösta beroenden. Klicka på ”Återställ”.
6. Ändra `launch.json` under `.vscode` att använda externa terminal som en konsol. Den här inställningen ska läsa som` "console": "externalTerminal"`
7. Visual Studio Code kan du felsöka .NET Core-program. Träffa `F5` att köra ditt program och kontrollera att inställningarna fungerar. Du bör se ”Hello World”! ut till konsolen. 

## <a name="add-data-movement-library-to-your-project"></a>Lägg till Data Movement Library i projektet

1. Lägg till den senaste versionen av Data Movement Library till den `dependencies` avsnitt i din `<project-name>.csproj` fil. Den här versionen är vid tidpunkten för skrivning`"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. En uppmaning visas om du vill återställa ditt projekt. Klicka på återställningsknappen ””. Du kan även återställa projektet från kommandoraden genom att skriva kommandot `dotnet restore` i roten av projektkatalogen.

Ändra `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Ställ in stommen i ditt program
Det första vi gör ställs in ”stommen” koden för vårt program. Den här koden oss uppmanas att ange ett namn och lagringskontonyckel och använder dessa autentiseringsuppgifter för att skapa en `CloudStorageAccount` objekt. Det här objektet används för att interagera med våra Storage-konto i alla scenarier för överföring. Koden uppmanar oss att välja vilken typ av överföringen som vi vill köra. 

Ändra `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

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

## <a name="transfer-local-file-to-azure-blob"></a>Överför lokal fil till Azure-Blob
Lägg till metoder `GetSourcePath` och `GetBlob` till `Program.cs`:

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

Ändra den `TransferLocalFileToAzureBlob` metoden:

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

Den här koden uppmanar oss att sökvägen till en lokal fil, namnet på en ny eller befintlig behållare och namnet på en ny blob. Den `TransferManager.UploadAsync` metoden utför uppladdningen med den här informationen. 

Träffa `F5` att köra programmet. Du kan kontrollera att överföringen uppstod genom att visa ditt lagringskonto med den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Ange antalet parallella åtgärder
En bra funktion som erbjuds av Data Movement Library är möjligheten att ange antalet parallella åtgärder för att öka genomflödet data transfer. Som standard Data Movement Library anger antalet parallella åtgärder till 8 * antal kärnor på datorn. 

Tänk på att många parallella åtgärder i en miljö med låg bandbredd kan överväldigande nätverksanslutningen och faktiskt förhindra operations fullständigt slutförs. Du behöver experimentera med den här inställningen för att avgöra vad som fungerar bäst baserat på din tillgängliga nätverksbandbredd. 

Lägg till kod som gör att vi kan ange antalet parallella åtgärder. Nu ska vi också lägga till kod som gånger hur lång tid det tar att slutföra överföringen.

Lägg till en `SetNumberOfParallelOperations` metod för att `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändra den `ExecuteChoice` metod du vill använda `SetNumberOfParallelOperations`:

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

Ändra den `TransferLocalFileToAzureBlob` metod du vill använda en timer:

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

## <a name="track-transfer-progress"></a>Spåra förloppet för överföring
Det är bra att känna till hur lång tid det tog för våra data att överföra. Dock att du kan se förloppet för vår överföring *under* överföringen blir ännu bättre. För att uppnå det här scenariot måste vi skapa en `TransferContext` objekt. Den `TransferContext` objektet kommer på två sätt: `SingleTransferContext` och `DirectoryTransferContext`. Är den förra för att överföra en fil (vilket är vad vi gör nu) och denna är för att överföra en katalog med filer (som vi lägger till senare).

Lägg till metoder `GetSingleTransferContext` och `GetDirectoryTransferContext` till `Program.cs`: 

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

Ändra den `TransferLocalFileToAzureBlob` metod du vill använda `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Återuppta en annullerad överföring
En annan lämplig funktion som erbjuds av Data Movement Library är möjligheten att återuppta en annullerad överföring. Lägg till kod som gör att vi kan tillfälligt avbryta överföringen genom att skriva `c`, och sedan fortsätta överföringen 3 sekunder senare.

Ändra `TransferLocalFileToAzureBlob`:

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

Fram till nu vår `checkpoint` värdet alltid har angetts till `null`. Om vi avbryter överföringen vi nu hämta den senaste kontrollpunkten på vår överföring och använda den här nya kontrollpunkten i vår kontext för överföring. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Överföra lokala katalog till Azure Blob-katalog
Det vore förväntar dig om Data Movement Library kan bara överföra en fil i taget. Som tur är är detta inte fallet. Data Movement Library ger möjlighet att överföra en katalog med filer och alla dess underkataloger. Lägg till kod som gör det möjligt för oss att göra just.

Lägg först till metoden `GetBlobDirectory` till `Program.cs`:

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

Ändra sedan `TransferLocalDirectoryToAzureBlobDirectory`:

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

Det finns några skillnader mellan den här metoden och metod för att överföra en fil. Nu ska du använda `TransferManager.UploadDirectoryAsync` och `getDirectoryTransferContext` metoden som vi skapade tidigare. Dessutom kan vi ger nu en `options` värde till vår överföringen, vilket gör att vi kan tyda på att vi vill inkludera undermappar i vår överföringen. 

## <a name="copy-file-from-url-to-azure-blob"></a>Kopiera filen från URL: en till Azure-Blob
Nu ska vi lägga till kod som gör att vi kan kopiera en fil från en URL till en Azure-Blob. 

Ändra `TransferUrlToAzureBlob`:

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

Det är en viktig användningsfall för den här funktionen när du behöver flytta data från en annan molntjänst (t.ex. AWS) till Azure. Så länge som du har en URL som ger dig tillgång till resursen du kan enkelt flytta resursen till Azure-BLOB med hjälp av den `TransferManager.CopyAsync` metoden. Den här metoden skapar också en ny boolesk parameter. Parametern `true` anger att vi vill göra en asynkron serversidan kopia. Parametern `false` anger en synkron kopia - vilket innebär att resursen är laddas ned till våra lokala datorn först och sedan överförs till Azure Blob. Dock finns synkron kopia för närvarande bara för att kopiera från en Azure Storage-resurs till en annan. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Överför Azure Blob till Azure Blob
En annan funktion som unikt tillhandahålls av Data Movement Library är möjligheten att kopiera från en Azure Storage-resurs till en annan. 

Ändra `TransferAzureBlobToAzureBlob`:

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

I det här exemplet anger vi boolesk parameter i `TransferManager.CopyAsync` till `false` att indikera att vi vill göra en synkron kopia. Detta innebär att resursen är laddas ned till våra lokala datorn först och sedan har överförts till Azure-Blob. Alternativet synkrona kopior är ett bra sätt att kontrollera att din Kopieringsåtgärden har en konsekvent hastighet. Däremot är hastigheten för en asynkron serversidan kopia beroende av den tillgängliga nätverksbandbredden på servern, vilket kan variera. Synkron copy kan dock ge ytterligare utgång kostnaden jämfört med asynkron kopia. Den rekommenderade metoden är att använda synkron kopia i en Azure VM är i samma region som ditt källa storage-konto för att undvika kostnader för utgående trafik.

## <a name="conclusion"></a>Sammanfattning
Vårt program för flytt av data är slutförd. [Fullständig kodexemplet finns på GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>Nästa steg
I den här komma igång, skapat vi ett program som samverkar med Azure Storage och körs på Windows, Linux och macOS. Den här komma igång fokuserar på Blob Storage. Denna samma kunskap kan dock användas för lagring. Lär dig mer genom att checka ut [referensdokumentationen för Azure Storage Data Movement Library](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]




