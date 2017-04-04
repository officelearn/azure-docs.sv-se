---
title: "Komma igång med Azure File Storage i Windows | Microsoft Docs"
description: "Lagra fildata i molnet med Azure File Storage och montera din molnbaserade filresurs från en virtuell Azure-dator eller från ett lokalt program som kör Windows."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: fcdeac53c79551000b48a47a1afc65e082bcc692
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-file-storage-on-windows"></a>Komma igång med Azure File Storage i Windows
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

Information om hur du använder File Storage med Linux finns i [Använda Azure File Storage med Linux](storage-how-to-use-files-linux.md).

Information om skalbarhets- och prestandamål för File Storage finns i [Skalbarhets- och prestandamål för Azure Storage](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files).

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## <a name="video-using-azure-file-storage-with-windows"></a>Video: Använda Azure File Storage med Windows
Här är en video som visar hur du skapar och använder Azure-filresurser i Windows.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Windows/player]
> 
> 

## <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här komma igång-kursen beskriver grunderna i Microsoft Azure File Storage. I den här självstudiekursen ska du:

* Använd Azure Portal och PowerShell för att skapa en ny Azure-filresurs, lägga till en katalog, ladda upp en lokal fil till resursen och visa en lista med filerna i katalogen.
* Montera filresursen, på samma sätt som du monterar en SMB-resurs.
* Använda Azure Storage-klientbiblioteket för .NET för att komma åt filresursen från ett lokalt program. Skapa ett konsolprogram och utföra följande åtgärder med filresursen:
  * Skriva innehållet i en fil i resursen till konsolfönstret.
  * Ange kvoten (den största tillåtna storleken) för filresursen.
  * Skapa en signatur för delad åtkomst för en fil som använder en princip för delad åtkomst som definierats för resursen.
  * Kopiera en fil till en annan fil i samma lagringskonto.
  * Kopiera en fil till en blobb i samma lagringskonto.
* Använd Azure Storage-mätvärden för felsökning

Nu stöds fillagring för alla lagringskonton, så du kan antingen använda ett befintligt lagringskonto eller skapa ett nytt. Information om hur du skapar ett lagringskonto finns i [Skapa ett lagringskonto](storage-create-storage-account.md#create-a-storage-account).

## <a name="use-the-azure-portal-to-manage-a-file-share"></a>Använda Azure Portal för att hantera en filresurs
[Azure Portal](https://portal.azure.com) tillhandahåller ett användargränssnitt för kunder som hjälper dem att hantera filresurser. Från portalen kan du:

* Skapa filresursen
* Ladda upp och ladda ned filer till och från filresursen
* Övervaka den faktiska användningen av varje filresurs
* Justera storlekskvoten för resurser
* Hämta `net use`-kommandot för att sedan montera filresursen från en Windows-klient

### <a name="create-file-share"></a>Skapa en filresurs
1. Logga in på Azure Portal.
2. Klicka på **Storage-konton** eller **Storage-konton (klassiska)** på navigeringsmenyn.
   
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-dotnet-how-to-use-files/files-create-share-0.png)
3. Välj ditt lagringskonto.
   
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-dotnet-how-to-use-files/files-create-share-1.png)
4. Välj tjänsten ”Filer”.
   
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-dotnet-how-to-use-files/files-create-share-2.png)
5. Klicka på ”Filresurser” och följ länken för att skapa din första filresurs.
   
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-dotnet-how-to-use-files/files-create-share-3.png)
6. Fyll i filresursens namn och storleken på filresursen (upp till 5 120 GB) för att skapa din första filresurs. När filresursen har skapats kan du montera den från alla filsystem som stöder SMB 2.1 eller SMB 3.0.
   
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-dotnet-how-to-use-files/files-create-share-4.png)

### <a name="upload-and-download-files"></a>Ladda upp och ladda ned filer
1. Välj en filresurs som du redan har skapat.
   
    ![Skärmbild som visar hur du laddar upp och laddar ned filer från portalen](./media/storage-dotnet-how-to-use-files/files-upload-download-1.png)
