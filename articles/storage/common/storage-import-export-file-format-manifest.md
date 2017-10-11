---
title: Azure Import/Export manifestfilen format | Microsoft Docs
description: "Lär dig mer om formatet för enheten manifestfilen som beskriver mappningen mellan blobbar i Azure Blob storage och filer på en enhet i en import eller exportera jobb i tjänsten Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure Import/Export service manifest filformat
Enheten manifestfilen beskriver mappningen mellan blobbar i Azure Blob storage och filer på enheten som består av ett jobb som importeras eller exporteras. För en importen manifestfilen skapas som en del av enheten förberedelseprocessen och lagras på enheten innan enheten skickas till Azure-datacentret. När du exporterar, manifestet skapas och lagras på enheten av tjänsten Azure Import/Export.  
  
För både importera och exportera jobben lagras manifestfilen enhet på import- eller enhet; den kan inte överföras till tjänsten via API-åtgärd.  
  
Följande beskriver allmänna formatet för en manifestfil på enheten:  
  
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

## <a name="manifest-xml-elements-and-attributes"></a>Manifestets XML-element och attribut

Dataelement och attribut för manifest-XML-format för enheten har angetts i följande tabell.  
  
|XML-Element|Typ|Beskrivning|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Rotelementet|Rotelementet för manifestfilen. Alla andra element i filen är under det här elementet.|  
|`Version`|Attributet sträng|Versionen av manifestfilen.|  
|`Drive`|Kapslade XML-element|Innehåller manifestet för varje enhet.|  
|`DriveId`|Sträng|Unikt enheten identifierare för enheten. Identifierare för enheten hittas genom att fråga enheten för dess serienummer. Serienumret för enheten skrivs vanligtvis på enheten samt utanför. Den `DriveID` elementet måste finnas före alla `BlobList` element i manifestfilen.|  
|`StorageAccountKey`|Sträng|Krävs för import jobb om och bara om `ContainerSas` har inte angetts. Kontonyckel för Azure storage-konto som är associerat med jobbet.<br /><br /> Det här elementet har utelämnats från manifestet för exporten.|  
|`ContainerSas`|Sträng|Krävs för import jobb om och bara om `StorageAccountKey` har inte angetts. Behållaren SAS för att komma åt blobar som är associerat med jobbet. Se [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) för dess format. Det här elementet har utelämnats från manifestet för exporten.|  
|`ClientCreator`|Sträng|Anger klienten som skapade XML-filen. Det här värdet är inte tolkas av tjänsten Import/Export.|  
|`BlobList`|Kapslade XML-element|Innehåller en lista över blobbar som ingår i att importera eller exportera jobb. Varje blobb i en blob-lista delar samma metadata och egenskaper.|  
|`BlobList/MetadataPath`|Sträng|Valfri. Anger den relativa sökvägen för en fil på disken som innehåller de metadata som standard som ställs in på blobbar i blob-listan för en importåtgärd. Dessa metadata kan åsidosättas på grundval av blob av blob.<br /><br /> Det här elementet har utelämnats från manifestet för exporten.|  
|`BlobList/MetadataPath/@Hash`|Attributet sträng|Anger Base16-kodade MD5 hash-värdet för metadatafilen.|  
|`BlobList/PropertiesPath`|Sträng|Valfri. Anger den relativa sökvägen för en fil på disken som innehåller standardegenskaper som ställs in på blobbar i blob-listan för en importåtgärd. De här egenskaperna kan åsidosättas på grundval av blob av blob.<br /><br /> Det här elementet har utelämnats från manifestet för exporten.|  
|`BlobList/PropertiesPath/@Hash`|Attributet sträng|Anger Base16-kodade MD5 hash-värdet för filen egenskaper.|  
|`Blob`|Kapslade XML-element|Innehåller information om varje blobb i varje blob-lista.|  
|`Blob/BlobPath`|Sträng|Relativ URI till blob, från och med behållarens namn. Om blob finns i Rotbehållare, det måste börja med `$root`.|  
|`Blob/FilePath`|Sträng|Anger den relativa sökvägen till filen på enheten. För exportjobb, ska blobbsökvägen användas för sökvägen till filen om möjligt. *t.ex.*, `pictures/bob/wild/desert.jpg` kommer att exporteras till `\pictures\bob\wild\desert.jpg`. På grund av begränsningar av NTFS-namn kan dock en blob exporteras till en fil med en sökväg som inte liknar blob-sökväg.|  
|`Blob/ClientData`|Sträng|Valfri. Innehåller kommentarer från kunden. Det här värdet är inte tolkas av tjänsten Import/Export.|  
|`Blob/Snapshot`|Datum och tid|Valfritt för exportjobb. Anger identifieraren för ögonblicksbild för en ögonblicksbild av exporterade blob.|  
|`Blob/Length`|Integer|Anger den totala längden på blob i byte. Värdet kan vara upp till 200 GB för en blockblob och upp till 1 TB för en sidblobb. Det här värdet måste vara delbar med 512 för en sidblobb.|  
|`Blob/ImportDisposition`|Sträng|Valfritt för importjobb utelämnas för exportjobb. Detta anger hur tjänsten Import/Export ska hantera fallet för ett importjobb där en blob med samma namn redan finns. Om det här värdet utelämnas från manifestet importera standardvärdet är `rename`.<br /><br /> Värden för det här elementet är:<br /><br /> -   `no-overwrite`: Om det finns redan en mål-blob med samma namn, importen ska hoppa över att importera den här filen.<br />-   `overwrite`: Alla befintliga mål-blob skrivs över helt av nyligen importerade filen.<br />-   `rename`: Ny blob kommer upp med ett ändrat namn.<br /><br /> Byta namn på regeln är följande:<br /><br /> -Om blobbnamnet inte innehåller en punkt, skapas ett nytt namn genom att lägga till `(2)` till det ursprungliga blobbnamnet, om det här nya namnet också står i konflikt med blobbnamnet på en befintlig sedan `(3)` läggs i stället för `(2)`; och så vidare.<br />-Om blob-namnet innehåller en punkt, anses del efter den sista punkten namnet. Liknar proceduren ovan `(2)` infogas innan den senaste punkten att generera ett nytt namn, om de nya namnet fortfarande står i konflikterna med en befintlig blob-namnet och sedan tjänsten försöker `(3)`, `(4)`och så vidare tills ett namn som inte orsakar konflikter hittas.<br /><br /> Några exempel:<br /><br /> Blob `BlobNameWithoutDot` får namnet:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Blob `Seattle.jpg` får namnet:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Kapslade XML-element|Krävs för en sidblobb.<br /><br /> För en import anger åtgärden, du en lista med byte-intervall för en fil som ska importeras. Varje sidintervall beskrivs av en förskjutning och längd i källfilen som beskriver sidintervall tillsammans med en MD5-hash för regionen. Den `Hash` attribut för ett sidintervall krävs. Tjänsten kommer att verifiera att data i blob hash matchar beräknade MD5-hash från sidintervallet. Valfritt antal sidintervall kan användas för att beskriva en fil för en import, med totalen storleken upp till 1 TB. Alla sidintervall måste vara sorterade efter förskjutningen och utan överlappningar tillåts.<br /><br /> Åtgärden anger en uppsättning med byte-intervall för en blob som har exporterats till enheten för export.<br /><br /> Sidor kan tillsammans omfatta endast underintervall för en blob eller fil.  Den återstående delen av filen som inte omfattas av några sidintervall är förväntat och dess innehåll kan vara odefinierat.|  
|`PageRange`|XML-element|Representerar ett sidintervall.|  
|`PageRange/@Offset`|Attributet heltal|Anger förskjutningen i filen överföring och blob där ett angivet intervall börjar. Det här värdet måste vara delbar med 512.|  
|`PageRange/@Length`|Attributet heltal|Anger längden på sidintervallet. Det här värdet måste vara delbar med 512 och högst 4 MB.|  
|`PageRange/@Hash`|Attributet sträng|Anger Base16-kodade MD5 hash-värdet för sidintervallet.|  
|`BlockList`|Kapslade XML-element|Krävs för en blockblobb med namngivna block.<br /><br /> En importen anger blockeringslistan en uppsättning block som ska importeras till Azure Storage. Lista över blockerade anger en exportåtgärden, där varje block har lagrats i filen på disken för export. Varje block beskrivs av en förskjutning i filen och en Blockets längd; varje block dessutom namnges av ett block-ID-attribut och innehåller en MD5-hash för blocket. Upp till 50 000 block kan användas för att beskriva en blob.  Alla block måste beställas av Förskjutning, tillsammans med bör omfatta vilka filen *d.v.s.*, det måste finnas inget mellanrum mellan block. Om blob är mer än 64 MB, måste block-ID: N för varje block vara antingen alla saknade eller alla finns. Block-ID: N måste vara Base64-kodade strängar. Se [placera Block](/rest/api/storageservices/put-block) ytterligare krav för block-ID: N.|  
|`Block`|XML-element|Representerar ett block.|  
|`Block/@Offset`|Attributet heltal|Anger förskjutningen där det angivna blocket börjar.|  
|`Block/@Length`|Attributet heltal|Anger antalet byte i blocket; Det här värdet måste vara fler än 4MB.|  
|`Block/@Id`|Attributet sträng|Anger en sträng som representerar block-ID för blocket.|  
|`Block/@Hash`|Attributet sträng|Anger Base16-kodade MD5-hash av blocket.|  
|`Blob/MetadataPath`|Sträng|Valfri. Anger den relativa sökvägen för en metadatafil. Under en import är metadata inställd på mål-blob. När du exporterar lagras blobens metadata i metadatafilen på enheten.|  
|`Blob/MetadataPath/@Hash`|Attributet sträng|Anger Base16-kodade MD5-hash för det blob-metadatafil.|  
|`Blob/PropertiesPath`|Sträng|Valfri. Anger den relativa sökvägen till en egenskapsfil. Under en import ange egenskaper på mål-blob. När du exporterar lagras blob-egenskaper i filen egenskaper på enheten.|  
|`Blob/PropertiesPath/@Hash`|Attributet sträng|Anger Base16-kodade MD5-hash för det blob egenskapsfil.|  
  
## <a name="next-steps"></a>Nästa steg
 
* [REST-API för Storage Import/Export](/rest/api/storageimportexport/)
