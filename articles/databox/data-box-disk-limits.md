---
title: Diskgränser för Azure Data Box | Microsoft-dokument
description: Beskriver systemgränser och rekommenderade storlekar för Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260168"
---
# <a name="azure-data-box-disk-limits"></a>Diskgränser för Azure Data Box


Tänk på dessa gränser när du distribuerar och använder din Microsoft Azure Data Box Disk-lösning.

## <a name="data-box-service-limits"></a>Begränsningar för tjänsten Data Box

 - Data Box-tjänsten är tillgänglig i Azure-regioner som anges i [Region tillgänglighet](data-box-disk-overview.md#region-availability).
 - Ett enda lagringskonto stöds med Data Box Disk.

## <a name="data-box-disk-performance"></a>Prestanda för disk i databox

Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

## <a name="azure-storage-limits"></a>Azure-lagringsgränser

I det här avsnittet beskrivs gränserna för Azure Storage-tjänsten och de nödvändiga namngivningskonventionerna för Azure-filer, Azure-blockblobar och Azure-sidblobar, beroende på databox-tjänsten. Granska lagringsgränserna noggrant och följ alla rekommendationer.

Den senaste informationen om Azure-lagringstjänstgränser och metodtips för namngivning av resurser, behållare och filer finns i:

- [Namnge och referera behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och sidblobbkonventioner](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstgränserna, eller inte överensstämmer med Azure Files/Blob-namngivningskonventioner, får dessa filer eller kataloger inte förtärs i Azure Storage via databox-tjänsten.

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

- Kopiera inte data direkt till diskarna. Kopiera data till förskapade *BlockBlob-,**PageBlob-* och *AzureFile-mappar.*
- En mapp under *BlockBlob* och *PageBlob* är en behållare. Behållare skapas till exempel som *BlockBlob/container* och *PageBlob/container*.
- Om du har ett befintligt Azure-objekt (till exempel en blob) i molnet med samma namn som objektet som kopieras, byter Data Box Disk namn på filen som fil(1) i molnet.
- Varje fil som skrivs in i *BlockBlob-* och *PageBlob-aktier* laddas upp som en blockblob respektive sidblobb.
- Alla tomma kataloghierarkier (utan några filer) som skapas under *mapparna BlockBlob* och *PageBlob* överförs inte.
- Om det finns några fel när du överför data till Azure skapas en fellogg i mållagringskontot. Sökvägen till den här felloggen är tillgänglig i portalen när överföringen är klar och du kan granska loggen för att vidta korrigerande åtgärder. Ta inte bort data från källan utan att verifiera de överförda data.
- Filmetadata och NTFS-behörigheter bevaras inte när data överförs till Azure-filer. Det senast *ändrade attributet* för filerna sparas till exempel inte när data kopieras.
- Om du har angett hanterade diskar i ordningen läser du följande ytterligare överväganden:

    - En hanterad disk i en resursgrupp måste ha ett unikt namn i de förskapade mapparna och på hela Data Box Disk. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp. Om de virtuella hårddiskarna har samma namn, konverteras bara en virtuell hårddisk till den hanterade disken med det namnet. De andra virtuella hårddiskarna laddas upp som sidblobar till mellanlagringskontot.
    - Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar de virtuella hårddiskarna utanför dessa mappar eller i en mapp som du har skapat, laddas de virtuella hårddiskarna upp till Azure Storage-kontot som sidblobar i stället för som hanterade diskar.
    - Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. Dynamiska virtuella hårddiskar och differentierande VHD- eller VHDX-filer stöds inte.

## <a name="azure-storage-account-size-limits"></a>Storleksbegränsningar för Azure-lagringskonto

Här är gränserna för storleken på de data som kopieras till lagringskontot. Se till att de data du laddar upp överensstämmer med dessa gränser. Den senaste informationen om dessa gränser finns i [Azure blob storage scale targets](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) och Azure Files scale [targets](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts).

| Storleken på data som kopieras till Azure-lagringskonto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Block Blob och sidblob                                            | 500 TB per lagringskonto. <br> Detta inkluderar data från alla källor, inklusive Data Box Disk.|


## <a name="azure-object-size-limits"></a>Storleksbegränsningar för Azure-objekt

Här är storleken på Azure-objekt som kan skrivas. Kontrollera att alla filer som överförs överensstämmer med dessa gränser.

| Azure-objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Block Blob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> (Varje fil som laddas upp i sidblobbformat måste vara 512 byte justerad, annars misslyckas överföringen. <br> Både DEN VIRTUELLA HÅRDDISKEN och VHDX är 512 byte justerade.) |
|Azure Files        | 1 TiB <br> Max. storleken på andelen är 5 TiB     |
| Hanterade diskar     |4 TiB <br> Mer information om storlek och begränsningar finns i: <li>[Skalbarhetsmål för hanterade diskar](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure-blockblob, sidblob och filnamnskonventioner

| Entitet                                       | Konventioner                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Behållarnamn för blockblob och sidblob <br> Fildelningsnamn för Azure-filer | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan bara innehålla gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad tillåts inte i namn. |
| Katalog- och filnamn för Azure-filer     |<li> Fallbevarande, skiftlägesokänslig och får inte vara längre än 255 tecken. </li><li> Det går inte att sluta med det främre snedstrecket (/). </li><li>Om det tillhandahålls tas den bort automatiskt. </li><li> Följande tecken är inte tillåtna:<code>" \\ / : \| < > * ?</code></li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Ogiltiga URL-sökvägstecken är inte tillåtna. Kodpunkter \\som uE000 är inte giltiga Unicode-tecken. Vissa ASCII- eller Unicode-tecken, som kontrolltecken (0x00 till 0x1F, \\u0081, etc.), är inte heller tillåtna. Regler som styr Unicode-strängar i HTTP/1.1 finns i RFC 2616, avsnitt 2.2: Grundläggande regler och RFC 3987. </li><li> Följande filnamn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.).</li>|
| Blobnamn för blockblobar och sidblobar      | Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. <br> Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. <br> Reserverade URL-tecken måste undantas korrekt. <br>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog. |

## <a name="managed-disk-naming-conventions"></a>Konventioner för hanterade disknamn

| Entitet | Konventioner                                             |
|-------------------|-----------------------------------------------------------|
| Hanterade disknamn       | <li> Namnet måste vara 1 till 80 tecken långt. </li><li> Namnet måste börja med en bokstav eller ett nummer, sluta med en bokstav, ett nummer eller ett understreck. </li><li> Namnet får bara innehålla bokstäver, siffror, understreck, punkter eller bindestreck. </li><li>   Namnet ska inte ha `/`blanksteg eller .                                              |

## <a name="next-steps"></a>Nästa steg

- Granska [systemkrav för databoxdiskar](data-box-disk-system-requirements.md)
