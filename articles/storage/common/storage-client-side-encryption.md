---
title: Kryptering på klientsidan med .NET för Microsoft Azure Storage | Microsoft Docs
description: Azure Storage-klientbiblioteket för .NET stöder client side encryption och integrering med Azure Key Vault för maximal säkerhet för dina Azure Storage-program.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c8f9b17bf5b572128348b22de62566ba06d5d766
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992412"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Client Side Encryption och Azure Key Vault för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
Den [Azure Storage-klientbiblioteket för .NET Nuget-paket](https://www.nuget.org/packages/WindowsAzure.Storage) har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptering av data under nedladdningen till klienten. Biblioteket stöder även integrering med [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar i storage-konto.

En stegvis självstudiekurs som leder dig genom processen för att kryptera blobbar med client side encryption och Azure Key Vault finns i [kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Client side encryption med Java finns [Client Side Encryption med Java för Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert-teknik
Processer för kryptering och dekryptering Följ kuvert-teknik.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert-teknik
Kryptering via kuvert-tekniken fungerar på följande sätt:

1. Azure storage-klientbiblioteket genererar en content krypteringsnyckel (CEK), vilket är en symmetrisk nyckel för en gång användning.
2. Användardata har krypterats med den här CEK.
3. CEK kapslas sedan (krypterad) med klartextnyckel-nyckel (KEK). KEK kan identifieras med en nyckelidentifierare och vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vaults.
   
    Storage-klientbiblioteket sig själv har aldrig åtkomst till KEK. Biblioteket anropar viktiga radbrytning algoritmen som tillhandahålls av Key Vault. Användare kan välja att använda anpassade providers för viktiga radbrytning/uppackning om så önskas.

4. Krypterade data överförs sedan till Azure Storage-tjänsten. Den omslutna nyckeln tillsammans med vissa ytterligare krypteringsmetadata lagras som metadata (för en blob) eller interpolerade med krypterade data (Kömeddelanden och tabellenheter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert-teknik
Dekryptering via kuvert-tekniken fungerar på följande sätt:

1. Klientbiblioteket förutsätter att du kan hantera nyckelkrypteringsnyckel (KEK) lokalt eller i Azure Key Vaults. Användaren behöver inte veta nyckeln som används för kryptering. I stället kan en nyckel matchare som motsvarar nycklar olika nyckelidentifierare ställas in och används.
2. Klientbiblioteket laddar ned den krypterade informationen tillsammans med eventuella kryptering material som är lagrad på tjänsten.
3. Den omslutna innehåll krypteringsnyckeln (CEK) är packats upp (dekrypterade) med hjälp av krypteringsnyckel nyckel (KEK). Här igen, har klientbiblioteket inte åtkomst till KEK. Den anropar bara anpassat eller borttagning av algoritmen för Key Vault-providern.
4. Innehåll krypteringsnyckeln (CEK) används sedan för att dekryptera krypterade informationen.

## <a name="encryption-mechanism"></a>Krypteringsalgoritm
Storage-klientbiblioteket använder [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Mer specifikt [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Var och en service fungerar på ett något annorlunda så vi upp vart och ett av dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande kryptering av hela blobar. Mer specifikt kryptering stöds när användarna använder den **UploadFrom** metoder eller **OpenWrite** metod. För nedladdningar, både fullständig och intervallet nedladdningar stöds.

Under krypteringen ska klientbiblioteket generera ett slumpmässigt initieringen vektor (IV) med 16 byte, tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) på 32 byte och utföra kuvert kryptering av blob-data med den här informationen. Den omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som blob-metadata tillsammans med krypterade blobben på tjänsten.

> [!WARNING]
> Om du redigerar eller ladda upp en egen metadata för blobben, måste du se till att dessa metadata bevaras. Om du laddar upp ny metadata utan dessa metadata, omslutna CEK, IV och andra metadata går förlorade och blobinnehållet inte kommer att hämtningsbar igen.
> 
> 

Ladda ned en krypterad blob innebär att du hämtar innehållet i hela bloben med den **DownloadTo**/**BlobReadStream** bekväma metoder. Den omslutna CEK oinslagna och används tillsammans med IV (som lagras som blob-metadata i det här fallet) för att returnera dekrypterade data till användarna.

Ladda ned ett godtyckligt adressintervall (**DownloadRange** metoder) innebär att området som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas för att kunna dekryptera den begärda i krypterad blob intervallet.

Alla blob-typer (blockblobbar, sidblobbar och tilläggsblobbar) kan krypterade/dekrypteras med hjälp av det här schemat.

### <a name="queues"></a>Köer
Eftersom Kömeddelanden kan vara i valfritt format, definierar klientbiblioteket ett anpassat format som innehåller initieringen vektor (IV) och den krypterade innehåll krypteringsnyckeln (CEK) i meddelandetexten.

Under krypteringen klientbiblioteket genererar en slumpmässig IV med 16 byte tillsammans med en slumpmässig CEK på 32 byte och utför kuvert kryptering av meddelandetexten kön med den här informationen. Den omslutna CEK och vissa ytterligare krypteringsmetadata läggs sedan till krypterade kömeddelandet. Det här ändrade meddelandet (se nedan) lagras på tjänsten.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Under dekrypteringen, är nyckeln omslutna extraheras från kömeddelandet och oinslagna. IV är också extraheras från kömeddelandet och används tillsammans med nyckeln packats upp för att dekryptera meddelandedata kö. Observera att krypteringsmetadata är liten (under 500 byte), så även om det räknas mot 64KB-gränsen för ett kömeddelande effekten ska hanteras.

### <a name="tables"></a>Tabeller
Klientbiblioteket stöder kryptering av Entitetsegenskaper för insert och ersätt-åtgärder.

> [!NOTE]
> Koppling stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan har krypterats tidigare med hjälp av en annan nyckel leder bara sammanslagning av de nya egenskaperna och uppdatera metadata som till förlust av data. Sammanslagning av antingen kräver extra service anrop till läsa befintliga entiteten från tjänsten, eller med en ny nyckel per egenskap som lämpar sig inte av prestandaskäl.
> 
> 

Tabellen kryptering fungerar på följande sätt:  

1. Användare ange egenskaper som ska krypteras.
2. Klientbiblioteket genererar en slumpmässig initieringen vektor (IV) med 16 byte tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) på 32 byte för varje entitet och utför kuvert kryptering på de enskilda egenskaperna som ska krypteras av som härleds en ny IV per egenskap. Egenskapen krypterade lagras som binära data.
3. Den omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som två ytterligare reserverade egenskaper. Den första reserverade egenskapen (_ClientEncryptionMetadata1) är en strängegenskap som innehåller information om IV, version och omslutna nyckel. Egenskapen andra reserverade (_ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen (_ClientEncryptionMetadata2) är krypterad.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering, kan användare nu har endast 250 anpassade egenskaper i stället för 252. Den totala storleken på entiteten måste vara mindre än 1 MB.

Observera att endast egenskaperna för anslutningssträngen kan krypteras. Om det finns andra typer av egenskaper som ska krypteras, måste de konverteras till strängar. Krypterade strängar som är lagrade på tjänsten som binära egenskaper och de konverteras till strängar efter dekryptering.

För tabeller, utöver krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras antingen att ange attributet [EncryptProperty] (för POCO entiteter som härleds från TableEntity) eller en kryptering matcharen i alternativen för certifikatförfrågan. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel och radnyckel egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska vara krypterad. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till ledningen. Delegaten ger också möjlighet att logic kring hur egenskaper som är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Observera att det inte är nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

### <a name="batch-operations"></a>Batchåtgärder
I batchåtgärder används samma KEK på alla rader i den batchåtgärd eftersom klientbiblioteket tillåter endast ett alternativ-objekt (och därför en princip/KEK) per batch-åtgärden. Men genererar klientbiblioteket internt ett nytt slumpmässigt IV och slumpmässiga CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i Konfliktlösare för kryptering.

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna som är krypterade, kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du försöker att resultaten vara felaktig, eftersom tjänsten skulle ha försökt att jämföra krypterade data med okrypterade data.
> 
> 
> Om du vill utföra frågeåtgärder, måste du ange en nyckel matchare som kan matcha alla nycklar i resultatuppsättningen. Om en entitet i frågeresultatet inte kan matchas till en leverantör, genereras klientbiblioteket ett fel. För en fråga som utför serversidan projektioner läggs klientbiblioteket metadataegenskaper särskilda kryptering (_ClientEncryptionMetadata1 och _ClientEncryptionMetadata2) som standard till de markerade kolumnerna.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Genom att använda Azure Key Vault kan kryptera användare nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). Mer information finns i [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Storage-klientbiblioteket använder Key Vault-Kärnbibliotek för att tillhandahålla ett gemensamt ramverk i Azure för att hantera nycklar. Användare kan också hämta den ytterligare fördelen med att använda Key Vault-tillägg-biblioteket. Tillägg-biblioteket innehåller användbara funktioner runt enkel och smidig Symmetric/RSA lokala och viktiga molnleverantörer samt med aggregering och cachelagring.

### <a name="interface-and-dependencies"></a>Gränssnittet och beroenden
Det finns tre Key Vault-paket:

* Microsoft.Azure.KeyVault.Core innehåller IKey och IKeyResolver. Det är ett litet paket utan beroenden. Storage-klientbiblioteket för .NET definierar det som ett beroende.
* Microsoft.Azure.KeyVault innehåller Key Vault REST-klient.
* Microsoft.Azure.KeyVault.Extensions innehåller tilläggskod som innehåller implementeringar av kryptografiska algoritmer och en RSAKey och en SymmetricKey. Det beror på Core och KeyVault-namnområden och tillhandahåller funktioner för att definiera en sammanställd matchare (när du vill använda flera viktiga providers) och en cachelagring viktiga matchare. Även om storage-klientbiblioteket inte direkt beroende av det här paketet om användarna vill använda Azure Key Vault för att lagra sina nycklar eller att använda Key Vault-tillägg för att använda lokalt och molnbaserade kryptografiproviders, behöver de det här paketet.

Key Vault är utformat för högt värde huvudnycklar och begränsningar gränser för Key Vault är utformade med detta i åtanke. När du utför kryptering på klientsidan med Key Vault, är att föredra att använda master symmetriska nycklar lagras som hemligheter i Key Vault och cachelagras lokalt. Användare måste göra följande:

1. Skapa en hemlighet offline och överför den till Key Vault.
2. Använda hemlighetens grundläggande identifierare som en parameter för att matcha den aktuella versionen av hemligheten för kryptering och cachelagrar informationen lokalt. Använd CachingKeyResolver för cachelagring; användare som inte förväntas implementera sina egna cachelagring logik.
3. Använd cachelagring matcharen som indata när du skapar krypteringsprincipen.

Mer information om Key Vault fungerar finns i den [kryptering kodexempel](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Bästa praxis
Stöd för kryptering är endast tillgänglig i storage-klientbiblioteket för .NET. Windows Phone och Windows Runtime stöder för närvarande inte kryptering.

> [!IMPORTANT]
> Tänk på dessa viktiga punkter när du använder client side encryption:
> 
> * När läsning eller skrivning till en krypterad blob, använder du hela blob ladda upp kommandon och intervallet/hela blob download kommandon. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som till exempel placera Block, placera Blockeringslista, skriva sidor, rensa sidor eller lägga till Block Annars kan du skadad krypterade blobben och gör den oläslig.
> * För tabeller finns en liknande begränsning. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.
> * Om du ställer in metadata för krypterad blob kan du skriva över de krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen metadata inte är additiva. Detta gäller även för ögonblicksbilder Undvik att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om metadata måste anges, måste du anropa den **FetchAttributes** metoden först för att hämta den aktuella krypteringsmetadata för och undvika samtidiga skrivningar medan metadata anges.
> * Aktivera den **RequireEncryption** -egenskapen i begäran standardalternativen för användare som ska fungera med endast krypterade data. Nedan finns mer information.
> 
> 

## <a name="client-api--interface"></a>API-klient / gränssnitt
När du skapar ett EncryptionPolicy-objekt, kan användare ange endast en nyckel (implementera IKey), bara en matchare (implementera IKeyResolver) eller båda. IKey är den grundläggande nyckeltyp som identifieras med en nyckelidentifierare och som ger logiken för radbrytning/uppackning. IKeyResolver används för att lösa en nyckel under dekrypteringen. Den definierar en ResolveKey-metod som returnerar en IKey får en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

* Nyckeln används alltid för kryptering, och om en nyckel resulterar i ett fel.
* För dekryptering:
  * Viktiga matcharen anropas om att hämta nyckeln har angetts. Om matcharen har angetts men inte har en mappning för nyckel-ID, inträffar ett fel.
  * Om matchare har angetts men en nyckel har angetts, används nyckeln om dess ID: t matchar den nödvändiga Nyckelidentifieraren. Om det ID: t inte matchar genereras ett fel.

Kodexemplen i den här artikeln visar hur en krypteringsprincip för och arbeta med krypterade data, men Visa inte arbeta med Azure Key Vault. Den [kryptering exempel](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) på GitHub visa en mer detaljerad slutpunkt till slutpunkt-scenario för blobar, köer och tabeller, tillsammans med Key Vault-integrering.

### <a name="requireencryption-mode"></a>RequireEncryption läge
Användare kan välja att ett läge där alla överföra och hämta filer måste vara krypterat. I det här läget misslyckas försök att överföra data utan en krypteringsprincip eller hämta data som inte är krypterad i tjänsten på klienten. Den **RequireEncryption** alternativ Begäranobjektet bestämmer detta beteende. Om ditt program kommer att kryptera alla objekt som lagras i Azure Storage så du kan ange den **RequireEncryption** egenskapen på standardalternativen för begäran för klientobjektet. Till exempel **CloudBlobClient.DefaultRequestOptions.RequireEncryption** till **SANT** att kräva kryptering för alla blob-åtgärder som utförs via klientobjektet.


### <a name="blob-service-encryption"></a>BLOB service-kryptering
Skapa en **BlobEncryptionPolicy** objektet och ange den i alternativ för begäran (per API eller på en klient med hjälp av **DefaultRequestOptions**). Allt annat ska hanteras av klientbiblioteket internt.

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

### <a name="queue-service-encryption"></a>Kryptering av lagringstjänst för kö
Skapa en **QueueEncryptionPolicy** objektet och ange den i alternativ för begäran (per API eller på en klient med hjälp av **DefaultRequestOptions**). Allt annat ska hanteras av klientbiblioteket internt.

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

### <a name="table-service-encryption"></a>Table service-kryptering
Förutom att skapa en krypteringsprincip för och ange den på begäran alternativen, måste du antingen ange en **EncryptionResolver** i **TableRequestOptions**, eller ange attributet [EncryptProperty] för den entiteten.

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
Observera att kryptera din lagring data resulterar i ytterligare prestanda försämras. Innehållsnyckeln och IV måste genereras själva innehållet måste vara krypterat och ytterligare metadata måste formateras och laddat upp. Det här arbetet varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testa sina program för prestanda under utvecklingen.

## <a name="next-steps"></a>Nästa steg
* [Självstudier: Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Ladda ned den [Azure Storage-klientbiblioteket för .NET NuGet-paket](https://www.nuget.org/packages/WindowsAzure.Storage)
* Ladda ned Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [klienten](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/), och [tillägg](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) paket  
* Gå till den [dokumentation om Azure Key Vault](../../key-vault/key-vault-whatis.md)