2. Öppna användargränssnittet för filöverföring genom att klicka på **Överför**.
   
    ![Skärmbild som visar hur du laddar upp filer från portalen](./media/storage-dotnet-how-to-use-files/files-upload-download-2.png)
3. Högerklicka på en fil och välj **Hämta** för att ladda ned den lokalt.
   
    ![Skärmbild som visar hur du laddar ned en fil från portalen](./media/storage-dotnet-how-to-use-files/files-upload-download-3.png)

### <a name="manage-file-share"></a>Hantera en filresurs
1. Klicka på **Kvot** om du vill ändra storleken på filresursen (upp till 5 120 GB).
   
    ![Skärmbild som visar hur du konfigurerar kvoten för filresursen](./media/storage-dotnet-how-to-use-files/files-manage-1.png)
2. Klicka på **Anslut** om du vill hämta kommandoraden för att montera filresursen från Windows.
   
    ![Skärmbild som visar hur du monterar filresursen](./media/storage-dotnet-how-to-use-files/files-manage-2.png)
   
    ![Skärmbild som visar hur du monterar filresursen](./media/storage-dotnet-how-to-use-files/files-manage-3.png)
   
   > [!TIP]
   > Du hittar åtkomstnyckeln för lagringskontot genom att klicka på **Inställningar** i lagringskontot och sedan på **Åtkomstnycklar**.
   > 
   > 
   
    ![Skärmbild som visar hur du hittar åtkomstnyckeln för lagringskontot](./media/storage-dotnet-how-to-use-files/files-manage-4.png)
   
    ![Skärmbild som visar hur du hittar åtkomstnyckeln för lagringskontot](./media/storage-dotnet-how-to-use-files/files-manage-5.png)

## <a name="use-powershell-to-manage-a-file-share"></a>Hantera en filresurs med hjälp av PowerShell
Du kan också skapa och hantera filresurser med hjälp av Azure PowerShell.

### <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Installera PowerShell-cmdlets för Azure Storage
Innan du kan använda PowerShell laddar du ned och installerar Azure PowerShell-cmdlets. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Vi rekommenderar att du laddar ned och installerar eller uppgradera till den senaste Azure PowerShell-modulen.
> 
> 

Öppna ett Azure PowerShell-fönster genom att klicka på **Start** och skriva **Windows PowerShell**. Azure Powershell-modulen läses in i PowerShell-fönstret.

