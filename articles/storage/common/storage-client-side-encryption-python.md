---
title: "Klientsidans kryptering med Python för Microsoft Azure Storage | Microsoft Docs"
description: "Azure Storage-klientbibliotek för Python stöder kryptering på klientsidan för maximal säkerhet för dina Azure Storage-program."
services: storage
documentationcenter: python
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: f9bf7981-9948-4f83-8931-b15679a09b8a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: bf6696cfdfe9fc18dd2f000162a4e787a7ca6e21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Klientsidans kryptering med Python för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
Den [Azure Storage-klientbibliotek för Python](https://pypi.python.org/pypi/azure-storage) har stöd för kryptering av data i klientprogram före överföringen till Azure Storage och dekryptera data under nedladdningen till klienten.

> [!NOTE]
> Azure Storage Python-bibliotek finns i förhandsgranskningen.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert-teknik
Processerna för kryptering och dekryptering Följ kuvert-teknik.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert-teknik
Kryptering via kuvert tekniken fungerar på följande sätt:

1. Azure storage-klientbiblioteket genererar en innehåll krypteringsnyckel (CEK), vilket är en gång Använd symmetrisk nyckel.
2. Användardata krypteras med den här CEK.
3. CEK kapslas sedan (krypterade) med hjälp av krypteringsnyckel (KEK). KEK identifieras med en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel som hanteras lokalt.
   Storage-klientbiblioteket själva har aldrig åtkomst till KEK. Biblioteket anropar algoritmen viktiga radbrytning som tillhandahålls av KEK. Användare kan välja att använda anpassade providers för viktiga radbrytning/uppackning om så önskas.
4. Krypterade data överförs sedan till Azure Storage-tjänsten. Nyckeln omsluten tillsammans med vissa ytterligare krypteringsmetadata lagras som metadata (på en blob) eller interpolerade med krypterade data (Kömeddelanden och tabellentiteter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert-teknik
Dekryptering via kuvert tekniken fungerar på följande sätt:

1. Klientbiblioteket förutsätter att användaren kan hantera nyckelkryptering nyckel (KEK) lokalt. Användaren behöver inte veta nyckeln som används för kryptering. I stället kan en nyckel matchare som motsvarar nycklar olika nyckelidentifierare ställa in och användas.
2. Klientbiblioteket hämtar krypterade data tillsammans med kryptering material som är lagrad på tjänsten.
3. Omslutna innehåll krypteringsnyckeln (CEK) är okrypterad (dekrypterade) med hjälp av krypteringsnyckel nyckel (KEK). Här igen, har klientbiblioteket inte åtkomst till KEK. Den anropar bara den anpassade providern borttagning av algoritmen.
4. Innehåll krypteringsnyckeln (CEK) används sedan för att dekryptera den krypterade informationen.

## <a name="encryption-mechanism"></a>Krypteringsalgoritm
Storage-klientbiblioteket använder [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Mer specifikt [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Varje tjänst fungerar olika, så vi upp dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande kryptering av hela blobbar. Mer specifikt kryptering stöds när användare använder den **skapa*** metoder. För hämtade filer, både fullständig och intervallet nedladdningar som stöds och parallellisering för både ladda upp och hämta är tillgänglig.

Under krypteringen klientbiblioteket Generera en slumpmässig initieringen Vector (IV) av 16 byte, tillsammans med en slumpmässig innehåll krypteringsnyckel (CEK) 32 byte och utföra kuvert kryptering av blob-data med den här informationen. Omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som blob metadata tillsammans med den kryptera blobben-tjänsten.

> [!WARNING]
> Om du redigerar eller överföra metadata för blobben, måste du se till att dessa metadata bevaras. Om du överför nya metadata utan metadata omsluten CEK, IV och andra metadata går förlorade och blob-innehåll aldrig kommer att hämtas igen.
> 
> 

Hämtar en krypterad blob omfattar hämtar innehållet i hela blob med hjälp av den **hämta*** bekvämlighet metoder. Omslutna CEK oförpackade och används tillsammans med IV (som lagras som blobbmetadata i det här fallet) för att returnera dekrypterade data till användarna.

Hämta ett godtyckligt intervall (**hämta*** metoder med intervallet parametrar skickades) innebär att området som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas för att dekryptera har det begärda intervallet i krypterade blob.

Blockblobbar och sidblobbar kan bara vara krypterad/dekrypteras med hjälp av det här schemat. Det finns inget stöd för kryptering av tilläggsblobar.

### <a name="queues"></a>Köer
Eftersom Kömeddelanden kan vara i valfritt format, definierar klientbiblioteket ett anpassat format som innehåller initieringen Vector (IV) och den krypterade innehållet krypteringsnyckeln (CEK) i meddelandetexten.

Under krypteringen klientbiblioteket genererar en slumpmässig IV av 16 byte tillsammans med en slumpmässig CEK 32 byte och utför kuvert kryptering av meddelandetexten kön med den här informationen. Omslutna CEK och vissa ytterligare krypteringsmetadata läggs sedan till krypterade kömeddelandet. Ändrade meddelandet (se nedan) lagras på tjänsten.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. Omslutna CEK och vissa ytterligare krypteringsmetadata lagras sedan som ytterligare två reserverade egenskaper. Först reserverade egenskapen (\_ClientEncryptionMetadata1) är en strängegenskap som innehåller information om IV, version och omslutna nyckel. Andra reserverade egenskapen (\_ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen (\_ClientEncryptionMetadata2) krypteras.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering, har användare nu endast 250 anpassade egenskaper i stället för 252. Den totala storleken på enheten måste vara mindre än 1MB.
   
   Observera att endast egenskaperna för anslutningssträngen kan vara krypterad. Om andra typer av egenskaper som ska krypteras, måste de konverteras till strängar. Krypterade strängar lagras på tjänsten som binära egenskaper och de konverteras till strängar (rådata strängar, inte EntityProperties med typen EdmType.STRING) efter dekrypteringen.
   
   För tabeller, förutom krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras genom att antingen lagra dessa egenskaper i TableEntity objekt typ inställt på EdmType.STRING och kryptera angetts till true eller anger encryption_resolver_function för tableservice-objektet. En kryptering lösare är en funktion som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logik runt hur egenskaperna är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Observera att det inte nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

### <a name="batch-operations"></a>Batchåtgärder
En krypteringsprincipen gäller för alla rader i batchen. Klientbiblioteket genererar internt en ny slumpmässiga IV och slumpmässiga CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i gruppen genom att definiera problemet i matcharen kryptering.
Om en batch har skapats som en kontext manager via metoden tableservice batch(), tillämpas den tableservice krypteringsprincipen automatiskt i gruppen. Om en batch har skapats explicit genom att anropa konstruktorn, krypteringsprincipen skickas som en parameter och vänster oförändrade under processernas livstid i batchen.
Observera att entiteter har krypterats som de infogas i gruppen med hjälp av den batchen krypteringsprincipen (entiteter inte är krypterad vid tiden för när en batch med hjälp av den tableservice krypteringsprincipen).

### <a name="queries"></a>Frågor
Om du vill utföra frågeåtgärder, måste du ange en nyckel matchare som kan matcha alla nycklar i resultatmängden. Om en entitet i frågeresultatet inte kan matchas till en provider, genereras klientbiblioteket ett fel. För en fråga som utför server side projektioner klientbiblioteket lägger till särskilda kryptering metadataegenskaper (\_ClientEncryptionMetadata1 och \_ClientEncryptionMetadata2) som standard till de markerade kolumnerna.

> [!IMPORTANT]
> Tänk på dessa viktiga punkter när du använder kryptering på klientsidan:
> 
> * När läsning eller skrivning till en krypterad blob Använd hela blob överför kommandon och intervall/hela blob download kommandon. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som till exempel placera Block, placera Blockeringslista, skriva sidor eller rensa sidor. Annars kan du skadad krypterade blob och att den inte kan läsas.
> * För tabeller finns en liknande begränsning. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.
> * Om du anger metadata för den kryptera blobben kan du skriva över den krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen metadata inte är additiva. Detta gäller även för ögonblicksbilder; Undvik att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om du måste ange metadata, måste du anropa den **get_blob_metadata** metoden först för att hämta krypteringsmetadata för den aktuella och undvika samtidiga skrivningar metadata anges.
> * Aktivera den **require_encryption** flaggan på serviceobjektet för användare som ska fungerar bara med krypterade data. Mer information finns nedan.
> 
> 

Storage-klientbiblioteket förväntar angivna KEK och viktiga matchning för att implementera följande gränssnitt. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stöd för hantering av Python KEK väntar och kommer att integreras i det här biblioteket när du är klar.

## <a name="client-api--interface"></a>Programmeringsgränssnitt mot klienten / gränssnitt
När ett service lagringsobjekt (d.v.s. blockblobservice) har skapats kan användaren kan tilldela värden till fälten som utgör en krypteringsprincip för: key_encryption_key key_resolver_function, och require_encryption. Användare kan ange endast en KEK endast en matchare eller båda. key_encryption_key är den grundläggande nyckeln som identifieras med en nyckelidentifierare och som innehåller logiken för radbrytning/uppackning. key_resolver_function används för att lösa en nyckel under dekrypteringsprocessen. Returnerar en giltig KEK ges en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

KEK måste implementera följande metoder för att kryptera data har:

* wrap_key(cek): radbryts angivna CEK (byte) med en algoritm för användarens val. Returnerar den omslutna nyckeln.
* get_key_wrap_algorithm(): returnerar den algoritm som används för att omsluta nycklar.
* get_kid(): returnerar strängen nyckel-id för den här KEK.
  KEK måste implementera följande metoder för att dekryptera data har:
* unwrap_key (cek, algoritm): returnerar okrypterad form av den angivna CEK med angiven sträng-algoritm.
* get_kid(): returnerar en sträng nyckel-id för den här KEK.

Viktiga matcharen måste minst implementera en metod som, anges en nyckel-id, returnerar motsvarande KEK implementerar gränssnittet ovan. Endast den här metoden är att tilldelas till egenskapen key_resolver_function för serviceobjektet.

* Nyckeln används alltid för kryptering, och avsaknaden av en nyckel resulterar i ett fel.
* För dekryptering:
  
  * Viktiga matcharen anropas om att hämta nyckeln har angetts. Om matcharen har angetts men inte har en mappning för nyckel-ID, returneras ett fel.
  * Om DNS-matchare inte har angetts, men en nyckel har angetts, används nyckeln om dess identifierare som matchar den nödvändiga Nyckelidentifieraren. Om ID inte matchar genereras ett fel.
    
    Exempel kryptering i azure.storage.samples <fix URL>demonstrera en mer detaljerad slutpunkt till slutpunkt för BLOB, köer och tabeller.
      Exempel implementeringar av KEK och viktiga matchning finns i exempelfilerna som KeyWrapper och KeyResolver respektive.

### <a name="requireencryption-mode"></a>RequireEncryption läge
Användare kan välja att ett läge där alla överföringar och hämtningsbara filer måste vara krypterad. I det här läget misslyckas försök att överföra data utan en krypteringsprincip för eller hämta data som inte är krypterad för tjänsten på klienten. Den **require_encryption** flaggan på serviceobjektet styr det här beteendet.

### <a name="blob-service-encryption"></a>Kryptering för BLOB-tjänst
Ange krypteringen principfält på blockblobservice-objektet. Allt annat hanteras av klientbiblioteket internt.

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

### <a name="queue-service-encryption"></a>Kön service-kryptering
Ange krypteringen principfält på queueservice-objektet. Allt annat hanteras av klientbiblioteket internt.

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

### <a name="table-service-encryption"></a>Tabellen service-kryptering
Förutom att skapa en krypteringsprincip för och ange värdet på begäran alternativ, måste du antingen ange en **encryption_resolver_function** på den **tableservice**, eller ange attributet kryptera på EntityProperty.

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
Som nämnts ovan är kan en egenskap markeras för kryptering genom att lagra det i ett EntityProperty objekt och ange fältet kryptera.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Kryptering och prestanda
Observera att kryptera din lagring data resulterar i ytterligare prestanda försämras. Innehållsnyckeln och IV måste skapas själva innehållet måste krypteras och ytterligare metadata måste vara formaterad och har överförts. Det här arbetet varierar beroende på mängden data som krypteras. Vi rekommenderar att kunder alltid testa sina program för prestanda under utveckling.

## <a name="next-steps"></a>Nästa steg
* Hämta den [Azure Storage-klientbibliotek för Java PyPi paketet](https://pypi.python.org/pypi/azure-storage)
* Hämta den [Azure Storage-klientbibliotek för Python källkod från GitHub](https://github.com/Azure/azure-storage-python)
