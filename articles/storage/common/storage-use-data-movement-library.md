---
title: Överföra Data med Microsoft Azure Storage Data Movement Library | Microsoft Docs
description: Använda biblioteket för flytt av Data för att flytta eller kopiera data till och från blob- och innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: ee24a055c6a3c53b9753728e13a7687e722c1c91
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526223"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Överföra Data med Microsoft Azure Storage Data Movement Library

## <a name="overview"></a>Översikt
Microsoft Azure Storage Data Movement Library är ett bibliotek med öppen källkod för flera plattformar som är utformad för höga prestanda som laddar upp, ladda ned och kopiera av Azure Storage-Blobbar och filer. Det här biblioteket är det ramverk flytt av data som driver [AzCopy](../storage-use-azcopy.md). Data Movement Library erbjuder praktiska metoder som inte är tillgängliga i vår traditionella [.NET Azure Storage-klientbiblioteket](../blobs/storage-dotnet-how-to-use-blobs.md). Detta inkluderar möjligheten att ange antalet parallella åtgärder, spåra överföringsprocessen, Fortsätt enkelt en avbrutna överföring och mycket mer.  

Det här biblioteket använder även .NET Core, vilket innebär att du kan använda den när du bygger .NET-appar för Windows, Linux och macOS. Mer information om .NET Core i den [.NET Core-dokumentation](https://dotnet.github.io/). Det här biblioteket fungerar även för traditionella .NET Framework-appar för Windows. 

Det här dokumentet visar hur du skapar ett .NET Core-konsolprogram som körs på Windows, Linux och macOS och utför följande scenarier:

- Ladda upp filer och kataloger till Blob Storage.
- Ange hur många parallella åtgärder vid överföring av data.
- Spåra överföringsprocessen för data.
- Återuppta avbrutna dataöverföring. 
- Kopiera filen från URL: en till Blob Storage. 
- Kopiera från Blob Storage till Blob Storage.

**Vad du behöver:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Ett [Azure Storage-konto](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> Den här guiden förutsätter att du redan är bekant med [Azure Storage](https://azure.microsoft.com/services/storage/). Om inte, läser den [introduktion till Azure Storage](storage-introduction.md) dokumentation är till hjälp. Framför allt du behöver [skapa ett lagringskonto](storage-create-storage-account.md#create-a-storage-account) att börja använda biblioteket för flytt av Data.
> 
> 

## <a name="setup"></a>Konfiguration  

1. Gå till den [installationsguide för .NET Core](https://www.microsoft.com/net/core) att installera .NET Core. När du väljer miljön väljer du kommandoradsalternativet. 
2. Skapa en katalog för ditt projekt från kommandoraden. Navigera till den här katalogen, Skriv `dotnet new console -o <sample-project-name>` att skapa ett C#-konsollprojekt.
3. Öppna den här katalogen i Visual Studio Code. Det här steget kan göras snabbt via kommandoraden genom att skriva `code .` i Windows.  
4. Installera den [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) från Visual Studio Code Marketplace. Starta om Visual Studio Code. 
5. Du bör nu se två frågor. En är för att lägga till ”tillgångar som krävs för att skapa och Felsök”. Klicka på ”Ja”. En annan fråga är för att återställa Olösta beroenden. Klicka på ”Återställ”.
6. Ändra `launch.json` under `.vscode` att använda extern terminal läge. Den här inställningen bör läsa som ` "console": "externalTerminal"`
7. Visual Studio Code kan du felsöka .NET Core-program. Tryck på `F5` att köra ditt program och kontrollera att din konfiguration fungerar. Du bör se ”Hello World”! ut till konsolen. 

## <a name="add-data-movement-library-to-your-project"></a>Lägg till bibliotek för flytt av Data i projektet

1. Lägg till den senaste versionen av Data Movement Library till den `dependencies` delen av din `<project-name>.csproj` fil. I det här dokumentet skrivs är den här versionen `"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. En uppmaning visas om du vill återställa ditt projekt. Klicka på återställningsknappen ””. Du kan även återställa ditt projekt från kommandoraden genom att skriva kommandot `dotnet restore` i roten av din projektkatalog.

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

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurera stommen i ditt program
Det första vi gör har ställts in ”stommen” koden för vårt program. Den här koden frågar oss efter ett namn och lagringskontonyckeln och använder dessa autentiseringsuppgifter för att skapa en `CloudStorageAccount` objekt. Det här objektet används för att interagera med våra Storage-konto i samtliga scenarier för överföring. Koden ber oss välja vilken typ av överföring åtgärd som ska köra. 

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

## <a name="transfer-local-file-to-azure-blob"></a>Överföra en lokal fil till Azure Blob
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

Den här koden ber oss om sökvägen till en lokal fil, namnet på en ny eller befintlig behållare och namnet på en ny blob. Den `TransferManager.UploadAsync` metoden utför uppladdningen med den här informationen. 

Tryck på `F5` att köra programmet. Du kan kontrollera att överföringen uppstod genom att visa ditt lagringskonto med den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Ange antalet parallella åtgärder
En bra funktion som erbjuds av Data Movement Library är möjligheten att ange antalet parallella åtgärder för att öka dataflödet för överföring. Som standard anger Data Movement Library antalet parallella åtgärder som 8 * antal kärnor på datorn. 

Tänk på att många parallella åtgärder i en miljö med låg bandbredd kan utnyttjandet nätverksanslutningen och faktiskt förhindra åtgärder från att slutföras helt. Du behöver att experimentera med den här inställningen för att avgöra vad som fungerar bäst beroende på din tillgängliga nätverksbandbredd. 

Vi lägger du till kod som gör att vi kan ange hur många parallella åtgärder. Nu ska vi också lägga till kod som gånger hur lång tid det tar att slutföra överföringen.

Lägg till en `SetNumberOfParallelOperations` metod för att `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändra den `ExecuteChoice` metod för att använda `SetNumberOfParallelOperations`:

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

Ändra den `TransferLocalFileToAzureBlob` metod du använder en timer:

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

## <a name="track-transfer-progress"></a>Spåra överföringsprocessen
Det är bra att känna till hur lång tid det tog för våra data att överföra. Men att kunna se förloppet för vår överföring *under* överföringen blir ännu bättre. För att uppnå det här scenariot måste vi skapa en `TransferContext` objekt. Den `TransferContext` objektet kommer på två sätt: `SingleTransferContext` och `DirectoryTransferContext`. Den tidigare versionen är för att överföra en enda fil (vilket är vad vi gör nu) och den andra är för att överföra en katalog med filer (som vi har lagt till senare).

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

Ändra den `TransferLocalFileToAzureBlob` metod för att använda `GetSingleTransferContext`:

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

## <a name="resume-a-canceled-transfer"></a>Återuppta en avbrutna överföring
En annan lämplig tjänst som erbjuds av Data Movement Library är möjligheten att återuppta en avbrutna överföring. Nu ska vi lägga till kod som gör att vi kan tillfälligt Avbryt överföringen genom att skriva `c`, återupptar överföringen 3 sekunder senare.

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

Fram tills nu, vår `checkpoint` värdet alltid har angetts till `null`. Nu, om vi avbryter överföringen vi hämta den senaste kontrollpunkten på vår överföring och sedan använda den här nya kontrollpunkten i vår kontext för överföring. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Överför lokala katalog till Azure Blob-katalog
Det vore en besvikelse om biblioteket för flytt av Data kan bara överföra en fil i taget. Som tur är är detta inte fallet. Data Movement Library ger möjlighet att överföra en katalog med filer och alla underkataloger. Vi lägger du till kod som gör att vi kan göra just detta.

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

Sedan kan ändra `TransferLocalDirectoryToAzureBlobDirectory`:

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

Det finns några skillnader mellan den här metoden och metod för att ladda upp en fil. Vi använder nu `TransferManager.UploadDirectoryAsync` och `getDirectoryTransferContext` metoden som vi skapade tidigare. Dessutom kan vi nu tillhandahålla en `options` värdet till våra överföringen, vilket gör att vi kan ange att vi vill inkludera underkataloger i vår överföring. 

## <a name="copy-file-from-url-to-azure-blob"></a>Kopiera filen från URL: en till Azure Blob
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

Det är ett viktigt användningsfall för den här funktionen när du behöver flytta data från en annan molntjänst (t.ex. AWS) till Azure. Så länge som du har en URL som ger dig åtkomst till resursen kan du enkelt flytta resursen i Azure-Blobar med hjälp av den `TransferManager.CopyAsync` metoden. Den här metoden inför en ny boolesk parameter. Ställa in den här parametern `true` anger att vi vill göra en asynkron serversidan kopia. Ställa in den här parametern `false` anger en synkron kopiera – vilket innebär att resursen är laddas ned till vår lokala datorn först och sedan överförs till Azure Blob. Dock finns synkron kopia för närvarande endast för att kopiera från en Azure Storage-resurs till en annan. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Överföra Azure-Blob till Azure-Blob
En annan funktion som tillhandahålls unikt av Data Movement Library är möjligheten att kopiera från en Azure Storage-resurs till en annan. 

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

I det här exemplet anger vi boolesk parameter `TransferManager.CopyAsync` till `false` att indikera att vi vill göra en synkron kopia. Det innebär att resursen är laddas ned till vår lokala datorn först och sedan överförs till Azure Blob. Alternativet synkron kopia är ett bra sätt att kontrollera att din kopieringen har en konsekvent hastighet. Däremot är hastigheten på en asynkron serversidan kopia beroende av den tillgängliga nätverksbandbredden på servern, vilket kan variera. Synkron copy kan dock ge ytterligare utgående kostnader jämfört med asynkron kopia. Den rekommenderade metoden är att använda synkron kopia i en Azure virtuell dator som är i samma region som din källagringskontot för att undvika kostnader för utgående trafik.

## <a name="conclusion"></a>Sammanfattning
Vårt program för flytt av data är slutförd. [Fullständig kodexemplet finns på GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>Nästa steg
I den här komma igång, skapade vi ett program som interagerar med Azure Storage och körs på Windows, Linux och macOS. Den här komma igång fokuserar på Blob Storage. Den här samma kunskapen kan dock tillämpas till File Storage. Om du vill veta mer, Kolla in [referensdokumentation för Azure Storage Data Movement Library](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]




