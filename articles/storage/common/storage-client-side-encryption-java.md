---
title: Kryptering på klientsidan med Java för Microsoft Azure Storage | Microsoft-dokument
description: Azure Storage Client Library for Java stöder kryptering och integrering på klientsidan med Azure Key Vault för maximal säkerhet för dina Azure Storage-program.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32691e0ddcee3f5410b12f07a2fb80806345bc26
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460519"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Kryptering på klientsidan och Azure Key Vault med Java för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
[Azure Storage Client Library for Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) stöder kryptering av data i klientprogram innan du överför till Azure Storage och dekrypterar data när du hämtar till klienten. Biblioteket stöder också integrering med [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av lagringskontonyckel.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuverttekniken
Krypterings- och dekrypteringsprocesserna följer kuverttekniken.  

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuverttekniken
Kryptering via kuverttekniken fungerar på följande sätt:  

1. Azure storage-klientbiblioteket genererar en innehållskrypteringsnyckel (CEK), som är en symmetrisk nyckel för engångsbruk.  
2. Användardata krypteras med denna CEK.   
3. CEK slås sedan in (krypterad) med hjälp av nyckelkrypteringsnyckeln (KEK). KEK identifieras av en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vaults.  
   Själva lagringsklientbiblioteket har aldrig tillgång till KEK. Biblioteket anropar nyckelomslagningsalgoritmen som tillhandahålls av Key Vault. Användare kan välja att använda anpassade leverantörer för nyckel omslag / uppackning om så önskas.  
4. De krypterade data överförs sedan till Azure Storage-tjänsten. Den raderade nyckeln tillsammans med några ytterligare krypteringsmetadata lagras antingen som metadata (på en blob) eller interpoleras med krypterade data (kömeddelanden och tabellentiteter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuverttekniken
Dekryptering via kuverttekniken fungerar på följande sätt:  

1. Klientbiblioteket förutsätter att användaren hanterar nyckelkrypteringsnyckeln (KEK) antingen lokalt eller i Azure Key Vaults. Användaren behöver inte känna till den specifika nyckel som användes för kryptering. I stället kan en nyckelmatchare som matchar olika nyckelidentifierare till nycklar ställas in och användas.  
2. Klientbiblioteket hämtar krypterade data tillsammans med allt krypteringsmaterial som lagras på tjänsten.  
3. Krypteringsnyckeln för för inslaget innehåll (CEK) packas sedan upp (dekrypteras) med hjälp av nyckelkrypteringsnyckeln (KEK). Även här har klientbiblioteket inte tillgång till KEK. Den anropar helt enkelt den anpassade eller Key Vault-leverantörens uppackningsalgoritm.  
4. Innehållskrypteringsnyckeln (CEK) används sedan för att dekryptera krypterade användardata.

## <a name="encryption-mechanism"></a>Krypteringsmekanism
Lagringsklientbiblioteket använder [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Specifikt, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Varje tjänst fungerar lite annorlunda, så vi kommer att diskutera var och en av dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande endast kryptering av hela blobbar. Specifikt stöds kryptering när användare använder **uppladdningsmetoden*** eller **metoden openOutputStream.** För nedladdningar stöds både fullständiga och intervallnedladdningar.  

Under krypteringen genererar klientbiblioteket en slumpmässig initieringsvektor (IV) på 16 byte, tillsammans med en slumpmässig innehållskrypteringsnyckel (CEK) på 32 byte och utför kuvertkryptering av blob-data med hjälp av den här informationen. Den raderade CEK och några ytterligare krypteringsmetadata lagras sedan som blobmetadata tillsammans med den krypterade bloben på tjänsten.

> [!WARNING]
> Om du redigerar eller laddar upp dina egna metadata för blobben måste du se till att metadata bevaras. Om du laddar upp nya metadata utan dessa metadata kommer de raderade CEK- och IV-metadata att gå förlorade och blob-innehållet kommer aldrig att hämtas igen.
> 
> 

Att ladda ned en krypterad blob innebär att hämta innehållet i hela blobben med hjälp av **de hämtningsbara**/metoderna**openInputStream.** Den inslagna CEK är oförpackade och används tillsammans med IV (lagras som blob metadata i detta fall) för att returnera dekrypterade data till användarna.

Ladda ner ett godtyckligt intervall **(downloadRange** metoder) i den krypterade blob innebär att justera det intervall som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas för att framgångsrikt dekryptera det begärda intervallet.  

Alla blobtyper (blockblobar, sidblobar och tilläggsblobbar) kan krypteras/dekrypteras med det här schemat.

### <a name="queues"></a>Köer
Eftersom kömeddelanden kan vara av valfritt format definierar klientbiblioteket ett anpassat format som innehåller Initieringsvektorn (IV) och krypteringsnyckeln för krypterat innehåll (CEK) i meddelandetexten.  

Under kryptering genererar klientbiblioteket en slumpmässig IV på 16 byte tillsammans med en slumpmässig CEK på 32 byte och utför kuvertkryptering av kömeddelandetexten med den här informationen. Den raderade CEK och några ytterligare krypteringsmetadata läggs sedan till i det krypterade kömeddelandet. Det här ändrade meddelandet (visas nedan) lagras på tjänsten.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Under dekryptering extraheras den raderade nyckeln från kömeddelandet och packas upp. IV extraheras också från kömeddelandet och används tillsammans med den oförpackade nyckeln för att dekryptera kömeddelandedata. Observera att krypteringsmetadata är små (under 500 byte), så medan det räknas mot 64KB-gränsen för ett kömeddelande bör effekten vara hanterbar.

### <a name="tables"></a>Tabeller
Klientbiblioteket stöder kryptering av entitetsegenskaper för att infoga och ersätta åtgärder.

> [!NOTE]
> Sammanfogning stöds för närvarande inte. Eftersom en delmängd av egenskaper kan ha krypterats tidigare med en annan nyckel, helt enkelt slå samman de nya egenskaperna och uppdatera metadata kommer att resultera i dataförlust. Sammanslagningen måste antingen göra extra serviceanrop för att läsa den befintliga entiteten från tjänsten, eller använda en ny nyckel per egenskap, som båda inte är lämpliga av prestandaskäl.
> 
> 

Kryptering av tabelldata fungerar på följande sätt:  

1. Användare anger vilka egenskaper som ska krypteras.  
2. Klientbiblioteket genererar en slumpmässig initieringsvektor (IV) på 16 byte tillsammans med en krypteringsnyckel för slumpmässigt innehåll (CEK) på 32 byte för varje entitet och utför kuvertkryptering på de enskilda egenskaper som ska krypteras genom att härleda en ny IV per egenskap. Den krypterade egenskapen lagras som binära data.  
3. Den raderade CEK och några ytterligare krypteringsmetadata lagras sedan som ytterligare två reserverade egenskaper. Den första reserverade egenskapen (_ClientEncryptionMetadata1) är en strängegenskap som innehåller informationen om IV, version och radbruten nyckel. Den andra reserverade egenskapen (_ClientEncryptionMetadata2) är en binär egenskap som innehåller informationen om de egenskaper som krypteras. Informationen i den här andra egenskapen (_ClientEncryptionMetadata2) är i sig krypterad.  
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering kan användare nu bara ha 250 anpassade egenskaper i stället för 252. Den totala storleken på entiteten måste vara mindre än 1 MB.  
   
   Observera att endast strängegenskaper kan krypteras. Om andra typer av egenskaper ska krypteras måste de konverteras till strängar. De krypterade strängarna lagras på tjänsten som binära egenskaper och de konverteras tillbaka till strängar efter dekryptering.
   
   För tabeller måste användarna, utöver krypteringsprincipen, ange vilka egenskaper som ska krypteras. Detta kan göras genom att antingen ange ett [Kryptera]-attribut (för POCO-entiteter som härleds från TableEntity) eller en krypteringsmatchningsverktyget i begäransalternativ. En krypteringsmatchningsordnare är ett ombud som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras när du skriver till kabeln. Ombudet ger också möjlighet till logik kring hur egenskaper krypteras. (Om du till exempel X krypterar du egenskap A; krypterar annars egenskaperna A och B.) Observera att det inte är nödvändigt att ange den här informationen när du läser eller frågar entiteter.

### <a name="batch-operations"></a>Batch-operationer
I batchåtgärder används samma KEK över alla rader i batch-åtgärden eftersom klientbiblioteket endast tillåter ett alternativobjekt (och därmed en princip/KEK) per batch-åtgärd. Klientbiblioteket genererar dock internt en ny slumpmässig IV och slumpmässig CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i krypterings resolver.

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna är krypterade kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du försöker blir resultaten felaktiga eftersom tjänsten skulle försöka jämföra krypterade data med okrypterade data.
> 
> 
> Om du vill utföra frågeåtgärder måste du ange en nyckelmatchning som kan matcha alla nycklar i resultatuppsättningen. Om en entitet som ingår i frågeresultatet inte kan matchas till en provider, genererar klientbiblioteket ett fel. För alla frågor som utför projektioner på serversidan lägger klientbiblioteket till de särskilda egenskaperna för krypteringsmetadata (_ClientEncryptionMetadata1 och _ClientEncryptionMetadata2) som standard i de markerade kolumnerna.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Genom att använda Azure Key Vault kan användare kryptera nycklar och hemligheter (t.ex. autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, . PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). Mer information finns i [Vad är en Azure Key Vault?](../../key-vault/general/overview.md).

Lagringsklientbiblioteket använder corebiblioteket för Key Vault för att tillhandahålla ett gemensamt ramverk i Azure för hantering av nycklar. Användarna får också ytterligare fördelen av att använda biblioteket för Key Vault-tillägg. Tilläggsbiblioteket ger användbara funktioner kring enkla och sömlösa symmetriska/RSA-lokala och molnnyckelleverantörer samt med aggregering och cachelagring.

### <a name="interface-and-dependencies"></a>Gränssnitt och beroenden
Det finns tre Key Vault-paket:  

* azure-keyvault-core innehåller IKey och IKeyResolver. Det är ett litet paket utan beroenden. Lagringsklientbiblioteket för Java definierar det som ett beroende.
* azure-keyvault innehåller REST-klienten för Key Vault REST.  
* azure-keyvault-extensions innehåller tilläggskod som innehåller implementeringar av kryptografiska algoritmer och en RSAKey och en SymmetricKey. Det beror på namnområdena Core och KeyVault och tillhandahåller funktioner för att definiera en mängdmatchningsmatchare (när användare vill använda flera nyckelleverantörer) och en cachelagringsnyckelmatchare. Även om lagringsklientbiblioteket inte är direkt beroende av det här paketet, behöver de det här paketet om användarna vill använda Azure Key Vault för att lagra sina nycklar eller använda Key Vault-tilläggen för att använda de lokala och molnkryptiska leverantörerna.  
  
  Key Vault är utformad för prisvärda huvudnycklar och begränsningsgränser per Nyckelvalv är utformade med detta i åtanke. När du utför kryptering på klientsidan med Key Vault är den föredragna modellen att använda symmetriska huvudnycklar som lagras som hemligheter i Key Vault och cachelagras lokalt. Användarna måste göra följande:  

1. Skapa en hemlig offline och ladda upp den till Key Vault.  
2. Använd hemlighetens basidentifierare som en parameter för att lösa den aktuella versionen av hemligheten för kryptering och cachelagra den här informationen lokalt. Använd CachingKeyResolver för cachelagring; användare förväntas inte implementera sin egen cachelagringslogik.  
3. Använd cachelagringsmatcharen som indata när krypteringsprincipen skapas.
   Mer information om användning av Key Vault finns i krypteringskodexemplen.

## <a name="best-practices"></a>Bästa praxis
Krypteringsstöd är endast tillgängligt i lagringsklientbiblioteket för Java.

> [!IMPORTANT]
> Var medveten om dessa viktiga punkter när du använder kryptering på klientsidan:
> 
> * När du läser från eller skriver till en krypterad blob använder du hela blob-uppladdningskommandon och kommando för hämtning av intervall/hela blob. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som Put Block, Put Block List, Write Pages, Clear Pages eller Append Block; Annars kan du skada den krypterade blobben och göra den oläslig.
> * För tabeller finns det ett liknande villkor. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.  
> * Om du anger metadata på den krypterade blobben kan du skriva över de krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen av metadata inte är additiv. Detta gäller även för ögonblicksbilder. undvika att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om metadata måste anges, se till att anropa **metoden downloadAttributes** först för att hämta aktuella krypteringsmetadata och undvika samtidiga skrivningar medan metadata ställs in.  
> * Aktivera flaggan **requireEncryption** i standardalternativen för begäran för användare som endast ska arbeta med krypterade data. Se nedan för mer info.  
> 
> 

## <a name="client-api--interface"></a>Klient-API / Gränssnitt
När du skapar ett EncryptionPolicy-objekt kan användarna bara ange en nyckel (implementera IKey), bara en resolver (implementera IKeyResolver) eller båda. IKey är den grundläggande nyckeltyp som identifieras med hjälp av en nyckelidentifierare och som ger logik för omslag / uppackning. IKeyResolver används för att matcha en nyckel under dekrypteringsprocessen. Den definierar en ResolveKey-metod som returnerar en IKey som ges en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

* För kryptering används nyckeln alltid och frånvaron av en nyckel resulterar i ett fel.  
* För dekryptering:  
  
  * Nyckelmatchningsorden anropas om det anges för att hämta nyckeln. Om matcharen har angetts men inte har någon mappning för nyckelidentifieraren genereras ett fel.  
  * Om matcharen inte har angetts men en nyckel anges används nyckeln om dess identifierare matchar den nödvändiga nyckelidentifieraren. Om identifieraren inte matchar genereras ett fel.  
    
    [Krypteringsexexemplen](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) visar ett mer detaljerat end-to-end-scenario för blobbar, köer och tabeller, tillsammans med Key Vault-integrering.

### <a name="requireencryption-mode"></a>Krävkrypningsläge
Användare kan eventuellt aktivera ett driftsläge där alla uppladdningar och nedladdningar måste krypteras. I det här läget misslyckas försök att ladda upp data utan en krypteringsprincip eller hämta data som inte är krypterade på tjänsten på klienten. Flaggan **requireEncryption** för objektet för begäransalternativ styr detta. Om ditt program krypterar alla objekt som lagras i Azure Storage kan du ange egenskapen **requireEncryption** på standardalternativen för begäran för tjänstklientobjektet.   

Använd till exempel **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** för att kräva kryptering för alla blob-åtgärder som utförs via klientobjektet.

### <a name="blob-service-encryption"></a>Kryptering av Blob-tjänst
Skapa ett **BlobEncryptionPolicy-objekt** och ange det i begäransalternativen (per API eller på klientnivå med hjälp av **DefaultRequestOptions**). Allt annat kommer att hanteras av klientbiblioteket internt.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Kryptering av kötjänst
Skapa ett **QueueEncryptionPolicy-objekt** och ange det i begäransalternativen (per API eller på klientnivå med hjälp av **DefaultRequestOptions**). Allt annat kommer att hanteras av klientbiblioteket internt.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Kryptering av tabelltjänst
Förutom att skapa en krypteringsprincip och ange den för alternativ för begäran måste du antingen ange en **EncryptionResolver** i **TableRequestOptions**eller ange attributet [Encrypt] på entitetens getter och setter.

### <a name="using-the-resolver"></a>Använda resolver

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan, om entiteten implementerar TableEntity, kan egenskaperna getter och setter dekoreras med attributet [Encrypt] i stället för att ange **Attributet EncryptionResolver**.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Kryptering och prestanda

Observera att om du krypterar lagringsdata får du ytterligare prestandakostnader. Innehållsnyckeln och IV måste genereras, själva innehållet måste krypteras och ytterligare metadata måste formateras och laddas upp. Denna omkostnader varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testar sina program för prestanda under utveckling.

## <a name="next-steps"></a>Nästa steg

* Ladda ned [Azure Storage Client Library för Java Maven-paketet](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Ladda ned [Azure Storage Client Library for Java Source Code från GitHub](https://github.com/Azure/azure-storage-java)
* Ladda ned Azure Key Vault Maven-biblioteket för Java Maven-paket:
  * [Kärnpaket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
  * [Klientpaket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Besök [dokumentationen](../../key-vault/general/overview.md) för Azure Key Vault
