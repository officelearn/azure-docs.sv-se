---
title: Azure Data Box-Disk begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box-Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/19/2019
ms.author: alkohli
ms.openlocfilehash: 70a611f6a9b52ba6a4c904cc4cfa9bc8f0b4df8e
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409592"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box-Disk-gränser


Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box-Disk-lösning.

## <a name="data-box-service-limits"></a>Data Box-tjänstbegränsningar

 - Data Box-tjänsten är tillgänglig i Azure-regioner som anges i [regiontillgänglighet](data-box-disk-overview.md#region-availability).
 - Ett enda lagringskonto stöds med Data Box-Disk.

## <a name="data-box-disk-performance"></a>Prestanda för data Box-Disk

Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

## <a name="azure-storage-limits"></a>Azure storage-begränsningar

Det här avsnittet beskriver begränsningar för Azure Storage-tjänsten och de nödvändiga namngivningskonventionerna för Azure Files, Azure blockblob-objekt och Azure-sidblobar, som gäller för Data Box-tjänsten. Granska Lagringsgränser noggrant och följer alla rekommendationer.

Gå till den senaste informationen på tjänstbegränsningar för Azure storage och bästa praxis för namngivning av resurser, behållare och filer:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockblob-objekt och konventioner för sidan blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider gränserna för Azure Storage-tjänsten eller inte följer namngivningskonventionerna i Azure-filer/Blob, sedan dessa filer eller kataloger är inte matas in i Azure Storage via Data Box-tjänsten.

## <a name="data-upload-caveats"></a>Överföra data varningar

- Kopiera inte data direkt till diskarna. Kopiera data till färdiga *BlockBlob*,*PageBlob*, och *AzureFile* mappar.
- En mapp under den *BlockBlob* och *PageBlob* är en behållare. Exempelvis behållare skapas som *BlockBlob/behållare* och *PageBlob/behållare*.
- Om du har ett befintligt Azure objekt (till exempel en blob) i molnet med samma namn som det objekt som ska kopieras skrivs Data Box-Disk till filen i molnet.
- Alla filer som skrivits till *BlockBlob* och *PageBlob* resurser överförs som en blockblob och sidblob respektive.
- Eventuella tomma Kataloghierarki (utan några filer) som skapats under *BlockBlob* och *PageBlob* mappar inte laddas.
- Om det finns några fel när du överför data till Azure, skapas en fellogg i mållagringskontot. Sökvägen till den här loggen är tillgänglig i portalen när överföringen är klar och du kan granska loggen för att vidta åtgärder. Ta inte bort data från källan utan att verifiera överförda data.
- Om du har angett hanterade diskar i ordningen kan du läsa följande ytterligare överväganden:

    - Du kan bara ha en hanterad disk med ett givet namn i en resursgrupp olika alla införande mappar och i alla de Data Box-Disk. Detta innebär att de virtuella hårddiskarna som överförts till mapparna införande bör ha unika namn. Kontrollera att det angivna namnet inte matchar en redan befintlig hanterad disk i en resursgrupp. Om virtuella hårddiskar har samma namn kan konverteras bara en virtuell Hårddisk till hanterade diskar med samma namn. De andra virtuella hårddiskarna laddas upp mellanlagringskontot som sidblobar.
    - Kopiera alltid de virtuella hårddiskarna till någon av mapparna införande. Om du kopierar de virtuella hårddiskarna utanför dessa mappar eller i en mapp som du skapade de virtuella hårddiskarna överförs till Azure Storage-konto som sidblobar och hanterade inte diskar.
    - Endast de fasta virtuella hårddiskarna kan laddas upp för att skapa hanterade diskar. Dynamiska virtuella hårddiskar, Differentierande virtuella hårddiskar eller VHDX-filer stöds inte.

## <a name="azure-storage-account-size-limits"></a>Storleksgränser för Azure storage-konto

Här finns gränserna på mängden data som kopieras till storage-konto. Kontrollera att datan överensstämmer med de här gränserna. Gå till den senaste informationen om dessa begränsningar [prestandamål i Azure blob storage skala](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) och [Azure Files skala mål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Storleken på data som kopieras till Azure storage-konto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Sidan och block Blob-blob                                            | 500 TB per lagringskonto. <br> Detta inkluderar data från alla källor, inklusive Data Box-Disk.|


## <a name="azure-object-size-limits"></a>Storleksgränser för Azure-objekt

Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> (Alla filer som överförts i sidans Blob-format måste vara 512 byte justerad, annars överföringen misslyckas. <br> Både VHD- och VHDX är 512 byte justerad.) |
|Azure Files        | 1 TiB <br> Max. storleken på filresursen är 5 TiB     |
| Hanterade diskar     |4 TiB <br> Mer information om storlek och begränsningar finns: <li>[Skalbarhetsmål för hanterade diskar](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)
</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure blockblob, sidblob och namngivningsregler

| Entitet                                       | Konventioner                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Namn på behållare för blockblob och page blob <br> Namn på filresursen för Azure Files | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan innehålla endast gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad tillåts inte i namn. |
| Katalog- och filnamn för Azure files     |<li> Bevara, skiftlägesokänslig och får inte överstiga 255 tecken långt. </li><li> Får inte sluta med snedstreck (/). </li><li>Om det tas automatiskt bort. </li><li> Följande tecken är inte tillåtna: ' ”\ /: | < > * ?`</li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Ogiltiga tecken i URL-sökväg är inte tillåtna. Kodpunkter som \uE000 är inte giltig Unicode-tecken. Vissa ASCII eller Unicode-tecken som kontrolltecken (0x00 0x1F, \u0081 etc.), också är inte tillåtna. Regler för att styra Unicode strängar i HTTP/1.1 finns i RFC 2616 avsnittet 2.2: Grundläggande regler och RFC 3987. </li><li> Följande filnamn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, punkt (.), och två punkter tecken (.).</li>|
| Blobnamn för blockblobar och sidblobar      | Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. <br> Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. <br> Reserverade URL-tecken måste undantas korrekt. <br>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog. |

## <a name="managed-disk-naming-conventions"></a>Managed disk namngivningskonventioner

| Entitet | Konventioner                                             |
|-------------------|-----------------------------------------------------------|
| Managed disk namn       | <li> Namnet måste vara 1 och 80 tecken långt. </li><li> Namnet måste börja med en bokstav eller siffra, sluta med en bokstav, siffra eller understreck. </li><li> Namnet får innehålla endast bokstäver, siffror, understreck, punkter eller bindestreck. </li><li>   Namnet får inte innehålla blanksteg eller `/`.                                              |

## <a name="next-steps"></a>Nästa steg

- Granska [Data Box-systemkrav](data-box-system-requirements.md)