### <a name="create-a-context-for-your-storage-account-and-key"></a>Skapa en kontext för ditt lagringskonto och din nyckel
Nu är det dags att skapa kontexten för lagringskontot. Kontexten innehåller lagringskontots namn och åtkomstnyckel. Anvisningar för hur du kopierar din kontonyckel från [Azure Portal](https://portal.azure.com) finns i [Visa och kopiera åtkomstnycklar för lagring](storage-create-storage-account.md#view-and-copy-storage-access-keys).

Ersätt `storage-account-name` och `storage-account-key` med lagringskontots namn och nyckel i följande exempel.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

### <a name="create-a-new-file-share"></a>Skapa en ny filresurs
Nu ska du skapa en ny resurs, med namnet `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Nu har du en filresurs i File Storage. Härnäst ska vi lägga till en katalog och en fil.

> [!IMPORTANT]
> Namnet på filresursen får bara innehålla gemener. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

### <a name="create-a-directory-in-the-file-share"></a>Skapa en katalog i filresursen
Nu ska du skapa en katalog i resursen. I följande exempel heter katalogen `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

### <a name="upload-a-local-file-to-the-directory"></a>Ladda upp en lokal fil till katalogen
Nu ska du ladda upp en lokal fil till katalogen. I följande exempel laddar vi upp en fil från `C:\temp\Log1.txt`. Ändra sökvägen till filen så att den pekar på en giltig fil på den lokala datorn.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

### <a name="list-the-files-in-the-directory"></a>Visa en lista med filerna i katalogen
Om du vill visa filen i katalogen kan du visa en lista med alla filer i katalogen. Det här kommandot returnerar filer och underkataloger (om det finns några) i katalogen CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile returnerar en lista över filer och kataloger för det katalogobjekt som du har angett. ”Get-AzureStorageFile -Share $s” returnerar en lista över filer och kataloger i rotkatalogen. Om du vill hämta en lista över filerna i en underkatalog måste du skicka underkatalogen till Get-AzureStorageFile. Detta händer: den första delen av kommandot fram till pipe returnerar en kataloginstans av underkatalogen CustomLogs. Därefter skickas detta till Get-AzureStorageFile, som returnerar filerna och katalogerna i CustomLogs.

### <a name="copy-files"></a>Kopiera filer
Från och med version 0.9.7 av Azure PowerShell kan du kopiera en fil till en annan fil, en fil till en blobb eller en blobb till en fil. Nedan ser du hur du utför dessa kopieringsåtgärder med PowerShell-cmdlets.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```

## <a name="mount-the-file-share"></a>Montera filresursen
Med stöd för SMB 3.0 stöder File Storage nu kryptering och beständiga referenser från SMB 3.0-klienter. Stöd för kryptering innebär att SMB 3.0-klienter kan montera en filresurs från var som helst, inklusive från:

* En virtuell Azure-dator i samma region (stöds även av SMB 2.1)
* En virtuell Azure-dator i en annan region (endast SMB 3.0)
* Ett lokalt klientprogram (endast SMB 3.0)

Vilken SMB-version som används när en klient ansluter till File Storage beror på vilken SMB-version som stöds av operativsystemet. Tabellen nedan innehåller en sammanfattning av stödet för Windows-klienter. Besök den här bloggen om du vill ha mer information om [SMB-versioner](http://blogs.technet.com/b/josebda/archive/2013/10/02/windows-server-2012-r2-which-version-of-the-smb-protocol-smb-1-0-smb-2-0-smb-2-1-smb-3-0-or-smb-3-02-you-are-using.aspx).

| Windows-klient | SMB-version som stöds |
|:--- |:--- |
| Windows 7 |SMB 2.1 |
| Windows Server 2008 R2 |SMB 2.1 |
| Windows 8 |SMB 3.0 |
| Windows Server 2012 |SMB 3.0 |
| Windows Server 2012 R2 |SMB 3.0 |
| Windows 10 |SMB 3.0 |

### <a name="mount-the-file-share-from-an-azure-virtual-machine-running-windows"></a>Montera filresursen från en virtuell Azure-dator som kör Windows
För att demonstrera hur du monterar en Azure-filresurs ska vi nu skapa en virtuell Azure-dator som kör Windows och fjärransluta till den för att montera resursen.

1. Börja med att skapa en ny virtuell Azure-dator genom att följa anvisningarna i [Skapa en virtuell Windows-dator på Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Fjärranslut sedan till den virtuella datorn genom att följa anvisningarna i [Logga in på en Windows-dator från Azure Portal](../virtual-machines/virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
3. Öppna ett PowerShell-fönster på den virtuella datorn.

### <a name="persist-your-storage-account-credentials-for-the-virtual-machine"></a>Spara autentiseringsuppgifterna för ditt lagringskonto för den virtuella datorn
Innan du monterar till filresursen sparar du autentiseringsuppgifterna för ditt lagringskonto på den virtuella datorn. Det här steget gör att Windows kan återansluta till filresursen automatiskt när den virtuella datorn startar om. För att spara dina kontouppgifterna kör du kommandot `cmdkey` från PowerShell-fönstret på den virtuella datorn. Ersätt `<storage-account-name>` med namnet på ditt lagringskonto och `<storage-account-key>` med din åtkomstnyckel för lagring. Du måste uttryckligen ange domänen ”AZURE” som i exemplet nedan. 

```
cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
```

Nu återansluter Windows till din filresurs när den virtuella datorn startas om. Du kan kontrollera att resursen har återanslutit genom att köra kommandot `net use` från ett PowerShell-fönster.

Observera att autentiseringsuppgifter endast sparas i kontexten där `cmdkey` körs. Om du utvecklar ett program som körs som en tjänst måste du spara dina autentiseringsuppgifter i den kontexten också.

### <a name="mount-the-file-share-using-the-persisted-credentials"></a>Montera filresursen med de sparade autentiseringsuppgifterna
När du har en fjärranslutning till den virtuella datorn kan du köra kommandot `net use` för att montera filresursen med följande syntax. Ersätt `<storage-account-name>` med namnet på ditt lagringskonto och `<share-name>` med namnet på din File Storage-resurs.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

example :
net use z: \\samples.file.core.windows.net\logs
```

Eftersom du sparade autentiseringsuppgifterna för ditt lagringskonto i föregående steg så behöver du inte ange dem med kommandot `net use`. Om du inte redan har sparat dina autentiseringsuppgifter tar du med dem som en parameter som skickas till kommandot `net use`, som du ser i följande exempel.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:AZURE\<storage-account-name> <storage-account-key>

example :
net use z: \\samples.file.core.windows.net\logs /u:AZURE\samples <storage-account-key>
```

Nu kan du arbeta med File Storage-resursen från den virtuella datorn precis som med andra enheter. Du kan köra standardfilkommandon från Kommandotolken eller visa den monterade resursen och dess innehåll från Utforskaren. Du kan också köra kod på den virtuella datorn som ansluter till filresursen med standard-I/O-API:er för Windows-filer, till exempel de som finns i [System.IO-namnrymderna](http://msdn.microsoft.com/library/gg145019.aspx) i .NET Framework.

Du kan också montera filresursen från en roll som körs i en Azure-molntjänst genom att fjärransluta till rollen.

### <a name="mount-the-file-share-from-an-on-premises-client-running-windows"></a>Montera filresursen från en lokal klient som kör Windows
Innan du monterar filresursen från en lokal klient måste du göra följande:

* Installera en version av Windows som stöder SMB 3.0. Windows använder SMB 3.0-kryptering för att överföra data på ett säkert sätt mellan din lokala klient och Azure-filresursen i molnet.
* Öppna Internetåtkomst för port 445 (TCP utgående) i det lokala nätverket eftersom det krävs av SMB-protokollet.

> [!NOTE]
> Vissa Internetleverantörer kan blockera port 445. Hör efter med leverantören om det behövs.
> 
> 

## <a name="develop-with-file-storage"></a>Utveckla med File Storage
Om du vill skriva kod som anropar File Storage kan du använda lagringsklientbiblioteken för .NET och Java eller REST-API:et för Azure Storage. Exemplet i det här avsnittet visar hur du arbetar med en filresurs med hjälp av [Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/mt347887.aspx) från ett enkelt konsolprogram som körs på skrivbordet.

### <a name="create-the-console-application-and-obtain-the-assembly"></a>Skapa konsolprogrammet och hämta monteringen
Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg beskriver hur du skapar ett konsolprogram i Visual Studio 2017, men stegen är liknande i andra versioner av Visual Studio.

1. Välj **Arkiv** > **Nytt** > **Projekt**
2. Välj **Installerat** > **Mallar** > **Visual C#** > **Windows Classic Desktop**
3. Välj **Konsolprogram (.NET Framework)**
4. Ange ett namn för ditt program i fältet **Namn**
5. Välj **OK**

Alla kodexempel i den här självstudiekursen kan läggas till i `Main()`-metoden i konsolprogrammets `Program.cs`-fil.

Du kan använda Azure Storage-klientbiblioteket i alla typer av .NET-program, t.ex. en Azure-molntjänst eller webbapp, eller i dator- och mobilprogram. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs
Det finns två paket som du måste referera till i ditt projekt för att slutföra den här kursen:

* [Microsoft Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): det här paketet ger programmatisk åtkomst till dataresurser i ditt lagringskonto.
* [Microsoft Azure Configuration Manager-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): det här paketet tillhandahåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta båda paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
2. Sök online efter ”WindowsAzure.Storage” och klicka på **Installera** för att installera Storage-klientbiblioteket och alla dess beroenden.
3. Sök online efter ”WindowsAzure.ConfigurationManager” och klicka på **Installera** för att installera Azure Configuration Manager.

### <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Spara autentiseringsuppgifterna för ditt lagringskonto i app.config-filen
Nu ska du spara dina autentiseringsuppgifter i projektets app.config-fil. Redigera app.config-filen så att den ser ut som i följande exempel. Ersätt `myaccount` med namnet på ditt lagringskonto och `mykey` med din åtkomstnyckel för lagring.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Den senaste versionen av Azure Storage-emulatorn har inte stöd för File Storage. Anslutningssträngen måste peka på ett Azure-lagringskonto i molnet för att fungera med File Storage.
> 
> 

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv
Öppna filen `Program.cs` från Solution Explorer och lägg till följande med hjälp av direktiv överst i filen.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage
using Microsoft.WindowsAzure.Storage.File; // Namespace for File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="access-the-file-share-programmatically"></a>Ansluta till filresursen via programmering
Lägg till följande kod i `Main()`-metoden (efter koden som visas ovan) för att hämta anslutningssträngen. Den här koden hämtar en referens till den fil som vi skapade tidigare och returnerar filens innehåll i konsolfönstret.

```csharp
// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Visa resultatet genom att köra konsolprogrammet.

### <a name="set-the-maximum-size-for-a-file-share"></a>Ange den största storleken för en filresurs
Från och med version 5.x av klientbiblioteket för Azure Storage kan du ange kvoten (eller den största storleken) för en filresurs, i antal gigabyte. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Genom att ange kvoten för en resurs kan du begränsa den totala storleken på filerna som lagras på resursen. Om den totala storleken på filerna som lagras på resursen överskrider kvoten som angetts för resursen kan klienterna inte öka storleken på befintliga filer eller skapa nya filer såvida inte dessa filer är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generera en signatur för delad åtkomst för en fil eller filresurs
Från och med version 5.x av klientbiblioteket för Azure Storage kan du generera en signatur för delad åtkomst (SAS) för en filresurs eller för en enskild fil. Du kan också skapa en princip för delad åtkomst på en filresurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en princip för delad åtkomst eftersom det ger dig möjlighet att återkalla signaturen för delad åtkomst om det behövs.

I följande exempel skapar vi en princip för delad åtkomst på en resurs och använder sedan principen för att ange begränsningarna för en signatur för delad åtkomst på en fil i resursen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Mer information om hur du skapar och använder signaturer för delad åtkomst finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md) och [Skapa och använda en SAS med Blob Storage](storage-dotnet-shared-access-signature-part-2.md).

