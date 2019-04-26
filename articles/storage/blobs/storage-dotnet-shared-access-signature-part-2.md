---
title: Skapa och använda en signatur för delad åtkomst (SAS) med Azure Blob storage | Microsoft Docs
description: Den här självstudien visar hur du skapar signaturer för delad åtkomst för användning med Blob storage och hur du använder dem i dina klientprogram.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.devlang: dotnet
ms.date: 05/15/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 5dcb9f16b589b8332d5fcf35c9d8b4cd914460f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392508"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage

[Del 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) av den här självstudiekursen utforskat signaturer för delad åtkomst (SAS) och beskrivs bästa praxis för att använda dem. Del 2 visar vi hur du skapar och sedan använda signaturer för delad åtkomst med Blob storage. Exemplen är skrivna i C# och använder Azure Storage-klientbiblioteket för .NET. I exemplen i den här självstudien:

* Generera en signatur för delad åtkomst i en behållare
* Generera en signatur för delad åtkomst för en blob
* Skapa en lagrad åtkomstprincip för att hantera signaturer på en behållare resurser
* Testa signaturer för delad åtkomst i en client-program

## <a name="about-this-tutorial"></a>Om den här självstudiekursen
I den här självstudiekursen skapar vi två konsolprogram som visar hur du skapar och använder signaturer för delad åtkomst för behållare och blobbar:

**Programmet 1**: Hanteringsprogram. Genererar en signatur för delad åtkomst för en behållare och en blob. Innehåller åtkomstnyckeln för lagringskontot i källkoden.

