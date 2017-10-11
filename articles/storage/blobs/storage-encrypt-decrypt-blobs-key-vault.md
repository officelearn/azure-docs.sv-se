---
title: "Självstudier: Kryptera och dekryptera blobbar i Azure Storage med hjälp av Azure Key Vault | Microsoft Docs"
description: "Hur du krypterar och dekrypterar en blob med kryptering på klientsidan för Microsoft Azure Storage med Azure Key Vault."
services: storage
documentationcenter: 
author: adhurwit
manager: jasonsav
editor: tysonn
ms.assetid: 027e8631-c1bf-48c1-9d9b-f6843e88b583
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 01/23/2017
ms.author: adhurwit
ms.openlocfilehash: a2a3a4773d33fe6b8589ad8d9d219acda4d1015e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Självstudier: Kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault
## <a name="introduction"></a>Introduktion
Den här självstudiekursen beskrivs hur du gör användning av klientsidan lagringskryptering med Azure Key Vault. Den vägleder dig igenom hur du krypterar och dekrypterar en blobb i ett konsolprogram med hjälp av dessa tekniker.

**Uppskattad tidsåtgång:** 20 minuter

Mer information om Azure Key Vault finns [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Mer information om klientens kryptering för Azure Storage finns [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Krav
För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett Azure Storage-konto
* Visual Studio 2013 eller senare
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Översikt över kryptering på klientsidan
En översikt av klientsidan kryptering för Azure Storage finns [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Här är en kort beskrivning av hur kryptering på serversidan för klienten fungerar:

1. Azure Storage client SDK genererar en innehåll krypteringsnyckel (CEK), vilket är en gång Använd symmetrisk nyckel.
2. Kundinformation krypteras med den här CEK.
3. CEK kapslas sedan (krypterade) med hjälp av krypteringsnyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Klientens lagring har aldrig åtkomst till KEK. Den anropar bara viktiga radbrytning algoritmen som tillhandahålls av Key Vault. Kunder kan välja att använda anpassade providers för för radbrytning/uppackning om de vill.
4. Krypterade data överförs sedan till Azure Storage-tjänsten.

## <a name="set-up-your-azure-key-vault"></a>Konfigurera Azure Key Vault
För att fortsätta med den här kursen behöver du följande steg, som beskrivs i kursen [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md):

* Skapa en nyckelvalvet.
* Lägga till en nyckel eller hemlighet i nyckelvalvet.
* Registrera ett program med Azure Active Directory.
* Tillåta program att använda nyckel eller hemlighet.

Anteckna ClientID och ClientSecret som genererats när du registrerar ett program med Azure Active Directory.

Skapa båda nycklarna i nyckelvalvet. Vi förutsätter för resten av guiden att du har använt följande namn: ContosoKeyVault och TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Skapa ett konsolprogram med paket och AppSettings
Skapa ett nytt konsolprogram i Visual Studio.

Lägg till nödvändiga nuget-paket i Package Manager-konsolen.

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

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

Lägg till följande `using` -satser och se till att lägga till en referens till System.Configuration i projektet.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Lägg till en metod för att hämta en token i konsoltillämpning
Följande metod används av Key Vault-klasser som måste autentiseras för åtkomst till nyckelvalvet.

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

## <a name="access-storage-and-key-vault-in-your-program"></a>Komma åt lagringsutrymme och Key Vault i ditt program
Lägg till följande kod i funktionen Main.

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
> Key Vault Nätverksobjektmodellerna
> 
> Det är viktigt att förstå att det finns faktiskt två Key Vault nätverksobjektmodellerna vara medveten om: en baseras på REST-API (KeyVault namnområde) och den andra är ett tillägg för kryptering på klientsidan.
> 
> Key Vault klienten samverkar med REST-API och förstår JSON Web nycklar och hemligheter för de två typerna av saker som ingår i Nyckelvalvet.
> 
> Key Vault-tillägg är klasser som verkar vara skapats specifikt för klientsidan kryptering i Azure Storage. De innehåller ett gränssnitt för nycklar (IKey) och klasser som baseras på konceptet för en nyckel-matchare. Det finns två implementeringar av IKey som du behöver veta: RSAKey och SymmetricKey. Nu de råkar sammanfaller med vad som ingår i ett Nyckelvalv, men då de är oberoende klasser (så IKey inte implementerar en nyckel och Hemlig hämtas av Key Vault klienten).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Kryptera blob och ladda upp
Lägg till följande kod för att kryptera en blob och överföra den till Azure storage-konto. Den **ResolveKeyAsync** returnerar metoden som används för en IKey.

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

Följande är en skärmbild från den [klassiska Azure-portalen](https://manage.windowsazure.com) för en blob som har krypterats med hjälp av klientsidan kryptering med en nyckel som lagras i Nyckelvalvet. Den **KeyId** egenskapen är URI: N för nyckeln i Nyckelvalvet som fungerar som KEK. Den **EncryptedKey** -egenskapen innehåller den krypterade versionen av CEK.

![Skärmbild som visar Blob-metadata som innehåller krypteringsmetadata](./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png)

> [!NOTE]
> Om du tittar på konstruktorn BlobEncryptionPolicy visas att den kan acceptera en nyckel och/eller en DNS-matchare. Tänk som nu har du inte använda en DNS-matchare för kryptering eftersom den har för närvarande inte stöder en standard-nyckel.
> 
> 

## <a name="decrypt-blob-and-download"></a>Dekryptera blob och ladda ned
Dekryptering är verkligen när passar med DNS-matchare klasser. ID för den nyckel som används för kryptering är associerad med blob i dess metadata, så det finns ingen anledning att hämta nyckeln och spara associationen mellan nyckel och blob. Du behöver bara kontrollera att nyckeln finns kvar i Nyckelvalvet.   

Den privata nyckeln för en RSA-nyckel som finns kvar i Nyckelvalvet, så för dekryptering ska ske, den krypterade nyckeln från blobmetadata som innehåller CEK skickas till Key Vault för dekryptering.

Lägg till följande för att dekryptera blob som du just har överfört.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Det finns några andra typer av matchare att underlätta hantering av nycklar, inklusive: AggregateKeyResolver och CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Använd Key Vault hemligheter
Sätt att använda en hemlighet med kryptering på klientsidan är klassen SymmetricKey eftersom en hemlighet är i grunden en symmetrisk nyckel. Men som nämns ovan, en hemlighet i Nyckelvalvet mappar inte exakt med en SymmetricKey. Det finns några saker att förstå:

* Nyckeln i en SymmetricKey måste vara en fast längd: 128, 192, 256, 384 eller 512 bitar.
* Nyckeln i en SymmetricKey måste vara Base64-kodad.
* En hemlighet i Nyckelvalvet som ska användas som en SymmetricKey måste ha en Content-Type för ”program/oktett-ström” i Nyckelvalvet.

Här är ett exempel i PowerShell för att skapa en hemlighet i Nyckelvalvet som kan användas som en SymmetricKey.
Observera att värdet hårdkodade $key, för demonstration ändamål endast. I din kod vill du skapa den här nyckeln.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Du kan använda samma anropet som innan att hämta den här hemligheten som en SymmetricKey i din konsolprogram.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Det var allt. Ha det så kul!

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Microsoft Azure Storage med C# finns [Microsoft Azure Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Mer information om Blob REST-API finns [REST-API för Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Den senaste informationen om Microsoft Azure Storage, gå till den [Microsoft Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/).