### <a name="copy-files"></a>Kopiera filer
Från och med version 5.x av klientbiblioteket för Azure Storage kan du kopiera en fil till en annan fil, en fil till en blobb eller en blobb till en fil. I nästa avsnitt ser du hur du utför kopieringsåtgärderna genom programmering.

Du kan också använda AzCopy för att kopiera en fil till en annan eller för att kopiera en blobb till en fil eller tvärtom. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md).

> [!NOTE]
> Om du kopierar en blobb till en fil eller en fil till en blobb måste du använda en signatur för delad åtkomst (SAS) för att autentisera källobjektet, även om du kopierar inom samma lagringskonto.
> 
> 

**Kopiera en fil till en annan fil**

I följande exempel kopierar vi en fil till en annan fil i samma resurs. Eftersom den här kopieringsåtgärden kopierar mellan filer i samma lagringskonto kan du använda autentisering med delad nyckel för att utföra kopieringen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Kopiera en fil till en blobb**

I följande exempel skapar vi en fil och kopierar den till en blobb inom samma lagringskonto. I exemplet skapas en SAS för källfilen, som tjänsten använder för att autentisera åtkomsten till källfilen under kopieringen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Du kan kopiera en blobb till en fil på samma sätt. Om källobjektet är en blobb skapar du en SAS för att autentisera åtkomsten till blobben under kopieringen.

