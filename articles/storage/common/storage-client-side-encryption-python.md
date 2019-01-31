---
title: Client Side Encryption med Python för Microsoft Azure Storage | Microsoft Docs
description: Client side encryption för maximal säkerhet för dina Azure Storage-program har stöd för Azure Storage-klientbiblioteket för Python.
services: storage
author: lakasa
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: dfff159d7e0204a752935458a2b4845499c0d652
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453407"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Client Side Encryption med Python för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
Den [Azure Storage-klientbibliotek för Python](https://pypi.python.org/pypi/azure-storage) har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptering av data under nedladdningen till klienten.

> [!NOTE]
> Azure Storage Python-bibliotek finns i förhandsversion.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert-teknik
Processer för kryptering och dekryptering Följ kuvert-teknik.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert-teknik
Kryptering via kuvert-tekniken fungerar på följande sätt:

1. Azure storage-klientbiblioteket genererar en content krypteringsnyckel (CEK), vilket är en symmetrisk nyckel för en gång användning.
2. Användardata har krypterats med den här CEK.
3. CEK kapslas sedan (krypterad) med klartextnyckel-nyckel (KEK). KEK identifieras med en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel som hanteras lokalt.
   Storage-klientbiblioteket sig själv har aldrig åtkomst till KEK. Biblioteket anropar viktiga radbrytning algoritmen som tillhandahålls av KEK. Användare kan välja att använda anpassade providers för viktiga radbrytning/uppackning om så önskas.
4. Krypterade data överförs sedan till Azure Storage-tjänsten. Den omslutna nyckeln tillsammans med vissa ytterligare krypteringsmetadata lagras som metadata (för en blob) eller interpolerade med krypterade data (Kömeddelanden och tabellenheter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert-teknik
Dekryptering via kuvert-tekniken fungerar på följande sätt:

1. Klientbiblioteket förutsätter att du kan hantera nyckelkrypteringsnyckel (KEK) lokalt. Användaren behöver inte veta nyckeln som används för kryptering. I stället kan en nyckel matchare som matchas olika nyckelidentifierare till nycklar, Ställ in och används.
2. Klientbiblioteket laddar ned den krypterade informationen tillsammans med eventuella kryptering material som är lagrad på tjänsten.
3. Den omslutna innehåll krypteringsnyckeln (CEK) är packats upp (dekrypterade) med hjälp av krypteringsnyckel nyckel (KEK). Här igen, har klientbiblioteket inte åtkomst till KEK. Den anropar bara den anpassade providern borttagning av algoritmen.
4. Innehåll krypteringsnyckeln (CEK) används sedan för att dekryptera krypterade informationen.

## <a name="encryption-mechanism"></a>Krypteringsalgoritm
Storage-klientbiblioteket använder [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Mer specifikt [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Var och en service fungerar på ett något annorlunda så vi upp vart och ett av dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande kryptering av hela blobar. Mer specifikt kryptering stöds när användarna använder den **skapa*** metoder. För nedladdningar, både fullständig och intervallet nedladdningar som stöds och parallellisering av både överföring och hämtning är tillgänglig.

Under krypteringen ska klientbiblioteket generera ett slumpmässigt initieringen vektor (IV) med 16 byte, tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) på 32 byte och utföra kuvert kryptering av blob-data med den här informationen. Den omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som blob-metadata tillsammans med krypterade blobben på tjänsten.

> [!WARNING]
> Om du redigerar eller ladda upp en egen metadata för blobben, måste du se till att dessa metadata bevaras. Om du överför nya metadata utan metadata, omslutna CEK, IV och andra metadata går förlorade och blobinnehållet inte kommer att hämtningsbar igen.
> 
> 

Ladda ned en krypterad blob innebär att du hämtar innehållet i hela bloben med den **hämta*** bekväma metoder. Den omslutna CEK oinslagna och används tillsammans med IV (som lagras som blob-metadata i det här fallet) för att returnera dekrypterade data till användarna.

Ladda ned ett godtyckligt adressintervall (**hämta*** metoder med intervallet parametrar som skickats in) i krypterad blob innebär att justera de intervall som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas till att har dekryptera det begärda intervallet.

Blockblobar och sidblobar kan endast vara krypterad/dekrypteras med hjälp av det här schemat. Det finns för närvarande inget stöd för kryptering av tilläggsblobar.

### <a name="queues"></a>Köer
Eftersom Kömeddelanden kan vara i valfritt format, definierar klientbiblioteket ett anpassat format som innehåller initieringen vektor (IV) och den krypterade innehåll krypteringsnyckeln (CEK) i meddelandetexten.

Under krypteringen klientbiblioteket genererar en slumpmässig IV med 16 byte tillsammans med en slumpmässig CEK på 32 byte och utför kuvert kryptering av meddelandetexten kön med den här informationen. Den omslutna CEK och vissa ytterligare krypteringsmetadata läggs sedan till krypterade kömeddelandet. Det här ändrade meddelandet (se nedan) lagras på tjänsten.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. Den omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som två ytterligare reserverade egenskaper. Först reserverad egenskap (\_ClientEncryptionMetadata1) är en strängegenskap som innehåller information om IV, version och omslutna nyckel. Andra reserverad egenskap (\_ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen (\_ClientEncryptionMetadata2) krypteras.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering, kan användare nu har endast 250 anpassade egenskaper i stället för 252. Den totala storleken på entiteten måste vara mindre än 1MB.
   
   Observera att endast egenskaperna för anslutningssträngen kan krypteras. Om det finns andra typer av egenskaper som ska krypteras, måste de konverteras till strängar. Krypterade strängar som är lagrade på tjänsten som binära egenskaper och de konverteras till strängar (raw strängar, inte EntityProperties med typen EdmType.STRING) efter dekryptering.
   
   För tabeller, utöver krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras genom att antingen lagra dessa egenskaper i TableEntity objekt med typen till EdmType.STRING och kryptera inställd på SANT eller ange encryption_resolver_function tableservice-objektet. En kryptering Konfliktlösaren är en funktion som tar en partitionsnyckel och radnyckel egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska vara krypterad. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till ledningen. Delegaten ger också möjlighet att logic kring hur egenskaper som är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Observera att det inte är nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

### <a name="batch-operations"></a>Batchåtgärder
En princip för kryptering gäller för alla rader i batchen. Klientbiblioteket genererar internt ett nytt slumpmässigt IV och slumpmässiga CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i Konfliktlösare för kryptering.
Om en batch skapas som en kontext manager via metoden tableservice batch() kan tillämpas automatiskt på tableservice krypteringsprincipen i gruppen. Om en batch skapas uttryckligen genom att anropa konstruktorn krypteringsprincipen skickas som en parameter och till vänster ska ändras för livslängden för batchen.
Observera att enheter krypteras när de infogas i batch med hjälp av det batch-krypteringspolicyn (entiteter inte är krypterad vid tidpunkten för transaktionen batch med hjälp av den tableservice kryptering princip).

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna som är krypterade, kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du försöker att resultaten vara felaktig, eftersom tjänsten skulle ha försökt att jämföra krypterade data med okrypterade data.
> 
>
Om du vill utföra frågeåtgärder, måste du ange en nyckel matchare som kan matcha alla nycklar i resultatuppsättningen. Om en entitet i frågeresultatet inte kan matchas till en leverantör, genereras klientbiblioteket ett fel. För en fråga som utför server sida projektioner klientbiblioteket lägger till särskilda kryptering metadataegenskaper (\_ClientEncryptionMetadata1 och \_ClientEncryptionMetadata2) som standard till de markerade kolumnerna.

> [!IMPORTANT]
> Tänk på dessa viktiga punkter när du använder client side encryption:
> 
> * När läsning eller skrivning till en krypterad blob, använder du hela blob ladda upp kommandon och intervallet/hela blob download kommandon. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som till exempel placera Block, placera Blockeringslista, skriva sidor eller rensa sidor. Annars kan du skadad krypterade blobben och gör den oläslig.
> * För tabeller finns en liknande begränsning. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.
> * Om du ställer in metadata för krypterad blob kan du skriva över de krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen metadata inte är additiva. Detta gäller även för ögonblicksbilder Undvik att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om metadata måste anges, måste du anropa den **get_blob_metadata** metoden först för att hämta den aktuella krypteringsmetadata för och undvika samtidiga skrivningar medan metadata anges.
> * Aktivera den **require_encryption** flaggan service-objekt för användare som ska fungerar bara med krypterade data. Nedan finns mer information.
> 
> 

Storage-klientbiblioteket förväntar sig den angivna KEK och viktiga Konfliktlösare för att implementera följande gränssnitt. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stöd för Python KEK väntar och kommer att integreras i det här biblioteket när du är klar.

## <a name="client-api--interface"></a>API-klient / gränssnitt
När du har skapat ett service lagringsobjekt (d.v.s. blockblobservice) du kan tilldela värden till fälten som utgör en krypteringsprincip: key_encryption_key, key_resolver_function, och require_encryption. Användare kan ange endast en KEK endast en DNS-matchare, eller båda. key_encryption_key är den grundläggande nyckeltyp som identifieras med en nyckelidentifierare och som ger logiken för radbrytning/uppackning. key_resolver_function används för att lösa en nyckel under dekrypteringen. Returnerar en giltig KEK får en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

KEK måste implementera följande metoder för att kryptera data har:

* wrap_key(cek): Omsluter den angivna CEK (byte) som använder en algoritm för användarens val. Returnerar den omslutna nyckeln.
* get_key_wrap_algorithm(): Returnerar den algoritm som används för att omsluta nycklar.
* get_kid(): Returnerar strängen nyckel-id för den här KEK.
  KEK måste implementera de följande metoderna för att dekryptera data har:
* unwrap_key (cek, algoritmen): Returnerar packats upp form av den angivna CEK med angiven sträng-algoritmen.
* get_kid(): Returnerar en sträng nyckel-id för den här KEK.

Viktiga matcharen verkställer minst en metod som, givet ett nyckel-id, returnerar den motsvarande KEK som implementerar gränssnittet ovan. Endast den här metoden är att tilldelas till egenskapen key_resolver_function på objektet.

* Nyckeln används alltid för kryptering, och om en nyckel resulterar i ett fel.
* För dekryptering:
  
  * Viktiga matcharen anropas om att hämta nyckeln har angetts. Om matcharen har angetts men inte har en mappning för nyckel-ID, inträffar ett fel.
  * Om matchare har angetts men en nyckel har angetts, används nyckeln om dess ID: t matchar den nödvändiga Nyckelidentifieraren. Om det ID: t inte matchar genereras ett fel.
    
    Exempel för kryptering i azure.storage.samples <fix URL>visar en mer detaljerad slutpunkt till slutpunkt-scenario för blobbar, köer och tabeller.
      Exemplet implementeringar av KEK och viktiga matchare finns i exempelfilerna som KeyWrapper och KeyResolver respektive.

### <a name="requireencryption-mode"></a>RequireEncryption läge
Användare kan välja att ett läge där alla överföra och hämta filer måste vara krypterat. I det här läget misslyckas försök att överföra data utan en krypteringsprincip eller hämta data som inte är krypterad i tjänsten på klienten. Den **require_encryption** flaggan serviceobjektet styr detta beteende.

### <a name="blob-service-encryption"></a>BLOB service-kryptering
Ange krypteringen principfält blockblobservice-objektet. Allt annat ska hanteras av klientbiblioteket internt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Kryptering av lagringstjänst för kö
Ange krypteringen principfält queueservice-objektet. Allt annat ska hanteras av klientbiblioteket internt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Table service-kryptering
Förutom att skapa en krypteringsprincip för och ange den på begäran alternativen, måste du antingen ange en **encryption_resolver_function** på den **tableservice**, eller Ställ in attributet encrypt på den EntityProperty.

### <a name="using-the-resolver"></a>Med hjälp av matcharen

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.
def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan är kan en egenskap markeras för kryptering genom att lagra det i ett EntityProperty-objekt och ange fältet encrypt.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Kryptering och prestanda
Observera att kryptera din lagring data resulterar i ytterligare prestanda försämras. Innehållsnyckeln och IV måste genereras själva innehållet måste vara krypterat och ytterligare metadata måste formateras och laddat upp. Det här arbetet varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testa sina program för prestanda under utvecklingen.

## <a name="next-steps"></a>Nästa steg
* Ladda ned den [Azure Storage-klientbibliotek för Java PyPi paket](https://pypi.python.org/pypi/azure-storage)
* Ladda ned den [Azure Storage-klientbibliotek för Python källkod från GitHub](https://github.com/Azure/azure-storage-python)
