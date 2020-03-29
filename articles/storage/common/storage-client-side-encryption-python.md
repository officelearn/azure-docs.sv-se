---
title: Kryptering på klientsidan med Python
titleSuffix: Azure Storage
description: Azure Storage Client Library for Python stöder kryptering på klientsidan för maximal säkerhet för dina Azure Storage-program.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895369"
---
# <a name="client-side-encryption-with-python"></a>Kryptering på klientsidan med Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
[Azure Storage Client Library for Python](https://pypi.python.org/pypi/azure-storage) stöder kryptering av data i klientprogram innan du överför till Azure Storage och dekrypterar data när du hämtar till klienten.

> [!NOTE]
> Azure Storage Python-biblioteket är i förhandsversion.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuverttekniken
Krypterings- och dekrypteringsprocesserna följer kuverttekniken.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuverttekniken
Kryptering via kuverttekniken fungerar på följande sätt:

1. Azure storage-klientbiblioteket genererar en innehållskrypteringsnyckel (CEK), som är en symmetrisk nyckel för engångsbruk.
2. Användardata krypteras med denna CEK.
3. CEK slås sedan in (krypterad) med hjälp av nyckelkrypteringsnyckeln (KEK). KEK identifieras av en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel, som hanteras lokalt.
   Själva lagringsklientbiblioteket har aldrig tillgång till KEK. Biblioteket anropar nyckelomslagningsalgoritmen som tillhandahålls av KEK. Användare kan välja att använda anpassade leverantörer för nyckel omslag / uppackning om så önskas.
4. De krypterade data överförs sedan till Azure Storage-tjänsten. Den raderade nyckeln tillsammans med några ytterligare krypteringsmetadata lagras antingen som metadata (på en blob) eller interpoleras med krypterade data (kömeddelanden och tabellentiteter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuverttekniken
Dekryptering via kuverttekniken fungerar på följande sätt:

1. Klientbiblioteket förutsätter att användaren hanterar nyckelkrypteringsnyckeln (KEK) lokalt. Användaren behöver inte känna till den specifika nyckel som användes för kryptering. I stället kan en nyckelmatchningsordnare, som matchar olika nyckelidentifierare till nycklar, ställas in och användas.
2. Klientbiblioteket hämtar krypterade data tillsammans med allt krypteringsmaterial som lagras på tjänsten.
3. Krypteringsnyckeln för för inslaget innehåll (CEK) packas sedan upp (dekrypteras) med hjälp av nyckelkrypteringsnyckeln (KEK). Även här har klientbiblioteket inte tillgång till KEK. Det åberopar helt enkelt den anpassade leverantörens uppackningsalgoritm.
4. Innehållskrypteringsnyckeln (CEK) används sedan för att dekryptera krypterade användardata.

## <a name="encryption-mechanism"></a>Krypteringsmekanism
Lagringsklientbiblioteket använder [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användardata. Specifikt, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) läge med AES. Varje tjänst fungerar lite annorlunda, så vi kommer att diskutera var och en av dem här.

### <a name="blobs"></a>Blobar
Klientbiblioteket stöder för närvarande endast kryptering av hela blobbar. Specifikt stöds kryptering när användare använder **metoderna skapa*** . För nedladdningar stöds både fullständiga och intervallnedladdningar, och parallellisering av både uppladdning och nedladdning är tillgänglig.

Under krypteringen genererar klientbiblioteket en slumpmässig initieringsvektor (IV) på 16 byte, tillsammans med en slumpmässig innehållskrypteringsnyckel (CEK) på 32 byte och utför kuvertkryptering av blob-data med hjälp av den här informationen. Den raderade CEK och några ytterligare krypteringsmetadata lagras sedan som blobmetadata tillsammans med den krypterade bloben på tjänsten.

> [!WARNING]
> Om du redigerar eller laddar upp dina egna metadata för blobben måste du se till att metadata bevaras. Om du laddar upp nya metadata utan dessa metadata kommer de raderade CEK- och IV-metadata att gå förlorade och blob-innehållet kommer aldrig att hämtas igen.
> 
> 

Ladda ner en krypterad blob innebär att hämta innehållet i hela blob med hjälp av **få*** bekvämlighet metoder. Den inslagna CEK är oförpackade och används tillsammans med IV (lagras som blob metadata i detta fall) för att returnera dekrypterade data till användarna.

Ladda ner ett godtyckligt intervall **(få*** metoder med intervallparametrar skickas in) i den krypterade blob innebär att justera intervallet som tillhandahålls av användare för att få en liten mängd ytterligare data som kan användas för att framgångsrikt dekryptera det begärda intervallet.

Blockblobar och sidblobar kan bara krypteras/dekrypteras med det här schemat. Det finns för närvarande inget stöd för att kryptera tilläggsblobar.

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
3. Den raderade CEK och några ytterligare krypteringsmetadata lagras sedan som ytterligare två reserverade egenskaper. Den första reserverade egenskapen (\_ClientEncryptionMetadata1) är en strängegenskap som innehåller informationen om IV, version och radbruten nyckel. Den andra reserverade egenskapen (\_ClientEncryptionMetadata2) är en binär egenskap som innehåller informationen om de egenskaper som är krypterade. Informationen i den här\_andra egenskapen (ClientEncryptionMetadata2) är själv krypterad.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering kan användare nu bara ha 250 anpassade egenskaper i stället för 252. Den totala storleken på entiteten måste vara mindre än 1 MB.

   Observera att endast strängegenskaper kan krypteras. Om andra typer av egenskaper ska krypteras måste de konverteras till strängar. De krypterade strängarna lagras på tjänsten som binära egenskaper och de konverteras tillbaka till strängar (råsträngar, inte EntityProperties med typen EdmType.STRING) efter dekryptering.

   För tabeller måste användarna, utöver krypteringsprincipen, ange vilka egenskaper som ska krypteras. Detta kan göras genom att antingen lagra dessa egenskaper i TableEntity-objekt med typen inställd på EdmType.STRING och kryptera inställd på true eller ange encryption_resolver_function på tableservice-objektet. En krypteringsmatchningsordnare är en funktion som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras när du skriver till kabeln. Ombudet ger också möjlighet till logik kring hur egenskaper krypteras. (Om du till exempel X krypterar du egenskap A; krypterar annars egenskaperna A och B.) Observera att det inte är nödvändigt att ange den här informationen när du läser eller frågar entiteter.

### <a name="batch-operations"></a>Batch-operationer
En krypteringsprincip gäller för alla rader i batchen. Klientbiblioteket genererar internt en ny slumpmässig IV och slumpmässig CEK per rad i batchen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i krypterings resolver.
Om en batch skapas som kontexthanterare via metoden tableservice batch() tillämpas tableservices krypteringsprincip automatiskt på batchen. Om en batch skapas explicit genom att anropa konstruktorn måste krypteringsprincipen skickas som en parameter och lämnas oförändrad under batchens livstid.
Observera att entiteter krypteras när de infogas i batchen med hjälp av batchens krypteringsprincip (entiteter krypteras INTE vid tidpunkten för att binda batchen med hjälp av tableservices krypteringsprincip).

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna är krypterade kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du försöker blir resultaten felaktiga eftersom tjänsten skulle försöka jämföra krypterade data med okrypterade data.
> 
> 
> Om du vill utföra frågeåtgärder måste du ange en nyckelmatchning som kan matcha alla nycklar i resultatuppsättningen. Om en entitet som ingår i frågeresultatet inte kan matchas till en provider, genererar klientbiblioteket ett fel. För alla frågor som utför serverprojektioner lägger klientbiblioteket\_till de särskilda egenskaperna för \_krypteringmetadata (ClientEncryptionMetadata1 och ClientEncryptionMetadata2) som standard i de valda kolumnerna.
> 
> [!IMPORTANT]
> Var medveten om dessa viktiga punkter när du använder kryptering på klientsidan:
> 
> * När du läser från eller skriver till en krypterad blob använder du hela blob-uppladdningskommandon och kommando för hämtning av intervall/hela blob. Undvik att skriva till en krypterad blob med hjälp av protokollåtgärder som Put Block, Put Block List, Write Pages eller Clear Pages. Annars kan du skada den krypterade blobben och göra den oläslig.
> * För tabeller finns det ett liknande villkor. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera krypteringsmetadata.
> * Om du anger metadata på den krypterade blobben kan du skriva över de krypteringsrelaterade metadata som krävs för dekryptering, eftersom inställningen av metadata inte är additiv. Detta gäller även för ögonblicksbilder. undvika att ange metadata när du skapar en ögonblicksbild av en krypterad blob. Om metadata måste anges måste du anropa **get_blob_metadata** metoden först för att hämta de aktuella krypteringsmetadata och undvika samtidiga skrivningar medan metadata ställs in.
> * Aktivera **flaggan require_encryption** på tjänstobjektet för användare som bara ska arbeta med krypterade data. Se nedan för mer info.

Lagringsklientbiblioteket förväntar sig att den angivna KEK- och nyckelmatchningen implementerar följande gränssnitt. [Azure Key Vault-stöd](https://azure.microsoft.com/services/key-vault/) för Python KEK-hantering väntar och kommer att integreras i det här biblioteket när det är klart.

## <a name="client-api--interface"></a>Klient-API / Gränssnitt
När ett lagringstjänstobjekt (dvs. blockblobservice) har skapats kan användaren tilldela värden till de fält som utgör en krypteringsprincip: key_encryption_key, key_resolver_function och require_encryption. Användare kan bara tillhandahålla en KEK, bara en resolver eller båda. key_encryption_key är den grundläggande nyckeltyp som identifieras med hjälp av en nyckelidentifierare och som ger logik för omslag/uppackning. key_resolver_function används för att matcha en nyckel under dekrypteringsprocessen. Den returnerar en giltig KEK som har fått en nyckelidentifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

KEK måste implementera följande metoder för att kryptera data:

* wrap_key(cek): Radbryts den angivna CEK (byte) med hjälp av en algoritm som användaren väljer. Returnerar den raderade nyckeln.
* get_key_wrap_algorithm(): Returnerar den algoritm som används för att radbrytas.
* get_kid(): Returnerar strängnyckel-ID för kek.get_kid(): Returnerar strängnyckel-ID för den här KEK:en.
  KEK måste implementera följande metoder för att dekryptera data:
* unwrap_key(cek, algoritm): Returnerar den oförpackade formen för den angivna CEK med hjälp av den strängspecificerade algoritmen.
* get_kid(): Returnerar ett strängnyckel-ID för kek.get_kid(): Returnerar ett strängnyckel-ID för den här KEK:et.

Nyckelmatchningsorden måste åtminstone implementera en metod som, med tanke på ett nyckel-ID, returnerar motsvarande KEK som implementerar gränssnittet ovan. Endast den här metoden ska tilldelas egenskapen key_resolver_function på serviceobjektet.

* För kryptering används nyckeln alltid och frånvaron av en nyckel resulterar i ett fel.
* För dekryptering:

  * Nyckelmatchningsorden anropas om det anges för att hämta nyckeln. Om matcharen har angetts men inte har någon mappning för nyckelidentifieraren genereras ett fel.
  * Om matcharen inte har angetts men en nyckel anges används nyckeln om dess identifierare matchar den nödvändiga nyckelidentifieraren. Om identifieraren inte matchar genereras ett fel.

    Krypteringsexexemplen i azure.storage.samples visar ett mer detaljerat end-to-end-scenario för blobbar, köer och tabeller.
      Exempel implementeringar av KEK och nyckelmatchningsordtavlan finns i exempelfilerna som KeyWrapper respektive KeyResolver.

### <a name="requireencryption-mode"></a>Krävkrypningsläge
Användare kan eventuellt aktivera ett driftsläge där alla uppladdningar och nedladdningar måste krypteras. I det här läget misslyckas försök att ladda upp data utan en krypteringsprincip eller hämta data som inte är krypterade på tjänsten på klienten. Flaggan **require_encryption** på tjänstobjektet styr detta.

### <a name="blob-service-encryption"></a>Kryptering av Blob-tjänst
Ange krypteringsprincipfälten för blockblobtjänstobjektet. Allt annat kommer att hanteras av klientbiblioteket internt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Kryptering av kötjänst
Ange krypteringsprincipfälten i kötjänstobjektet. Allt annat kommer att hanteras av klientbiblioteket internt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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

### <a name="table-service-encryption"></a>Kryptering av tabelltjänst
Förutom att skapa en krypteringsprincip och ange den för alternativ för begäran måste du antingen ange en **encryption_resolver_function** på **tableservice**eller ange attributet encrypt på EntityProperty.

### <a name="using-the-resolver"></a>Använda resolver

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan kan en egenskap markeras för kryptering genom att lagra den i ett EntityProperty-objekt och ställa in krypterarfältet.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Kryptering och prestanda
Observera att om du krypterar lagringsdata får du ytterligare prestandakostnader. Innehållsnyckeln och IV måste genereras, själva innehållet måste krypteras och ytterligare metadata måste formateras och överföras. Denna omkostnader varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testar sina program för prestanda under utveckling.

## <a name="next-steps"></a>Nästa steg
* Ladda ned [Azure Storage Client Library för Java PyPi-paketet](https://pypi.python.org/pypi/azure-storage)
* Ladda ned [Azure Storage Client Library for Python-källkoden från GitHub](https://github.com/Azure/azure-storage-python)
