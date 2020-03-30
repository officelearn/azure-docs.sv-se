---
title: Felsöka problem i din Azure Data Box, Azure Data Box Heavy
description: Beskriver hur du felsöker problem som visas i Azure Data Box och Azure Data Box Heavy när du kopierar data till dessa enheter.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560073"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Felsöka problem relaterade till Azure Data Box och Azure Data Box Heavy

I den här artikeln beskrivs information om hur du felsöker problem som kan visas när du använder Azure Data Box eller Azure Data Box Heavy. Artikeln innehåller en lista över möjliga fel som visas när data kopieras till datarutan eller när data överförs från Data Box.

## <a name="error-classes"></a>Felklasser

Felen i Data Box och Data Box Heavy sammanfattas på följande sätt:

| Felkategori*        | Beskrivning        | Rekommenderad åtgärd    |
|----------------------------------------------|---------|--------------------------------------|
| Behållar- eller delningsnamn | Behållar- eller resursnamn följer inte Azure-namngivningsreglerna.  |Hämta fellistorna. <br> Byt namn på behållarna eller resurserna. [Läs mer](#container-or-share-name-errors).  |
| Storleksgräns för behållare eller resurs | Den totala informationen i behållare eller resurser överskrider Azure-gränsen.   |Hämta fellistorna. <br> Minska de övergripande data i behållaren eller resursen. [Läs mer](#container-or-share-size-limit-errors).|
| Storleksgräns för objekt eller fil | Objektet eller filerna i behållare eller resurser överskrider Azure-gränsen.|Hämta fellistorna. <br> Minska filstorleken i behållaren eller resursen. [Läs mer](#object-or-file-size-limit-errors). |    
| Data eller filtyp | Dataformatet eller filtypen stöds inte. |Hämta fellistorna. <br> För sidblobar eller hanterade diskar kontrollerar du att data är 512 byte justerade och kopierade till de förskapade mapparna. [Läs mer](#data-or-file-type-errors). |
| Icke-kritiska blob- eller filfel  | Blob- eller filnamnen följer inte Azure-namngivningsreglerna eller så stöds inte filtypen. | Dessa blob eller filer får inte kopieras eller namnen kan ändras. [Läs om hur du åtgärdar dessa fel](#non-critical-blob-or-file-errors). |

\*De första fyra felkategorierna är kritiska fel och måste åtgärdas innan du kan fortsätta att förbereda leveransen.


## <a name="container-or-share-name-errors"></a>Fel i behållar- eller delningsnamn

Det här är fel som är relaterade till behållar- och delningsnamn.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Felbeskrivning:** Behållarens eller delningsnamnet måste vara mellan 3 och 63 tecken. 

**Föreslagen upplösning:** Mappen under datarutan eller datarutan Tung resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagringskonto. 

- På sidan **Anslut och kopiera** i enhetens lokala webbgränssnitt hämtar och granskar du felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under datarutan eller datarutan Tung resurs för att kontrollera att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namnen kan inte ha på varandra följande bindestreck.
    - Exempel på giltiga `my-folder-1`namn: ,`my-really-extra-long-folder-111`
    - Exempel på namn som inte `my-folder_1` `my`är `--myfolder` `myfolder--`giltiga: , , , ,`myfolder!`

    Mer information finns i Azure-namngivningskonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [delningsnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Felbeskrivning:** Behållarens eller resursnamnet får endast bestå av bokstäver, siffror eller bindestreck.

**Föreslagen upplösning:** Mappen under datarutan eller datarutan Tung resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagringskonto. 

- På sidan **Anslut och kopiera** i enhetens lokala webbgränssnitt hämtar och granskar du felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under datarutan eller datarutan Tung resurs för att kontrollera att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namnen kan inte ha på varandra följande bindestreck.
    - Exempel på giltiga `my-folder-1`namn: ,`my-really-extra-long-folder-111`
    - Exempel på namn som inte `my-folder_1` `my`är `--myfolder` `myfolder--`giltiga: , , , ,`myfolder!`

    Mer information finns i Azure-namngivningskonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [delningsnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Felbeskrivning:** Behållarnamnen och resursnamnen kan inte börja eller sluta med bindestreck och kan inte ha på varandra följande bindestreck.

**Föreslagen upplösning:** Mappen under datarutan eller datarutan Tung resurs (SMB/NFS) som du har kopierat data till blir en Azure-behållare i ditt lagringskonto. 

- På sidan **Anslut och kopiera** i enhetens lokala webbgränssnitt hämtar och granskar du felfilerna för att identifiera mappnamnen med problem.
- Ändra mappnamnet under datarutan eller datarutan Tung resurs för att kontrollera att:

    - Namnet har mellan 3 och 63 tecken.
    - Namnen kan bara ha bokstäver, siffror och bindestreck.
    - Namnen kan inte börja eller sluta med bindestreck.
    - Namnen kan inte ha på varandra följande bindestreck.
    - Exempel på giltiga `my-folder-1`namn: ,`my-really-extra-long-folder-111`
    - Exempel på namn som inte `my-folder_1` `my`är `--myfolder` `myfolder--`giltiga: , , , ,`myfolder!`

    Mer information finns i Azure-namngivningskonventionerna för [behållarnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) och [delningsnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Fel i behållare eller resursstorleksbegränsning

Det här är fel som är relaterade till data som överskrider storleken på data som tillåts i en behållare eller en resurs.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Felbeskrivning:** Azure-filresurs begränsar en resurs till 5 TB data. Denna gräns har överskridits för vissa aktier.

**Föreslagen upplösning:** Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.

Identifiera de mappar som har det här problemet från felloggarna och kontrollera att filerna i mappen är under 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Fel i objekt- eller filstorleksbegränsningen

Dessa är fel relaterade till data som överskrider den maximala storleken på objektet eller filen som tillåts i Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Felbeskrivning:** Filstorleken överskrider den maximala filstorleken för överföring.

**Föreslagen upplösning:** Blobben eller filstorlekarna överskrider den högsta tillåtna gränsen för överföring.

- Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
- Kontrollera att blob- och filstorlekarna inte överskrider storleksbegränsningarna för Azure-objektet.

## <a name="data-or-file-type-errors"></a>Data- eller filtypsfel

Det här är fel som är relaterade till filtyp eller datatyp som inte stöds i behållaren eller resursen. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Felbeskrivning:** Blob eller-filen är felaktigt justerad.

**Föreslagen upplösning:** Sidblolobens resurs på Data Box eller Data Box Heavy stöder endast filer som är 512 byte justerade (till exempel VHD/VHDX). Alla data som kopieras till sidblobbresursen överförs till Azure som sidblobar.

Ta bort alla data som inte är VHD/VHDX från sidblobbresursen. Du kan använda resurser för blockblob eller Azure-filer för allmänna data.

Mer information finns i [Översikt över sidblobar](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Felbeskrivning:** Det finns en filtyp som inte stöds i en hanterad diskresurs. Endast fasta virtuella hårddiskar är tillåtna.

**Föreslagen upplösning:**

- Se till att du bara laddar upp fasta virtuella hårddiskar för att skapa hanterade diskar.
- VHDX-filer eller **dynamiska** och **differentierande** virtuella hårddiskar stöds inte.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Felbeskrivning:** En katalog är inte tillåten i någon av de befintliga mapparna för de hanterade diskarna. Endast fasta virtuella hårddiskar är tillåtna i dessa mappar.

**Föreslagen upplösning:** För hanterade diskar skapas följande tre mappar inom varje resurs som motsvarar behållare i ditt lagringskonto: Premium SSD, Standard HDD och Standard SSD. Dessa mappar motsvarar prestandanivån för den hanterade disken.

- Kontrollera att du kopierar sidblobbdata (VHD) till en av dessa befintliga mappar.
- En mapp eller katalog är inte tillåten i dessa befintliga mappar. Ta bort alla mappar som du har skapat i de befintliga mapparna.

Mer information finns i [Kopiera till hanterade diskar](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Felbeskrivning:** Symboliska länkar är inte tillåtna i Linux. 

**Föreslagen upplösning:** De symboliska länkarna är vanligtvis länkar, rör och andra sådana filer. Ta antingen bort länkarna eller lösa länkarna och kopiera data.


## <a name="non-critical-blob-or-file-errors"></a>Icke-kritiska blob- eller filfel

Alla icke-kritiska fel som är relaterade till namn på blobbar, filer eller behållare som visas under datakopiering sammanfattas i följande avsnitt. Om dessa fel finns, kommer namnen att ändras för att överensstämma med Azure-namngivningskonventionerna. Motsvarande orderstatus för dataöverföring **slutförs med varningar**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Felbeskrivning:** Blob- eller filnamnen innehåller kontrolltecken som inte stöds.

**Föreslagen upplösning:** Blobbar eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
Ta bort eller byt namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure-namngivningskonventionerna för [blobnamn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Felbeskrivning:** Blob- eller filnamnen innehåller ogiltiga tecken.

**Föreslagen upplösning:** Blobbar eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
Ta bort eller byt namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure-namngivningskonventionerna för [blobnamn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Felbeskrivning:** Blob- eller filnamnen slutar med felaktiga tecken.

**Föreslagen upplösning:** Blobbar eller de filer som du har kopierat innehåller namn med tecken som inte stöds.

Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
Ta bort eller byt namn på filerna för att ta bort tecken som inte stöds.

Mer information finns i Azure-namngivningskonventionerna för [blobnamn](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamn](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Felbeskrivning:** Blob- eller filnamnet innehåller för många sökvägssegment.

**Föreslagen upplösning:** Blobbar eller de filer som du har kopierat överskrider det maximala antalet sökvägssegment. Ett bansegment är strängen mellan på varandra följande avgränsare, till exempel det främre snedstrecket /.

- Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
- Kontrollera att [blob-namnen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamnen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) överensstämmer med Azure-namngivningskonventioner.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Felbeskrivning:** Blob- eller filnamnet är för långt.

**Föreslagen upplösning:** Blob eller filnamnen överskrider den maximala längden.

- Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
- Blob-namnet får inte överstiga 1 024 tecken.
- Ta bort eller byt namn på blob eller filer så att namnen inte överstiger 1024 tecken.

Mer information finns i Azure-namngivningskonventionerna för blobnamn och filnamn.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Felbeskrivning:** Ett av segmenten i blobben eller filnamnet är för långt.

**Föreslagen upplösning:** Ett av sökvägssegmenten i blobben eller filnamnet överskrider det maximala antalet tecken. Ett bansegment är strängen mellan på varandra följande avgränsare, till exempel det främre snedstrecket /.

- Ladda ned och granska felfilerna på sidan **Anslut och kopiera** i det lokala webbgränssnittet.
- Kontrollera att [blob-namnen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) och [filnamnen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) överensstämmer med Azure-namngivningskonventioner.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Felbeskrivning:** Felaktiga behållarnamn har angetts för hanterade diskresurser.

**Föreslagen upplösning:** För hanterade diskar skapas följande mappar inom varje resurs som motsvarar behållare i ditt lagringskonto: Premium SSD, Standard HDD och Standard SSD. Dessa mappar motsvarar prestandanivån för den hanterade disken.

- Kontrollera att du kopierar sidblobbdata (VHD) till en av dessa befintliga mappar. Endast data från dessa befintliga behållare överförs till Azure.
- Alla andra mappar som skapas på samma nivå som Premium SSD, Standard HDD och Standard SSD motsvarar inte en giltig prestandanivå och kan inte användas.
- Ta bort filer eller mappar som skapats utanför prestandanivåerna.

Mer information finns i [Kopiera till hanterade diskar](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om kraven för [lagringssystem för databoxblo.](data-box-system-requirements-rest.md)
