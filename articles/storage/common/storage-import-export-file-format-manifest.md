---
title: Manifestfilformat för Azure Import/Export | Microsoft Docs
description: Läs mer om formatet för enheten manifestfilen som beskriver mappningen mellan blobar i Azure Blob storage och filer på en enhet i en import eller export-jobb i Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 920f350ab5ba1e9e1703ffcc32dc8c7153624c0b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525162"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure Import/Export-tjänstens manifestfilformat
Manifestfilen enhet beskriver mappningen mellan i Azure Blob storage-blobbar och filer på enheten som består av ett import eller export-jobb. Manifestfilen för en import-åtgärd har skapats som en del av processen för att förbereda enheten och lagras på enheten innan enheten skickas till Azure-datacentret. När du exporterar, manifestet skapas och lagras på enheten av tjänsten Azure Import/Export.  
  
För både importera och exportera jobb lagras manifestfilen enhet på import eller export enhet; den kan inte överföras till tjänsten via alla API-åtgärder.  
  
Nedan beskrivs det allmänna formatet för en enhet manifestfil:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Manifest XML-element och attribut

Dataelement och attribut för manifest-XML-format för enheten har angetts i tabellen nedan.  
  
|XML-Element|Typ|Beskrivning|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Rotelement|Rotelementet i manifestfilen. Alla andra element i filen är under det här elementet.|  
|`Version`|Attribut, sträng|Versionen av manifestfilen.|  
|`Drive`|Kapslade XML-element|Innehåller manifestet för varje enhet.|  
|`DriveId`|Sträng|Den unika Enhetsidentifieraren för enheten. Enhetsidentifieraren hittas genom att fråga enheten där dess serienummer. Serienumret för enheten skrivs vanligtvis på utsidan samt på enheten. Den `DriveID` elementet måste finnas innan någon `BlobList` element i manifestfilen.|  
|`StorageAccountKey`|Sträng|Krävs för import jobb om och endast om `ContainerSas` har inte angetts. Kontonyckel för Azure storage-kontot som är associerad med jobbet.<br /><br /> Det här elementet har utelämnats från manifestet för en export.|  
|`ContainerSas`|Sträng|Krävs för import jobb om och endast om `StorageAccountKey` har inte angetts. Behållare SAS för åtkomst till blobbarna som är associerat med jobbet. Se [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) för dess format. Det här elementet har utelämnats från manifestet för en export.|  
|`ClientCreator`|Sträng|Anger klienten som skapade XML-filen. Det här värdet tolkas inte av Import/Export-tjänsten.|  
|`BlobList`|Kapslade XML-element|Innehåller en lista över blobar som är en del av importera eller exportera jobbet. Varje blob i en blob-lista delar samma metadata och egenskapers.|  
|`BlobList/MetadataPath`|Sträng|Valfri. Anger den relativa sökvägen för en fil på disken som innehåller de metadata som standard som kan ställas in för blobbar i blob-listan för en importåtgärd. Dessa metadata kan åsidosättas på basis av blob av blob.<br /><br /> Det här elementet har utelämnats från manifestet för en export.|  
|`BlobList/MetadataPath/@Hash`|Attribut, sträng|Anger värdet för Base16-kodad MD5-hash för metadatafilen.|  
|`BlobList/PropertiesPath`|Sträng|Valfri. Anger den relativa sökvägen för en fil på disken som innehåller de standardegenskaper som kan ställas in för blobbar i blob-listan för en importåtgärd. De här egenskaperna kan åsidosättas på basis av blob av blob.<br /><br /> Det här elementet har utelämnats från manifestet för en export.|  
|`BlobList/PropertiesPath/@Hash`|Attribut, sträng|Anger Base16-kodad MD5-hash-värde för filen egenskaper.|  
|`Blob`|Kapslade XML-element|Innehåller information om varje blob i varje blob-lista.|  
|`Blob/BlobPath`|Sträng|Relativ URI till bloben från och med behållarens namn. Om blobben finns i Rotbehållare, det måste börja med `$root`.|  
|`Blob/FilePath`|Sträng|Anger den relativa sökvägen till filen på enheten. För export-jobb, ska blobbsökvägen användas för sökvägen om möjligt. *t.ex.*, `pictures/bob/wild/desert.jpg` kommer att exporteras till `\pictures\bob\wild\desert.jpg`. En blob kan dock på grund av begränsningar av NTFS som exporteras till en fil med en sökväg som inte liknar den blobbsökvägen.|  
|`Blob/ClientData`|Sträng|Valfri. Innehåller kommentarer från kunden. Det här värdet tolkas inte av Import/Export-tjänsten.|  
|`Blob/Snapshot`|DateTime|Valfritt av export-jobb. Anger identifieraren för ögonblicksbild för en exporterade blobögonblicksbild.|  
|`Blob/Length`|Integer|Anger den totala längden på blob i byte. Värdet kan vara upp till 200 GB för en blockblob och upp till 1 TB för en sidblob. Det här värdet måste vara en multipel av 512 för en sidblob.|  
|`Blob/ImportDisposition`|Sträng|Valfritt för importjobb som utelämnats av export-jobb. Detta anger hur Import/Export-tjänsten ska hantera fallet för ett importjobb där en blob med samma namn finns redan. Om det här värdet utelämnas från import manifestet, standardvärdet är `rename`.<br /><br /> Värden för det här elementet är:<br /><br /> -   `no-overwrite`: Om det finns redan en mål-blob med samma namn, kommer importen hoppa över importera den här filen.<br />-   `overwrite`: Alla befintliga målblobben skrivs över helt av den nyligen importerade filen.<br />-   `rename`: Den nya bloben kommer överföras med ett ändrade namn.<br /><br /> Byta namn på regeln är följande:<br /><br /> -Om blobnamnet inte innehåller en punkt, skapas ett nytt namn genom att lägga till `(2)` till den ursprungliga blob-name; om det här nya namnet också står i konflikt med ett befintligt blobnamn, sedan `(3)` läggs i stället för `(2)`; och så vidare.<br />-Om blobbnamnet innehåller en punkt, betraktas den del som följer den senaste punkten tilläggets namn. Liknar proceduren ovan `(2)` infogas innan den senaste punkten att generera ett nytt namn, om de nya namnet fortfarande står i konflikterna med en befintlig blob-namnet och sedan tjänsten försöker `(3)`, `(4)`och så vidare tills ett icke motstridiga namn hittas.<br /><br /> Några exempel:<br /><br /> Blobben `BlobNameWithoutDot` namn till:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Blobben `Seattle.jpg` namn till:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Kapslade XML-element|Krävs för en sidblob.<br /><br /> För en import-åtgärden, anger en lista med byte-intervall för en fil som ska importeras. Varje sidintervall beskrivs av en förskjutning och längd i källfilen som beskriver de sidor som, tillsammans med en MD5-hash för regionen. Den `Hash` attribut för ett sidintervall krävs. Tjänsten verifierar att hash för data i bloben matchar den beräknade MD5-hashen från sidintervallet. Valfritt antal sidor kan användas för att beskriva en fil för en import, med totalen storleken på upp till 1 TB. Alla sidintervall måste vara sorterade efter förskjutningen och utan överlappningar tillåts.<br /><br /> För en export anger åtgärden, en uppsättning byte-intervall för en blob som har exporterats till enheten.<br /><br /> Sidor kan tillsammans omfatta endast underordnade intervallen för en blob eller fillagring.  Den återstående delen av filen som inte omfattas av alla sidintervall förväntas och dess innehåll kan vara odefinierad.|  
|`PageRange`|XML-element|Representerar ett sidintervall.|  
|`PageRange/@Offset`|Integer-attribut|Anger förskjutningen i överföringen fil- och blob där ett angivet intervall börjar. Det här värdet måste vara en multipel av 512.|  
|`PageRange/@Length`|Integer-attribut|Anger hur lång sidintervallet. Det här värdet måste vara en multipel av 512 och mer än 4 MB.|  
|`PageRange/@Hash`|Attribut, sträng|Anger värdet för Base16-kodad MD5-hash för sidintervallet.|  
|`BlockList`|Kapslade XML-element|Krävs för en blockblob med namngivna blocken.<br /><br /> För en import-åtgärd anger i blockeringslistan en uppsättning av block som ska importeras till Azure Storage. För en export anger blockeringslistan där varje block har lagrats i filen på disken för export. Varje block beskrivs av en förskjutning i filen och en Blockets längd; varje block vidare med namnet av ett block-ID-attribut och innehåller en MD5-hash för blocket. Upp till 50 000 block kan användas för att beskriva en blob.  Alla block måste beställas av Förskjutning, och tillsammans ska täcka hela utbudet av filen *d.v.s.*, det måste vara Ingen klyftan mellan block. Om blobben som är mer än 64 MB, måste block-ID: N för varje block vara antingen alla saknade eller alla finns. Block-ID: N måste vara Base64-kodade strängar. Se [placera Block](/rest/api/storageservices/put-block) för ytterligare krav för block-ID: N.|  
|`Block`|XML-element|Representerar ett block.|  
|`Block/@Offset`|Integer-attribut|Anger förskjutningen där det angivna blocket börjar.|  
|`Block/@Length`|Integer-attribut|Anger antalet byte i blocket; Det här värdet måste vara mer än 4MB.|  
|`Block/@Id`|Attribut, sträng|Anger en sträng som representerar block-ID för blocket.|  
|`Block/@Hash`|Attribut, sträng|Anger Base16-kodad MD5-hashen för blocket.|  
|`Blob/MetadataPath`|Sträng|Valfri. Anger den relativa sökvägen för en metadatafil. Metadata har angetts för målblobben under en import. När du exporterar lagras i blob-metadata i metadatafilen på enheten.|  
|`Blob/MetadataPath/@Hash`|Attribut, sträng|Anger Base16-kodad MD5-hash för den blob-metadatafil.|  
|`Blob/PropertiesPath`|Sträng|Valfri. Anger den relativa sökvägen för filsegment egenskaper. Egenskaperna är inställda på målblobben under en import. När du exporterar lagras blobegenskaper i filen egenskaper på enheten.|  
|`Blob/PropertiesPath/@Hash`|Attribut, sträng|Anger Base16-kodad MD5-hashen för blobfil egenskaper.|  
  
## <a name="next-steps"></a>Nästa steg
 
* [REST-API för Storage Import/Export](/rest/api/storageimportexport/)
