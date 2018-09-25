---
title: Azure Data Box begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box-komponenter och anslutningar.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/05/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: fe42380288c0f139a2bae80a12f0ebc428a4c286
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993093"
---
# <a name="azure-data-box-limits"></a>Azure Data Box-gränser

Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box. I följande tabell beskrivs dessa gränser för Data Box.


## <a name="data-box-service-limits"></a>Data Box-tjänstbegränsningar

 - Data Box-tjänsten är tillgänglig endast inom USA alls den [Azure-regioner för offentliga Azure-molnet](https://azure.microsoft.com/regions/).
 - Om du använder flera lagringskonton med Data Box-tjänsten, måste alla lagringskonton tillhöra samma Azure-region endast.
 - Vi rekommenderar att du använder mer än tre lagringskonton. Använda flera konton kan potentiellt påverka prestandan.

## <a name="data-box-limits"></a>Databegränsningar för Box

- Data Box kan lagra upp till 5 miljoner filer.

## <a name="azure-storage-limits"></a>Azure storage-begränsningar

Det här avsnittet beskriver begränsningar för Azure Storage-tjänsten och de nödvändiga namngivningskonventionerna för Azure Files, Azure blockblob-objekt och Azure-sidblobar, som gäller för Data Box-tjänsten. Granska Lagringsgränser noggrant och följer alla rekommendationer.

Gå till den senaste informationen på tjänstbegränsningar för Azure storage och bästa praxis för namngivning av resurser, behållare och filer:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockblob-objekt och konventioner för sidan blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider gränserna för Azure Storage-tjänsten eller inte följer namngivningskonventionerna i Azure-filer/Blob, sedan dessa filer eller kataloger är inte matas in i Azure Storage via Data Box-tjänsten.

## <a name="data-upload-caveats"></a>Överföra data varningar

- Kopiera inte data direkt under införande resurser. Du måste skapa en mapp under resursen och kopiera sedan data till mappen.
- En mapp under den *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* är en behållare. Exempelvis behållare skapas som *StorageAccount_BlockBlob/behållare* och *StorageAccount_PageBlob/behållare*.
- Varje mapp som skapas direkt under *StorageAccount_AzureFiles* översätts till en Azure-filresurs.
- Om du har ett befintligt Azure objekt (till exempel en blob eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs Data Box till filen i molnet.
- Alla filer som skrivits till *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* resurser överförs som en blockblob och sidblob respektive.
- Azure blob storage stöder inte kataloger. Om du skapar en mapp under den *StorageAccount_BlockBlob* mapp, skapas virtuella mappar i blobnamnet. För Azure Files bevaras faktiska katalogstruktur.
- Eventuella tomma Kataloghierarki (utan några filer) som skapats under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* mappar inte laddas.
- Om det finns några fel när du överför data till Azure, skapas en fellogg i mållagringskontot. Sökvägen till den här loggen är tillgänglig när överföringen är klar och du kan granska loggen för att vidta åtgärder. Ta inte bort data från källan utan att verifiera överförda data.

## <a name="azure-storage-account-size-limits"></a>Storleksgränser för Azure storage-konto

Här finns gränserna på mängden data som kopieras till storage-konto. Kontrollera att datan överensstämmer med de här gränserna. Gå till den senaste informationen om dessa begränsningar [prestandamål i Azure blob storage skala](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) och [Azure Files skala mål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Storleken på data som kopieras till Azure storage-konto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Blockblob och page blob                                            | 500 TiB per lagringskonto. <br> Detta inkluderar data från alla källor, inklusive Data Box.|
| Azure-fil                                                          | 5 TiB per resurs.<br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen.       |

## <a name="azure-object-size-limits"></a>Storleksgränser för Azure-objekt

Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> Varje fil som laddats upp sidan blobformatet måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad. |
| Azure-fil        | 1 TiB                                                      |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure blockblob, sidblob och namngivningsregler

| Entitet                                       | Konventioner                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Namn på behållare för blockblob och page blob | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan innehålla endast gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad tillåts inte i namn. |
| Resursnamn för Azure files                  | Samma som ovan                                                                                                                                                                                                                                                                                                             |
| Katalog- och filnamn för Azure files     |<li> Bevara, skiftlägesokänslig och får inte överstiga 255 tecken långt. </li><li> Får inte sluta med snedstreck (/). </li><li>Om det tas automatiskt bort. </li><li> Följande tecken är inte tillåtna: ' ”\ /: | < > * ”?</li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Ogiltiga tecken i URL-sökväg är inte tillåtna. Kodpunkter som \uE000 är inte giltig Unicode-tecken. Vissa ASCII eller Unicode-tecken som kontrolltecken (0x00 0x1F, \u0081 etc.), också är inte tillåtna. Regler för att styra Unicode strängar i HTTP/1.1 finns i RFC 2616 avsnittet 2.2: grundläggande regler och RFC 3987. </li><li> Följande fil är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, punkt (.), och två punkter tecken (.).</li>|
| Blobnamn för blockblobar och sidblobar      | </li><li>Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. </li><li>Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. </li><li>Reserverade URL-tecken måste undantas korrekt. </li><li>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog.</li> |