**Programmet 2**: Klientprogrammet. Åtkomst till behållare och blobnamn resurser med hjälp av signaturer för delad åtkomst som skapats med det första programmet. Använder endast de delade åtkomstsignaturer åtkomst-behållaren och blob-resurser – sker *inte* inkluderar åtkomstnyckel för lagringskontot.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Del 1: Skapa ett konsolprogram för att generera delade åtkomstsignaturer
Kontrollera först att du har Azure Storage-klientbiblioteket för .NET som installeras. Du kan installera den [NuGet-paketet](https://nuget.org/packages/WindowsAzure.Storage/ "NuGet-paketet") som innehåller den senaste sammansättningarna för klientbiblioteket. Det här är den rekommenderade metoden för att säkerställa att du har de senaste korrigeringarna. Du kan också hämta klientbiblioteket som en del av den senaste versionen av den [Azure SDK för .NET](https://azure.microsoft.com/downloads/).

Skapa ett nytt Windows-konsolprogram i Visual Studio och ge den namnet **GenerateSharedAccessSignatures**. Lägg till referenser till [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) och [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) genom att använda någon av följande metoder:

* Använd den [NuGet-Pakethanteraren](https://docs.nuget.org/consume/installing-nuget) i Visual Studio. Välj **projekt** > **hantera NuGet-paket**, Sök online efter varje paket (Microsoft.WindowsAzure.ConfigurationManager och WindowsAzure.Storage) och installera dem.
* Du kan också leta upp dessa sammansättningar i din installation av Azure SDK och Lägg till referenser till dem:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Lägg till följande överst i filen Program.cs **med** direktiv:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Redigera app.config-filen så att den innehåller en konfigurationsinställning med en anslutningssträng som pekar på ditt lagringskonto. Filen app.config bör se ut ungefär så här:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generera en signatur för delad åtkomst URI för en behållare
Börja med att vi lägga till en metod för att generera en signatur för delad åtkomst på en ny behållare. I det här fallet är signaturen inte associerad med en lagrad åtkomstprincip så den bedriver URI: N den information som anger dess förfallotid och behörigheter som den ger.

Lägg först till kod till den **Main()** metod för att bevilja åtkomst till ditt storage-konto och skapa en ny behållare:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Lägg sedan till en metod som genererar signatur för delad åtkomst för behållaren och returnerar signatur-URI:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Lägg till följande rader längst ned på den **Main()** sättet innan anropet till **Console.ReadLine()** att anropa **GetContainerSasUri()** och skriva signatur-URI till den konsolfönstret:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Kompilera och kör om du vill spara signaturen för delad åtkomst URI för den nya behållaren. URI: N blir ungefär så här:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

När du har kört koden kommer signatur för delad åtkomst som du skapade för behållaren att gälla för det närmaste dygnet. Signaturen ger en klient-behörighet för att lista blobar i behållaren och för att skriva nya blobbar i behållaren.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generera en signatur för delad åtkomst URI för en blob
Nu ska skriva vi liknande kod för att skapa en ny blob i behållaren och generera en signatur för delad åtkomst för den. Den här signaturen för delad åtkomst är inte kopplad till en lagrad åtkomstprincip, så att den omfattar starttid, förfallotid och behörighetsinformation i URI: N.

Lägg till en ny metod som skapar en ny blob och skriver text till den, och sedan genererar en signatur för delad åtkomst och returnerar signatur-URI:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Längst ned på den **Main()** metoden lägger du till följande rader att anropa **GetBlobSasUri()**, innan anropet till **Console.ReadLine()**, och skriva signatur för delad åtkomst URI till konsolfönstret:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Kompilera och kör om du vill spara signaturen för delad åtkomst URI för den nya bloben. URI: N blir ungefär så här:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Skapa en lagrad åtkomstprincip för behållaren
Nu ska vi skapa en lagrad åtkomstprincip på den behållare som definierar begränsningarna för alla signaturer för delad åtkomst som är associerade med den.

I föregående exempel kan angav vi starttiden (implicit eller explicit), förfallotid och behörigheter på signatur för delad åtkomst URI själva. I följande exempel har ange vi dessa på lagrad åtkomstprincip, inte efter signatur för delad åtkomst. Detta gör att vi kan ändra de här begränsningarna utan att återutfärda signatur för delad åtkomst.

Det är möjligt att ha en eller flera av begränsningarna för signatur för delad åtkomst och resten på lagrad åtkomstprincip. Du kan dock endast ange starttid, förfallotid och behörigheter i ett och samma ställe eller den andra. Du kan inte exempelvis anger behörigheter för signatur för delad åtkomst och även ange dem på lagrad åtkomstprincip.

När du lägger till en lagrad åtkomstprincip till en behållare måste du hämta behållarens befintliga behörigheter, Lägg till ny åtkomstprincip och sedan ange behållarens behörigheter.

Lägg till en ny metod som skapar en ny lagrad åtkomstprincip för en behållare och returnerar namnet på principen:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

Längst ned på den **Main()** sättet innan anropet till **Console.ReadLine()**, Lägg till följande rader till första Rensa eventuella befintliga principer för åtkomst och sedan anropa den  **CreateSharedAccessPolicy()** metod:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

När du avmarkerar åtkomstprinciper för en behållare, måste du först hämta behållarens befintliga behörigheter, och sedan avmarkera behörigheterna sedan ange behörigheter igen.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generera en signatur för delad åtkomst URI för behållaren som använder en åtkomstprincip
Nu ska skapa vi en annan signatur för delad åtkomst för behållare som vi skapade tidigare, men den här gången vi koppla signaturen lagrad åtkomstprincip som vi skapade i föregående exempel.

Lägg till en ny metod för att generera en annan signatur för delad åtkomst för behållaren:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Längst ned på den **Main()** sättet innan anropet till **Console.ReadLine()**, lägger du till följande rader att anropa den **GetContainerSasUriWithPolicy** metod:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generera en signatur för delad åtkomst URI för Blob som använder en åtkomstprincip
Slutligen kan vi lägga till en liknande metod för att skapa en annan blob och generera en signatur för delad åtkomst som är associerat med en lagrad åtkomstprincip.

Lägg till en ny metod för att skapa en blob och generera en signatur för delad åtkomst:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Längst ned på den **Main()** sättet innan anropet till **Console.ReadLine()**, lägger du till följande rader att anropa den **GetBlobSasUriWithPolicy** metod:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

Den **Main()** metoden bör nu se ut så här i sin helhet. Kör det för att skriva signatur för delad åtkomst URI: er till konsolfönstret och kopiera och klistra in dem i en textfil för användning i den andra delen av den här självstudien.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

När du kör konsolprogrammet GenerateSharedAccessSignatures visas utdata som liknar följande. Det här är signaturer för delad åtkomst som du använder i en del 2 av självstudiekursen.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Del 2: Skapa ett konsolprogram för att testa signaturer för delad åtkomst
Om du vill testa signaturer för delad åtkomst som skapats i föregående exempel kan skapa vi ett andra konsolprogram som använder signaturer för att utföra åtgärder på behållaren och för en blob.

> [!NOTE]
> Om mer än 24 timmar har passerat sedan du slutfört den första delen av självstudien, längre signaturer som du genererade inte giltig. I det här fallet bör du köra koden i det första konsolprogrammet för att generera nya delade åtkomstsignaturer för användning i den andra delen av kursen.
>

Skapa ett nytt Windows-konsolprogram i Visual Studio och ge den namnet **ConsumeSharedAccessSignatures**. Lägg till referenser till [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) och [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), som du gjorde tidigare.

Lägg till följande överst i filen Program.cs **med** direktiv:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

I själva den **Main()** metoden Lägg till följande strängkonstanter, ändra deras värden till signaturer för delad åtkomst som du genererade i del 1 av självstudiekursen.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Lägg till en metod om du vill prova container åtgärder med hjälp av en signatur för delad åtkomst
Nu ska vi lägga till en metod som testar vissa åtgärder för behållaren med hjälp av en signatur för delad åtkomst för behållaren. Signatur för delad åtkomst används för att returnera en referens till behållaren måste autentisera åtkomsten till den behållare baserat på enbart signaturen.

Lägg till följande metod i Program.cs:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Uppdatera den **Main()** metod för att anropa **UseContainerSAS()** med både signaturer för delad åtkomst som du skapade för behållaren:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Lägg till en metod om du vill prova blobåtgärder med hjälp av en signatur för delad åtkomst
Slutligen kan vi lägga till en metod som testar vissa blobåtgärder med hjälp av en signatur för delad åtkomst i blobben. I det här fallet använder vi konstruktorn **CloudBlockBlob(String)**, och skickar signaturen för delad åtkomst, att returnera en referens till bloben. Inga andra autentisering krävs. den är baserad på enbart signaturen.

Lägg till följande metod i Program.cs:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Uppdatera den **Main()** metod för att anropa **UseBlobSAS()** med både signaturer för delad åtkomst som du skapade i blobben:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Kör konsolprogrammet och Observera utdata att se vilka åtgärder som tillåts för vilka signaturer. Utdata i konsolfönstret ska se ut ungefär så här:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Nästa steg

* [Signaturer för delad åtkomst, del 1: Förstå SAS-modellen](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Hantera anonym läsbehörighet till behållare och blobbar](storage-manage-access-to-resources.md)
* [Delegera åtkomst med signatur för delad åtkomst (REST API)](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduktion till tabell och kö-SAS](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
