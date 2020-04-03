---
title: Självstudiekurs - Kryptera och dekryptera blobbar med Azure Key Vault
titleSuffix: Azure Storage
description: Lär dig hur du krypterar och dekrypterar en blob med kryptering på klientsidan med Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 3f4f1f5e163dfed9f356aed538d934d0e4258790
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618885"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Självstudiekurs - Kryptera och dekryptera blobbar med Azure Key Vault

Den här självstudien beskriver hur du använder lagringskryptering på klientsidan med Azure Key Vault. Den går igenom hur du krypterar och dekrypterar en blob i ett konsolprogram med hjälp av dessa tekniker.

**Uppskattad tidsåtgång:** 20 minuter

Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md).

Mer information om kryptering på klientsidan för Azure Storage finns i [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett Azure Storage-konto
* Visual Studio 2013 eller senare
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Översikt över kryptering på klientsidan

En översikt över kryptering på klientsidan för Azure Storage finns i [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Här är en kort beskrivning av hur kryptering på klientsidan fungerar:

1. Azure Storage-klienten SDK genererar en innehållskrypteringsnyckel (CEK), som är en symmetrisk nyckel för engångsbruk.
2. Kunddata krypteras med denna CEK.
3. CEK slås sedan in (krypterad) med hjälp av nyckelkrypteringsnyckeln (KEK). KEK identifieras av en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Lagringsklienten själv har aldrig tillgång till KEK. Det åberopar bara nyckeln omslagsalgoritm som tillhandahålls av Key Vault. Kunder kan välja att använda anpassade leverantörer för nyckel omslag / uppackning om de vill.
4. De krypterade data överförs sedan till Azure Storage-tjänsten.

## <a name="set-up-your-azure-key-vault"></a>Konfigurera ditt Azure Key Vault

För att kunna fortsätta med den här självstudien måste du göra följande steg, som beskrivs i självstudien [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av en .NET-webbapp:](../../key-vault/quick-create-net.md)

* Skapa ett nyckelvalv.
* Lägg till en nyckel eller hemlighet i nyckelvalvet.
* Registrera ett program med Azure Active Directory.
* Auktorisera programmet att använda nyckeln eller hemligheten.

Anteckna clientID och ClientSecret som genererades när du registrerade ett program med Azure Active Directory.

Skapa båda nycklarna i nyckelvalvet. Vi förutsätter för resten av självstudien att du har använt följande namn: ContosoKeyVault och TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Skapa ett konsolprogram med paket och AppSettings

Skapa ett nytt konsolprogram i Visual Studio.

Lägg till nödvändiga nugetpaket i Package Manager-konsolen.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
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

Lägg till `using` följande direktiv och se till att lägga till en referens till System.Configuration i projektet.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Lägga till en metod för att få en token till konsolprogrammet

Följande metod används av Key Vault-klasser som behöver autentisera för åtkomst till nyckelvalvet.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Få tillgång till Azure Storage och Key Vault i ditt program

Lägg till följande kod i metoden Main().

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Objektmodeller för Key Vault
> 
> Det är viktigt att förstå att det faktiskt finns två Key Vault-objektmodeller att vara medveten om: en baseras på REST API (KeyVault-namnområde) och det andra är ett tillägg för kryptering på klientsidan.
> 
> Key Vault-klienten interagerar med REST API och förstår JSON Web Keys och hemligheter för de två typer av saker som finns i Key Vault.
> 
> Key Vault-tilläggen är klasser som verkar särskilt skapade för kryptering på klientsidan i Azure Storage. De innehåller ett gränssnitt för nycklar (IKey) och klasser baserat på begreppet en nyckellösare. Det finns två implementeringar av IKey som du behöver veta: RSAKey och SymmetricKey. Nu råkar de sammanfalla med de saker som finns i ett Nyckelvalv, men på denna punkt de är oberoende klasser (så nyckeln och hemligheten hämtas av Key Vault Client inte genomföra IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Kryptera blob och ladda upp

Lägg till följande kod för att kryptera en blob och ladda upp den till ditt Azure-lagringskonto. Metoden **ResolveKeyAsync** som används returnerar en IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Om du tittar på BlobEncryptionPolicy-konstruktorn ser du att den kan acceptera en nyckel och/eller en resolver. Tänk på att du just nu inte kan använda en resolver för kryptering eftersom den för närvarande inte stöder en standardnyckel.

## <a name="decrypt-blob-and-download"></a>Dekryptera blob och ladda ner

Dekryptering är verkligen när du använder Resolver klasser vettigt. ID:et för nyckeln som används för kryptering är associerat med blobben i dess metadata, så det finns ingen anledning för dig att hämta nyckeln och komma ihåg kopplingen mellan nyckel och blob. Du måste bara se till att nyckeln finns kvar i Key Vault.   

Den privata nyckeln till en RSA-nyckel finns kvar i Key Vault, så för att dekryptering ska inträffa skickas den krypterade nyckeln från blobmetadata som innehåller CEK till Key Vault för dekryptering.

Lägg till följande för att dekryptera bloben som du just har laddat upp.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Det finns ett par andra typer av resolvers för att göra nyckelhantering enklare, inklusive: AggregateKeyResolver och CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Använd Hemligheter för Key Vault

Sättet att använda en hemlighet med kryptering på klientsidan är via klassen SymmetricKey eftersom en hemlighet i huvudsak är en symmetrisk nyckel. Men, som nämnts ovan, en hemlighet i Key Vault inte karta exakt till en SymmetricKey. Det finns några saker att förstå:

* Nyckeln i en SymmetricKey måste vara en fast längd: 128, 192, 256, 384 eller 512 bitar.
* Nyckeln i en SymmetricKey ska vara Base64-kodad.
* En Key Vault hemlighet som kommer att användas som en SymmetricKey måste ha en innehållstyp av "application/octet-stream" i Key Vault.

Här är ett exempel i PowerShell för att skapa en hemlighet i Key Vault som kan användas som en SymmetricKey.
Observera att det hårdkodade värdet, $key, endast är för demonstrationsändamål. I din egen kod vill du generera den här nyckeln.

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

I konsolprogrammet kan du använda samma anrop som tidigare för att hämta den här hemligheten som en SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Klart! Ha det så kul!

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Microsoft Azure Storage med C#finns i [Microsoft Azure Storage Client Library för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Mer information om Blob REST API finns i [Blob Service REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Den senaste informationen om Microsoft Azure Storage finns i [Microsoft Azure Storage Team Blog](https://blogs.msdn.com/b/windowsazurestorage/).
