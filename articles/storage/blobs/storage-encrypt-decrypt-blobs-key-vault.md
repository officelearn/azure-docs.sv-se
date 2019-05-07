---
title: 'Självstudier: Kryptera och dekryptera blobbar i Azure Storage med Azure Key Vault | Microsoft Docs'
description: Så här att kryptera och dekryptera en blob med client side encryption för Microsoft Azure Storage med Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 758eeedb89b3cef6766cf195a2cada50fbe63042
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148423"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Självstudier: Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault

## <a name="introduction"></a>Introduktion
Den här självstudien beskriver hur du får använda storage client side encryption med Azure Key Vault. Vi går igenom hur du krypterar och dekrypterar en blob i ett konsolprogram med hjälp av dessa tekniker.

**Uppskattad tidsåtgång:** 20 minuter

Mer information om Azure Key Vault finns i [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Mer information om client side encryption för Azure Storage finns i [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett Azure Storage-konto
* Visual Studio 2013 eller senare
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Översikt över client side encryption

En översikt över client side encryption för Azure Storage finns i [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Här är en kort beskrivning av hur kryptering fungerar:

1. SDK för Azure Storage-klient genererar en content krypteringsnyckel (CEK), vilket är en symmetrisk nyckel för en gång användning.
2. Kunddata är krypterat med den här CEK.
3. CEK kapslas sedan (krypterad) med klartextnyckel-nyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller i Azure Key Vault. Storage-klienten sig själv har aldrig åtkomst till KEK. Den anropar bara viktiga radbrytning algoritmen som tillhandahålls av Key Vault. Kunderna kan välja att använda anpassade providers för nyckeln radbrytning/uppackning om de vill.
4. Krypterade data överförs sedan till Azure Storage-tjänsten.

## <a name="set-up-your-azure-key-vault"></a>Konfigurera Azure Key Vault

För att fortsätta med den här självstudien behöver du följande steg, som beskrivs i självstudiekursen [vad är Azure Key Vault?](../../key-vault/key-vault-overview.md):

* Skapa ett nyckelvalv.
* Lägg till en nyckel eller hemlighet till nyckelvalvet.
* Registrera ett program med Azure Active Directory.
* Godkänna att programmet använder nyckeln eller hemligheten.

Anteckna ClientID och ClientSecret som genererades när du registrerar ett program med Azure Active Directory.

Skapa båda nycklarna i nyckelvalvet. Vi antar för resten av självstudierna att du har använt följande namn: ContosoKeyVault och TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Skapa ett konsolprogram med paket och AppSettings

Skapa ett nytt konsolprogram i Visual Studio.

Lägg till nödvändiga nuget-paket i Package Manager-konsolen.

```powershell
Install-Package WindowsAzure.Storage
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Lägg till AppSettings i App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Lägg till följande `using` direktiv och se till att lägga till en referens till System.Configuration i projektet.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Lägg till en metod för att få en token för ditt konsolprogram

Följande metod som används av Key Vault-klasser som måste autentiseras för åtkomst till ditt nyckelvalv.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>Åtkomst till lagring och Key Vault i ditt program

Lägg till följande kod i Main-funktion.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Key Vault objektmodeller
> 
> Det är viktigt att förstå att det finns faktiskt två Key Vault objektmodeller känna till: en baseras på REST-API (KeyVault namnområde) och den andra är ett tillägg för client side encryption.
> 
> Key Vault-klienten interagerar med REST API och förstår JSON Web nycklar och hemligheter för de två typerna av saker som finns i Key Vault.
> 
> Key Vault tillägg är klasser som verkar skapats enbart för client side encryption i Azure Storage. De innehåller ett gränssnitt för nycklar (IKey) och klasser som baseras på konceptet med en nyckel-matchare. Det finns två implementeringar av IKey som du behöver känna till: RSAKey och SymmetricKey. Nu de råkar sammanträffar med de saker som finns i ett Nyckelvalv, men då de är oberoende klasser (så IKey inte implementerar en nyckel och hemlighet som hämtas av Key Vault-klienten).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Kryptera blob och ladda upp

Lägg till följande kod för att kryptera en blob och överföra den till Azure storage-kontot. Den **ResolveKeyAsync** returnerar metoden som används för en IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Om du tittar på konstruktorn BlobEncryptionPolicy ser du att den kan acceptera en nyckel och/eller en DNS-matchare. Tänk som nu har du inte använda en DNS-matchare för kryptering eftersom den har för närvarande inte stöder en standardnyckel.
> 
> 

## <a name="decrypt-blob-and-download"></a>Dekryptera blob och ladda ned

Dekryptering är verkligen när med hjälp av klasserna matchare meningsfullt. ID för den nyckel som används för kryptering är associerad med bloben i dess metadata, så det finns ingen anledning att hämta nyckeln och Kom ihåg att associationen mellan nyckel- och blob. Du behöver bara se till att nyckeln finns kvar i Key Vault.   

Den privata nyckeln för en RSA-nyckel som finns kvar i Key Vault, så för dekryptering ska ske den krypterade nyckeln från blobmetadata som innehåller CEK skickas till Key Vault för dekryptering.

Lägg till följande för att dekryptera den blob som du just har överfört.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Det finns några andra typer av matchare att underlätta nyckelhantering, inklusive: AggregateKeyResolver och CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Använd Key Vault-hemligheter

Sätt att använda en hemlighet med client side encryption är klassen SymmetricKey eftersom en hemlighet är i grunden en symmetrisk nyckel. Men som anges ovan är en hemlighet i Key Vault mappar inte exakt med en SymmetricKey. Det finns några saker att förstå:

* Nyckeln i en SymmetricKey måste vara en fast längd: 128, 192, 256, 384 eller 512 bitar.
* Nyckeln i en SymmetricKey måste vara Base64-kodad.
* En Key Vault-hemlighet som ska användas som en SymmetricKey måste ha en innehållstyp ”application/octet-ström” i Key Vault.

Här är ett exempel i PowerShell för att skapa en hemlighet i Key Vault som kan användas som en SymmetricKey.
Observera att värdet hårdkodad $key, är endast för demonstration. I din egen kod kommer du att skapa den här nyckeln.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Du kan använda samma anropet som innan i konsolprogrammet, för att hämta den här hemligheten som en SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Klart! Ha det så kul!

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder Microsoft Azure Storage med C#, [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Mer information om Blob REST API finns i [REST-API för Blob Service](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Den senaste informationen om Microsoft Azure Storage går du till den [Microsoft Azure Storage-teamets blogg](https://blogs.msdn.com/b/windowsazurestorage/).