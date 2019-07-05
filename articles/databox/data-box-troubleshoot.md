---
title: Felsöka problem med din Azure Data Box genom Azure Data Box tung | Microsoft Docs
description: Beskriver hur du felsöker problem som kan uppstå i Azure Data Box och Azure Data Box tunga när du kopierar data till dessa enheter.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439400"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Felsöka problem med Azure Data Box och Azure Data Box tunga

Den här artikeln innehåller information om hur du felsöker problem uppstå när du använder Azure Data Box eller Azure Data Box tung. Artikeln innehåller listan över möjliga fel som visas när data kopieras till Data Box eller när data har överförts från Data Box.

## <a name="error-classes"></a>Felklasser

Fel i Data Box och Data Box tunga kan sammanfattas på följande sätt:

| Fel kategori *        | Beskrivning        | Rekommenderad åtgärd    |
|----------------------------------------------|---------|--------------------------------------|
| Namn på behållare eller filresurs | Behållare eller resurs som följer inte Azure namnregler.  |Hämta fel-listor. <br> Byt namn på behållare och filresurser. [Läs mer](#container-or-share-name-errors).  |
| Storleksgräns för behållare eller filresurs | Den totala mängden data i behållare eller resurser överskrider gränsen för Azure.   |Hämta fel-listor. <br> Minska den övergripande data i behållare eller filresurs. [Läs mer](#container-or-share-size-limit-errors).|
| Storleksgräns för objekt eller en fil | Objektet eller filer i behållare eller resurser överskrider gränsen för Azure.|Hämta fel-listor. <br> Minska filstorleken i behållare eller filresurs. [Läs mer](#object-or-file-size-limit-errors). |    
| Data eller filtyp | Filtypen eller dataformatet stöds inte. |Hämta fel-listor. <br> Kontrollera att data är 512 byte justerad och kopieras till mapparna skapats i förväg för sidblobar eller hanterade diskar. [Läs mer](#data-or-file-type-errors). |
| Icke-kritiska fel för blob eller fillagring  | Blob eller fillagring namnen följer inte Azure namnregler eller att filtypen stöds inte. | Dessa blob eller filer får inte kopieras eller namnen kan ändras. [Lär dig hur du åtgärdar de här felen](#non-critical-blob-or-file-errors). |

\* De första fyra felkategorier är kritiska fel och måste åtgärdas innan du går till Förbered för att skicka.


## <a name="container-or-share-name-errors"></a>Behållare eller filresurs namngivningsfel

Det här är fel som rör behållare och dela namn.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Felbeskrivning:** Namnet på containern eller resursen måste innehålla mellan 3 och 63 tecken. 

**Föreslagen lösning:** Mappen under rutan Data eller Data Box tung share(SMB/NFS) som du har kopierat data som blir en Azure-behållare i ditt lagringskonto. 

- På den **Anslut och kopiera** i enhetens lokala webbgränssnitt, ladda ned och granska felfiler att identifiera mappen namn med problem.
- Ändra namnet på mappen under rutan Data eller Data Box tunga resursen att se till att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namn får inte innehålla flera bindestreck i rad.
    - Exempel på giltiga namn: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Mer information finns i Azure namnkonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resursnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Felbeskrivning:** Namnet på containern eller resursen måste bestå av endast bokstäver, siffror eller bindestreck.

**Föreslagen lösning:** Mappen under rutan Data eller Data Box tung share(SMB/NFS) som du har kopierat data som blir en Azure-behållare i ditt lagringskonto. 

- På den **Anslut och kopiera** i enhetens lokala webbgränssnitt, ladda ned och granska felfiler att identifiera mappen namn med problem.
- Ändra namnet på mappen under rutan Data eller Data Box tunga resursen att se till att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namn får inte innehålla flera bindestreck i rad.
    - Exempel på giltiga namn: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Mer information finns i Azure namnkonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resursnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Felbeskrivning:** Namn på behållare och resursnamn får inte börja eller sluta med bindestreck och får inte innehålla flera bindestreck i rad.

**Föreslagen lösning:** Mappen under rutan Data eller Data Box tung share(SMB/NFS) som du har kopierat data som blir en Azure-behållare i ditt lagringskonto. 

- På den **Anslut och kopiera** i enhetens lokala webbgränssnitt, ladda ned och granska felfiler att identifiera mappen namn med problem.
- Ändra namnet på mappen under rutan Data eller Data Box tunga resursen att se till att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namn får inte innehålla flera bindestreck i rad.
    - Exempel på giltiga namn: `my-folder-1`, `my-really-extra-long-folder-111`
    - Exempel på namn som inte är giltiga: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Mer information finns i Azure namnkonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [resursnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Behållare eller filresurs storlek gränsen fel

Det här är fel som rör data som överskrider storleken på data som tillåts i en behållare eller en resurs.

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Felbeskrivning:** Azure-filresurs begränsar en resurs till 5 TB data. Den här gränsen har överskridits för vissa resurser.

**Föreslagen lösning:** På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.

Identifiera de mappar som har det här problemet från felloggarna och kontrollera att filerna i mappen är under 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Fel i gränsen storlek objekt eller en fil

Det här är fel som rör data som överskrider den maximala storleken för objektet eller den fil som är tillåtet i Azure. 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Felbeskrivning:** Filstorleken överskrider den maximala filstorleken för uppladdning.

**Föreslagen lösning:** Blob eller filstorleken överskrider maxgränsen som tillåts för överföring.

- På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
- Se till att blob- och storlekar inte överskrider storleksgränserna Azure-objekt.

## <a name="data-or-file-type-errors"></a>Felaktiga data eller en fil

Det här är fel som rör filtypen eller datatyp hittades i behållare eller filresurs. 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Felbeskrivning:** Blobben eller filen är felaktigt justerad.

**Föreslagen lösning:** Sidan blob-resurs på Data Box eller Data Box tung endast har stöd för filer som är 512 byte justerade (till exempel VHD-/ VHDX). Alla data som kopierats till sidan blob resursen har överförts till Azure som sidblobar.

Ta bort alla icke-VHD/VHDX-data från sidan blob-resursen. Du kan använda resurser för blockblob eller Azure files för allmänna data.

Mer information finns i [översikt av sidblobbar](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Felbeskrivning:** En filtyp finns i en hanterad disk-resurs. Endast fasta virtuella hårddiskar är tillåtna.

**Föreslagen lösning:**

- Kontrollera att du bara ladda upp de fasta virtuella hårddiskarna för att skapa hanterade diskar.
- VHDX-filer eller **dynamisk** och **differentierande** VHD: er stöds inte.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Felbeskrivning:** En katalog är inte tillåtet i någon av de befintliga mapparna för hanterade diskar. Endast fasta virtuella hårddiskar är tillåtna i dessa mappar.

**Föreslagen lösning:** För hanterade diskar i varje resurs skapas följande tre mappar som motsvarar en behållare i ditt storage-konto: Premium SSD, Standard HDD och SSD som Standard. Dessa mappar motsvarar prestandanivå för den hantera disken.

- Kontrollera att du kopierar dina sidan blob-data (VHD) i någon av dessa befintliga mappar.
- En mapp eller katalog tillåts inte i dessa befintliga mappar. Ta bort de mappar som du har skapat i de befintliga mapparna.

Mer information finns i [kopia till managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

**Felbeskrivning:** Symboliska länkar är inte tillåtna i Linux. 

**Föreslagen lösning:** Symboliska länkar är vanligtvis länkar, pipes och andra sådana filer. Ta bort länkar, eller matcha länken och kopiera data.


## <a name="non-critical-blob-or-file-errors"></a>Icke-kritiska fel för blob eller fillagring

Alla fel som visas vid kopiering av data sammanfattas i följande avsnitt.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Felbeskrivning:** Blob eller fillagring får innehålla kontrolltecken stöds inte.

**Föreslagen lösning:** Blobarna eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
Ta bort eller byta namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure namnkonventionerna för [blob namn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Felbeskrivning:** Blob eller fillagring namnen innehåller ogiltiga tecken.

**Föreslagen lösning:** Blobarna eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
Ta bort eller byta namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure namnkonventionerna för [blob namn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Felbeskrivning:** De blob eller fillagring som slutar med felaktiga tecken.

**Föreslagen lösning:** Blobarna eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
Ta bort eller byta namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure namnkonventionerna för [blob namn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Felbeskrivning:** Blob eller filnamnet innehåller för många sökvägssegment.

**Föreslagen lösning:** Blobarna eller de filer som du har kopierat överskrider det maximala antalet segment för resurssökväg. Ett segment är en sträng mellan på varandra följande avgränsartecken, till exempel snedstreck /.

- På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
- Se till att den [blob namn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) följer namngivningskonventionerna Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Felbeskrivning:** Namnet på blobben eller filen är för långt.

**Föreslagen lösning:** Blob eller filnamnen överskrider den maximala längden.

- På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
- Blob-namnet får innehålla högst 1 024 tecken.
- Ta bort eller byta namn på blob eller filer så att namnen inte vara längre än 1024 tecken.

Mer information finns i Azure namnkonventionerna för blob och filnamn.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Felbeskrivning:** Ett av segmenten i blobb- eller filnamnet är för långt.

**Föreslagen lösning:** En av segment för resurssökväg i blob- eller överskrider det högsta antalet tecken. Ett segment är en sträng mellan på varandra följande avgränsartecken, till exempel snedstreck /.

- På den **Anslut och kopiera** sida av det lokala webbgränssnittet ladda ned och granska fel.
- Se till att den [blob namn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) följer namngivningskonventionerna Azure.


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Felbeskrivning:** Felaktig behållarnamn har angetts för hanterad diskresurser.

**Föreslagen lösning:** För hanterade diskar i varje resurs skapas följande mappar som motsvarar en behållare i ditt storage-konto: Premium SSD, Standard HDD och SSD som Standard. Dessa mappar motsvarar prestandanivå för den hantera disken.

- Kontrollera att du kopierar dina sidan blob-data (VHD) i någon av dessa befintliga mappar. Endast data från dessa behållare överförs till Azure.
- En annan mapp som skapas på samma nivå som Premium SSD och HDD-Standard Standard SSD motsvarar inte en giltig prestandanivån och kan inte användas.
- Ta bort filer eller mappar som skapas utanför prestandanivåer.

Mer information finns i [kopia till managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [Data Box Blob storage-systemkrav](data-box-system-requirements-rest.md).
