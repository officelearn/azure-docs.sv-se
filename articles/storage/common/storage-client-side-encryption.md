---
title: Kryptering av klientsidan med .NET för Microsoft Azure Storage | Microsoft Docs
description: Azure Storage-klientbiblioteket för .NET stöder kryptering på klientsidan och integrering med Azure Key Vault för maximal säkerhet för dina Azure Storage-program.
services: storage
documentationcenter: .net
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: becfccca-510a-479e-a798-2044becd9a64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cshoe
ms.openlocfilehash: 723ef31c0247d2b2b5e546b4e4fb3d91a516773c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
Den [Azure Storage-klientbibliotek för .NET-Nuget-paketet](https://www.nuget.org/packages/WindowsAzure.Storage) har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptera data under nedladdningen till klienten. Biblioteket stöder även integrering med [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) storage-konto för hantering av nycklar.

En stegvis självstudiekurs som leder dig genom processen för att kryptera blobar med hjälp av kryptering på klientsidan och Azure Key Vault finns [kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Kryptering på klientsidan med Java, se [klientsidan kryptering med Java för Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert-teknik
Processerna för kryptering och dekryptering Följ kuvert-teknik.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert-teknik
Kryptering via kuvert tekniken fungerar på följande sätt:

1. Azure storage-klientbiblioteket genererar en innehåll krypteringsnyckel (CEK), vilket är en gång Använd symmetrisk nyckel.
2. Användardata krypteras med den här CEK.
3. CEK kapslas sedan (krypterade) med hjälp av krypteringsnyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key valv.
   
    Storage-klientbiblioteket själva har aldrig åtkomst till KEK. Biblioteket anropar algoritmen viktiga radbrytning som tillhandahålls av Key Vault. Användare kan välja att använda anpassade providers för viktiga radbrytning/uppackning om så önskas.

4. Krypterade data överförs sedan till Azure Storage-tjänsten. Nyckeln omsluten tillsammans med vissa ytterligare krypteringsmetadata lagras som metadata (på en blob) eller interpolerade med krypterade data (Kömeddelanden och tabellentiteter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert-teknik
Dekryptering via kuvert tekniken fungerar på följande sätt:

1. Klientbiblioteket förutsätter att användaren kan hantera nyckelkryptering nyckel (KEK) lokalt eller i Azure Key valv. Användaren behöver inte veta nyckeln som används för kryptering. I stället kan en nyckel matchare som matchar olika nyckelidentifierare nycklar ställa in och användas.
2. Klientbiblioteket hämtar krypterade data tillsammans med kryptering material som är lagrad på tjänsten.
3. Omslutna innehåll krypteringsnyckeln (CEK) är okrypterad (dekrypterade) med hjälp av krypteringsnyckel nyckel (KEK). Här igen, har klientbiblioteket inte åtkomst till KEK. Den anropar bara ett anpassat eller borttagning av algoritmen för Key Vault-providern.
4. Innehåll krypteringsnyckeln (CEK) används sedan för att dekryptera den krypterade informationen.

## <a name="encryption-mechanism"></a>Krypteringsalgoritm
Storage-klientbiblioteket använder [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Mer specifikt [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Varje tjänst fungerar olika, så vi upp dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande kryptering av hela blobbar. Mer specifikt kryptering stöds när användare använder den **UploadFrom*** metoder eller **OpenWrite** metod. För hämtade filer, både fullständig och intervallet hämtningsbara filer stöds.

Under krypteringen klientbiblioteket Generera en slumpmässig initieringen Vector (IV) av 16 byte, tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) 32 byte och utföra kuvert kryptering av blob-data med den här informationen. Omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som blob metadata tillsammans med den kryptera blobben-tjänsten.

> [!WARNING]
> Om du redigerar eller överföra metadata för blobben, måste du se till att dessa metadata bevaras. Om du överför nya metadata utan dessa metadata, omslutna CEK, IV och andra metadata går förlorade och blob-innehåll aldrig kommer att hämtas igen.
> 
> 

Hämtar en krypterad blob omfattar hämtar innehållet i hela blob med hjälp av den **DownloadTo *** /**BlobReadStream ** bekvämlighet metoder. Omslutna CEK oförpackade och används tillsammans med IV (som lagras som blobbmetadata i det här fallet) för att returnera dekrypterade data till användarna.

Hämta ett godtyckligt intervall (**DownloadRange*** metoder) innebär att området som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas för att dekryptera har det begärda intervallet i krypterade blob.

Alla blob-typer (blockblobbar, sidblobbar och tilläggsblobbar) kan krypterade/dekrypteras med hjälp av det här schemat.

### <a name="queues"></a>Köer
Eftersom Kömeddelanden kan vara i valfritt format, definierar klientbiblioteket ett anpassat format som innehåller initieringen Vector (IV) och den krypterade innehållet krypteringsnyckeln (CEK) i meddelandetexten.

Under krypteringen klientbiblioteket genererar en slumpmässig IV av 16 byte tillsammans med en slumpmässig CEK 32 byte och utför kuvert kryptering av meddelandetexten kön med den här informationen. Omslutna CEK och vissa ytterligare krypteringsmetadata läggs sedan till krypterade kömeddelandet. Ändrade meddelandet (se nedan) lagras på tjänsten.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Under dekrypteringen, omslutna nyckeln extraheras från kön meddelandet och oförpackade. IV är också extraheras från kön meddelandet och används tillsammans med nyckeln packats upp för att dekryptera meddelandedata kön. Observera att krypteringsmetadata är liten (under 500 byte), så när den räknas mot 64KB-gränsen för ett kömeddelande effekten ska hanteras.

### <a name="tables"></a>Tabeller
Klientbiblioteket har stöd för kryptering av Entitetsegenskaper för insert och ersätt-åtgärder.

> [!NOTE]
> Koppling stöds inte för närvarande. Eftersom en delmängd av egenskaper kan har krypterats tidigare med hjälp av en annan nyckel leder bara sammanslagning nya egenskaper och uppdaterar metadata till dataförlust. Sammanslagning av antingen kräver extra service-anrop för att läsa den befintliga enheten från tjänsten eller med en ny nyckel för varje egenskap som lämpar sig inte av prestandaskäl.
> 
> 

Tabell datakryptering fungerar på följande sätt:  

1. Användare ange egenskaper som ska krypteras.
2. Klientbiblioteket genererar en slumpmässig initieringen Vector (IV) av 16 byte tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) 32 byte för varje entitet och utför kuvert kryptering på de enskilda egenskaperna som ska krypteras av härleds en ny IV per egenskap. Egenskapen krypterade lagras som binära data.
3. Omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som ytterligare två reserverade egenskaper. Den första reserverade egenskapen (_ClientEncryptionMetadata1) är en strängegenskap som innehåller information om IV, version och omslutna nyckel. Egenskapen andra reserverade (_ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen (_ClientEncryptionMetadata2) är krypterad.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering, har användare nu endast 250 anpassade egenskaper i stället för 252. Den totala storleken på enheten måste vara mindre än 1 MB.

Observera att endast egenskaperna för anslutningssträngen kan vara krypterad. Om andra typer av egenskaper som ska krypteras, måste de konverteras till strängar. Krypterade strängar lagras på tjänsten som binära egenskaper och de konverteras till strängar efter dekrypteringen.

För tabeller, förutom krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras genom att antingen att ange attributet [EncryptProperty] (för POCO entiteter som är härledda från TableEntity) eller en kryptering matcharen begäran alternativ. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logik runt hur egenskaperna är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Observera att det inte nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

### <a name="batch-operations"></a>Batchåtgärder
I batch-åtgärder, kommer samma KEK att används över alla rader i den batchåtgärd eftersom klientbiblioteket kan bara ett alternativ objekt (och därför en princip/KEK) per batchåtgärd. Men genererar klientbiblioteket internt en ny slumpmässiga IV och slumpmässiga CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i gruppen genom att definiera problemet i matcharen kryptering.

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom enheterna är krypterade, kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du försöker, blir resultatet felaktigt, eftersom skulle tjänsten försöker jämföra krypterade data med okrypterade data.
> 
> 
Om du vill utföra frågeåtgärder, måste du ange en nyckel matchare som kan matcha alla nycklar i resultatmängden. Om en entitet i frågeresultatet inte kan matchas till en provider, genereras klientbiblioteket ett fel. För en fråga som utför serversidan projektioner läggs klientbiblioteket metadataegenskaper särskilda kryptering (_ClientEncryptionMetadata1 och _ClientEncryptionMetadata2) som standard till de markerade kolumnerna.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med hjälp av Azure Key Vault kan kryptera användare nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). Mer information finns i [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Storage-klientbiblioteket använder Key Vault-Kärnbibliotek för att tillhandahålla ett gemensamt ramverk i Azure för hantering av nycklar. Användare kan också få ytterligare fördelen med att använda tillägg Key Vault-bibliotek. Tillägg biblioteket innehåller användbara funktioner runt enkel och sömlös Symmetric/RSA lokala och viktiga molntjänstleverantörer samt med aggregering och cachelagring.

### <a name="interface-and-dependencies"></a>Gränssnittet och beroenden
Det finns tre Key Vault-paket:

* Microsoft.Azure.KeyVault.Core innehåller IKey och IKeyResolver. Det är ett litet paket utan beroenden. Storage-klientbiblioteket för .NET definierar det som ett beroende.
* Microsoft.Azure.KeyVault innehåller Key Vault REST-klient.
* Microsoft.Azure.KeyVault.Extensions innehåller tilläggskod som innehåller implementeringar av kryptografiska algoritmer och en RSAKey och en SymmetricKey. Det beror på kärnor och KeyVault-namnområden och ger funktioner för att definiera en sammanställd matcharen (när du vill använda flera viktiga providers) och en cachelagring viktiga matchning. Även om storage-klientbiblioteket inte direkt beror på det här paketet om användarna vill använda Azure Key Vault för att lagra sina nycklar eller att använda Key Vault-tillägg att använda lokalt och molnet kryptografiproviders, måste de det här paketet.

Key Vault är utformat för högt värde huvudnycklar och bandbreddsbegränsning gränser för Key Vault är utformad med detta i åtanke. När du utför kryptering på klientsidan med Key Vault är att föredra att använda master symmetriska nycklar lagras som hemligheter i Nyckelvalvet och cachelagras lokalt. Användarna måste göra följande:

1. Skapa en hemlighet offline och överföra den till Nyckelvalvet.
2. Använda hemlighetens grundläggande identifierare som en parameter för att matcha den aktuella versionen av hemlighet för kryptering och cachelagra informationen lokalt. Använd CachingKeyResolver för cachelagring; användare som inte förväntas implementera sina egna cachelagring logik.
3. Använd cachelagring matcharen indata medan kryptering.

Mer information om användning av Key Vault finns i den [kryptering kodexempel](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Bästa praxis
Stöd för kryptering är endast tillgänglig i storage-klientbiblioteket för .NET. Windows Phone och Windows Runtime stöder inte kryptering för närvarande.

> [!IMPORTANT]
> Tänk på dessa viktiga punkter när du använder kryptering på klientsidan:
> 
> * När läsning eller skrivning till en krypterad blob Använd hela blob överför kommandon och intervall/hela blob download kommandon. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som till exempel placera Block, placera Blockeringslista, skriva sidor, rensa sidor eller lägga till Block. Annars kan du skadad krypterade blob och att den inte kan läsas.
> * För tabeller finns en liknande begränsning. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.
> * Om du anger metadata för den kryptera blobben kan du skriva över den krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen metadata inte är additiva. Detta gäller även för ögonblicksbilder; Undvik att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om du måste ange metadata, måste du anropa den **FetchAttributes** metoden först för att hämta krypteringsmetadata för den aktuella och undvika samtidiga skrivningar metadata anges.
> * Aktivera den **RequireEncryption** egenskap i begäran standardalternativen för användare som ska fungerar bara med krypterade data. Mer information finns nedan.
> 
> 

## <a name="client-api--interface"></a>Programmeringsgränssnitt mot klienten / gränssnitt
När du skapar en EncryptionPolicy objekt, kan användare ange endast en nyckel (implementera IKey), bara en matchare (implementera IKeyResolver) eller båda. IKey är den grundläggande nyckeln som identifieras med en nyckelidentifierare och som innehåller logiken för radbrytning/uppackning. IKeyResolver används för att lösa en nyckel under dekrypteringsprocessen. Den definierar en ResolveKey-metod som returnerar ett IKey ges en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

* Nyckeln används alltid för kryptering, och avsaknaden av en nyckel resulterar i ett fel.
* För dekryptering:
  * Viktiga matcharen anropas om att hämta nyckeln har angetts. Om matcharen har angetts men inte har en mappning för nyckel-ID, returneras ett fel.
  * Om DNS-matchare inte har angetts, men en nyckel har angetts, används nyckeln om dess identifierare som matchar den nödvändiga Nyckelidentifieraren. Om ID inte matchar genereras ett fel.

Kodexemplen i den här artikeln visar hur en krypteringsprincip för och arbetar med krypterade data, men Visa inte arbeta med Azure Key Vault. Den [kryptering prover](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) på GitHub demonstrera en mer detaljerad slutpunkt till slutpunkt för BLOB, köer och tabeller, tillsammans med Key Vault-integrering.

### <a name="requireencryption-mode"></a>RequireEncryption läge
Användare kan välja att ett läge där alla överföringar och hämtningsbara filer måste vara krypterad. I det här läget misslyckas försök att överföra data utan en krypteringsprincip för eller hämta data som inte är krypterad för tjänsten på klienten. Den **RequireEncryption** alternativ Begäranobjektet bestämmer det här beteendet. Om ditt program kommer att kryptera alla objekt som lagras i Azure Storage, så du kan ange den **RequireEncryption** egenskapen på standardalternativen för begäran för klienten serviceobjektet. Till exempel **CloudBlobClient.DefaultRequestOptions.RequireEncryption** till **SANT** att kräva kryptering för alla blob-åtgärder som utförs genom att klientobjekt.


### <a name="blob-service-encryption"></a>Kryptering för BLOB-tjänst
Skapa en **BlobEncryptionPolicy** objekt och ange det i alternativen för begäran (per API eller på en klientnivå med hjälp av **DefaultRequestOptions**). Allt annat hanteras av klientbiblioteket internt.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Kön service-kryptering
Skapa en **QueueEncryptionPolicy** objekt och ange det i alternativen för begäran (per API eller på en klientnivå med hjälp av **DefaultRequestOptions**). Allt annat hanteras av klientbiblioteket internt.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Tabellen service-kryptering
Förutom att skapa en krypteringsprincip för och ange värdet på begäran alternativ, måste du antingen ange en **EncryptionResolver** i **TableRequestOptions**, eller ange attributet [EncryptProperty] för entiteten.

#### <a name="using-the-resolver"></a>Med hjälp av matcharen

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan, om entiteten implementerar TableEntity sedan egenskaperna kan vara dekorerad med attributet [EncryptProperty] istället för att ange den **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Kryptering och prestanda
Observera att kryptera din lagring data resulterar i ytterligare prestanda försämras. Innehållsnyckeln och IV måste skapas själva innehållet måste krypteras och ytterligare metadata måste vara formaterad och har överförts. Det här arbetet varierar beroende på mängden data som krypteras. Vi rekommenderar att kunder alltid testa sina program för prestanda under utveckling.

## <a name="next-steps"></a>Nästa steg
* [Självstudier: Kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Hämta den [Azure Storage-klientbibliotek för .NET-NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.Storage)
* Hämta Azure Key Vault NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [klienten](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/), och [tillägg](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) paket  
* Besök den [Azure Key Vault-dokumentation](../../key-vault/key-vault-whatis.md)