## <a name="troubleshooting-file-storage-using-metrics"></a>Felsöka File Storage med hjälp av mätvärden
Nu stöder Azure Storage Analytics mätvärden för File Storage. Med hjälp av mätvärdesdata kan du spåra begäranden och diagnostisera problem.

Du kan aktivera mätvärden för File Storage från [Azure Portal](https://portal.azure.com). Du kan också aktivera mätvärden via programmering genom att anropa åtgärden Ange egenskaper för filtjänsten via REST-API:et eller någon av dess motsvarigheter i klientbiblioteket för Azure Storage.

Följande exempel visar hur du använder klientbiblioteket för Azure Storage för .NET för att aktivera mätvärden för File Storage.

Lägg först till följande `using`-direktiv i `Program.cs`-filen, förutom de som du lade till ovan:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Observera att medan blobb-, tabell- och kölagringen använder den delade `ServiceProperties`-typen i namnrymden `Microsoft.WindowsAzure.Storage.Shared.Protocol` så använder File Storage sin egen typ, typen `FileServiceProperties` i namnrymden `Microsoft.WindowsAzure.Storage.File.Protocol`. Din kod måste dock referera till båda namnrymderna för att följande kod ska kompileras.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Du kan också gå till [Felsökningsartikeln om Azure-filer](storage-troubleshoot-file-connection-problems.md) för felsökningsinformation från slutpunkt till slutpunkt. 

## <a name="file-storage-faq"></a>Vanliga frågor och svar om File Storage
1. **Stöds Active Directory-baserad autentisering av File Storage?**
   
    För närvarande stöds inte AD-baserad autentisering eller ACL:er, men vi har det i åtanke för framtida versioner. Nu används i stället Azure Storage-kontonycklar för att tillhandahålla autentisering till filresursen. Vi erbjuder dock en lösning som använder signaturer för delad åtkomst (SAS) via REST-API:et eller klientbiblioteken. Med SAS kan du generera token med särskilda behörigheter som är giltiga under ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en viss fil. Alla som har denna token under tiden då den är giltig har skrivskyddad åtkomst till filen.
   
    SAS stöds endast via REST-API:et eller klientbiblioteken. När du monterar filresursen via SMB-protokollet kan du inte använda en SAS för att delegera åtkomst till dess innehåll. 

2. **Hur kan jag ge åtkomst till en specifik fil via en webbläsare?**
   Med SAS kan du generera token med särskilda behörigheter som är giltiga under ett angivet tidsintervall. Du kan till exempel generera ett token med skrivskyddad åtkomst till en viss fil under en viss tidsperiod. Alla som har den här URL:en kan hämta direkt från valfri webbläsare medan den är giltig. SAS-nycklar går lätt att generera från användargränssnitt som Storage Explorer.

3.   **Vad finns det för olika sätt att komma åt filer i Azure File Storage?**
    Du kan montera filresursen på den lokala datorn med SMB 3.0-protokollet eller med verktyg som [Storage Explorer](http://storageexplorer.com/) eller Cloudberry för att få åtkomst till filer i filresursen. Från programmet kan du använda Klientbibliotek, REST API eller Powershell för att få åtkomst till dina filer i Azure-filresursen.
    
4.   **Hur monterar jag Azure-filresursen på datorn lokalt?** Om port 445 (TCP utgående) är öppen och klienten stöder SMB 3.0-protokollet (*t.ex.* Windows 8 eller Windows Server 2012) kan du montera filresursen via SMB-protokollet. Kontakta din lokala Internet-leverantör för att avblockera porten. Under tiden kan du se dina filer med Lagringsutforskaren eller annat program från en tredje part, som till exempel Cloudberry.

5. **Räknas nätverkstrafiken mellan en virtuell Azure-dator och en filresurs som extern bandbredd som debiteras till prenumerationen?**
   
    Om filresursen och den virtuella datorn finns i olika områden debiteras trafiken mellan dem som extern bandbredd.
6. **Är nätverkstrafik mellan en virtuell dator och filresurs inom samma region kostnadsfri?**
   
    Ja. Trafiken är kostnadsfri om den är i samma region.
7. **Är anslutningen från lokala virtuella datorer till Azure File Storage beroende av Azure ExpressRoute?**
   
    Nej. Om du inte har ExpressRoute kan du fortfarande komma åt filresursen från lokala virtuella datorer under förutsättning att port 445 (TCP utgående) är öppen för Internetåtkomst. Men du kan använda ExpressRoute med File Storage om du vill.
8. **Är ett ”filresursvittne” för ett redundanskluster en av användningsfallen för Azure File Storage?**
   
    Detta stöds inte för närvarande.
9. **Replikeras File Storage bara via LRS eller GRS för tillfället?**  
   
    Vi planerar att implementera stöd för RA-GRS men tidsplanen är inte fastställd än.
10. **När kan jag använda befintliga lagringskonton för Azure File Storage?**
   
    Nu är Azure File Storage aktiverat för alla lagringskonton.
11. **Kommer en namnbytesåtgärd också att läggas till i REST API:et?**
   
    Namnbyten stöds inte än i vårt REST-API.
12. **Går det att ha inbäddade resurser, det vill säga en resurs inuti en resurs?**
    
    Nej. Filresursen är den virtuella drivrutin som du kan montera, så kapslade resurser stöds inte.
13. **Är det möjligt att ange skrivskydd eller lässkydd för mappar inom resursen?**
    
    Du har inte den här kontrollnivån över behörigheterna om du monterar filresursen via SMB. Du kan dock åstadkomma detta genom att skapa en signatur för delad åtkomst (SAS) via REST-API:et eller klientbiblioteken.  
14. **Min prestanda var långsam när jag försökte zippa upp filer till File Storage. Vad ska jag göra?**
    
    Om du vill överföra ett stort antal filer till File Storage rekommenderar vi att du använder AzCopy, Azure PowerShell (Windows) eller Azure CLI (Linux/Unix) eftersom dessa verktyg har optimerats för nätverksöverföring.
15. **En snabbkorrigering släpptes för att fixa problemet med långsam prestanda med Azure Files**
    
    Windows-teamet släppte nyligen en korrigering som löser problemet med långsamma prestanda när kunden ansluter till Azure Storage från Windows 8.1 eller Windows Server 2012 R2. Mer information finns i KB-artikeln [Låga prestanda när du ansluter till Azure File Storage från Windows 8.1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).
16. **Använd Azure File Storage med IBM MQ**
    
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder att konfigurera Azure File Storage med deras tjänst. Mer information finns i [Konfigurera IBM MQ MIQM (Multi Instance Queue Manager) med Microsoft Azures filtjänst](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).
17. **Hur felsöker jag Azure File Storage-fel?**
    
    Du kan gå till [Felsökningsartikeln om Azure-filer](storage-troubleshoot-file-connection-problems.md) för felsökningsinformation från slutpunkt till slutpunkt.               

18. **Hur kan jag aktivera kryptering på serversidan för Azure Files?**

    [Kryptering på serversidan](storage-service-encryption.md) för Azure Files är för närvarande tillgängligt som en förhandsversion. I förhandsversionen kan du bara aktivera den här funktionen för nya Azure Resource Manager-lagringskonton som skapats med hjälp av [Azure Portal](https://portal.azure.com). Inga extra kostnader tillkommer för att aktivera den här funktionen. När du aktiverar Kryptering av lagringstjänst för Azure File Storage krypteras dina data automatiskt åt dig. 
    
    Vi planerar att lägga till stöd så att Kryptering av lagringstjänst kan aktiveras via [Azure PowerShell](/powershell/resourcemanager/azurerm.storage/v2.7.0/azurerm.storage), [Azure CLI](storage-azure-cli.md) och [Azure Storage Resource Provider REST-API:et](/rest/api/storagerp/storageaccounts). 
    Mer information om kryptering i vila i Azure Storage finns i avsnittet om [Kryptering av lagringstjänst](storage-service-encryption.md). Du kan också kontakta ssediscussions@microsoft.com om du har frågor under förhandsgranskningen.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure File Storage finns på följande länkar.

### <a name="conceptual-articles-and-videos"></a>Begreppsrelaterade artiklar och videoklipp
* [Azure Files Storage: ett friktionslöst moln-SMB-filsystem för Windows och Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Använd Azure File Storage med Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Verktygsstöd för File Storage
* [Använd Azure PowerShell med Azure Storage](storage-powershell-guide-full.md)
* [Använd AzCopy med Microsoft Azure Storage](storage-use-azcopy.md)
* [Använd Azure CLI:et med Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)
* [Felsökning av problem i Azure File Storage](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referens
* [Storage-klientbibliotek för .NET-referens](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API referens](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Blogginlägg
* [Azure File Storage finns nu allmänt tillgänglig](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inuti Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduktion till Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Bevara anslutningar till Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

