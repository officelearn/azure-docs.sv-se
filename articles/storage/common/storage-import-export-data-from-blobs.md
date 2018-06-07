---
title: Använda Azure Import/Export för att exportera data från Azure BLOB | Microsoft Docs
description: Lär dig hur du skapar exportjobb i Azure-portalen för att överföra data från Azure BLOB.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: eb41708c7446b3139758678c9247ffbb11da8b40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661004"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Använda tjänsten Azure Import/Export för att exportera data från Azure Blob storage
Den här artikeln innehåller stegvisa instruktioner om hur du använder tjänsten Azure Import/Export för att exportera stora mängder data på ett säkert sätt från Azure Blob storage. Tjänsten kräver att du levererar tomma enheter till Azure-datacenter. Tjänsten exporterar data från ditt lagringskonto till enheterna och levereras enheter tillbaka.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett exportjobb för att överföra data från Azure Blob Storage noggrant granska och utför följande förutsättningar för den här tjänsten. Du måste:

- Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
- Ha minst ett Azure Storage-konto. Visa en lista över [storage-konton och lagringstyper som stöds för Import/Export service](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).

## <a name="step-1-create-an-export-job"></a>Steg 1: Skapa ett exportjobb

Utför följande steg för att skapa ett exportjobb i Azure-portalen.

1. Logga in på https://portal.azure.com/.
2. Gå till **alla services > Storage > Import/export jobb**. 

    ![Gå till Import/export-jobb](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klicka på **skapa Import-/ exportjobb**.

    ![Klicka på Importera och exportera jobb](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. I **grunderna**:
    
    - Välj **exportera från Azure**. 
    - Ange ett beskrivande namn för exportjobbet. Använd det namn du väljer att spåra förloppet för jobbet. 
        - Namnet får innehålla endast små bokstäver, siffror, bindestreck och understreck.
        - Namnet måste börja med en bokstav och får inte innehålla blanksteg. 
    - Välj en prenumeration.
    - Ange eller välj en resursgrupp.

        ![Grundläggande inställningar](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. I **Jobbdetaljer**:

    - Välj det lagringskonto där data exporteras finns. 
    - Samlingsbibliotek platsen fylls i automatiskt baserat på regionen som det lagringskonto som valts. 
    - Ange blob-data som du vill exportera från ditt lagringskonto till tomma enheten eller enheter. 
    - Välja att **exporterar alla** blob-data i lagringskontot.
    
         ![Exportera allt](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Du kan ange vilka behållare och blobbar att exportera.
        - **Ange en blob att exportera**: Använd den **lika med** selector. Ange den relativa sökvägen till blob, från och med behållarens namn. Använd *$root* ange root-behållaren.
        - **Ange alla BLOB som börjar med ett prefix**: Använd den **börjar med** väljare. Ange det prefix som börjar med ett snedstreck (/). Prefixet kan vara prefixet för behållarens namn, fullständig behållarens namn eller fullständig behållarens namn följt av prefixet för blob-namnet. Du måste ange blob-sökvägar i ett giltigt format för att undvika fel under bearbetning, som visas i den här skärmbilden. Mer information finns i [exempel på giltiga blob sökvägar](#examples-of-valid-blob-paths). 
   
           ![Exportera valda behållare och blobbar](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Du kan exportera från filen med listan blob.

        ![Exportera från listan blob-fil](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Om blob exporteras används vid kopiering av data, Azure Import/Export service tar en ögonblicksbild av blob och kopierar ögonblicksbilden.
 

4. I **returnera leverans info**:

    - Välj en operatör i listrutan.
    - Ange en giltig operatör kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts. 
    - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region.
   
5. I **sammanfattning**:

    - Granska detaljer för jobbet.
    - Observera leveransadressen jobbet namn och angivna Azure-datacenter för leverans av diskar till Azure. 
    - Klicka på **OK** slutföra exporten jobb.

## <a name="step-2-ship-the-drives"></a>Steg 2: Levereras enheterna

Om du inte vet hur många enheter som du behöver gå till den [kontrollera antalet enheter](#check-the-number-of-drives). Om du vet hur många enheter kan fortsätta att leverera enheterna.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Steg 3: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Steg 4: Ta emot diskar
När instrumentpanelen rapporterar jobbet är klart, diskarna levereras till dig och Spårningsnumret för leveransen är tillgänglig på portalen.

1. Du måste hämta BitLocker-nycklar för att låsa upp enheter när du har fått enheter med exporterade data. Gå till exportjobb i Azure-portalen. Klicka på **Import/Export** fliken. 
2. Välj och klicka på exporten jobbet i listan. Gå till **BitLocker-nycklar** och kopiera nycklarna.
   
   ![Visa BitLocker-nycklar för exportjobb](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Använd BitLocker för att låsa upp diskarna.

Exporten har slutförts. Du kan ta bort jobbet just nu, eller så kan automatiskt hämtar bort efter 90 dagar.


## <a name="check-the-number-of-drives"></a>Kontrollera antalet enheter

Detta *valfria* steg hjälper dig att du bestämmer hur många enheter som krävs för att exportera jobbet. Utför det här steget på ett Windows-system som kör en [stöds OS-version](storage-import-export-requirements.md#supported-operating-systems).

1. [Hämta WAImportExport version 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) på Windows-systemet. 
2. Packa upp i standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.
3. Öppna ett fönster med PowerShell eller Kommandotolken med administratörsbehörighet. Ändra katalogen till mappen uppackade genom att köra följande kommando:
    
    `cd C:\WaImportExportV1`

4. Kontrollera antalet diskar som krävs för den valda BLOB-objekt genom att köra följande kommando:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    I följande tabell beskrivs parametrarna:
    
    |Kommandoradsparametern|Beskrivning|  
    |--------------------------|-----------------|  
    |**/logdir:**|Valfri. Loggkatalogen. Utförlig loggfilerna skrivs till den här katalogen. Om inget anges används den aktuella katalogen som loggkatalogen.|  
    |**/SN:**|Krävs. Namnet på lagringskontot för exportjobbet.|  
    |**/Sk:**|Krävs endast om en behållare SAS inte har angetts. Kontonyckel för lagringskontot för exportjobbet.|  
    |**/csas:**|Krävs endast om en lagringskontonyckel inte har angetts. Behållare SAS för att visa en lista över blobbar som ska exporteras i exportjobbet.|  
    |**/ ExportBlobListFile:**|Krävs. Sökvägen till XML-fil som innehåller listan över blob-sökvägar eller blob sökväg prefix för blob som ska exporteras. Filformat som används i den `BlobListBlobPath` element i den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) driften av tjänsten Import/Export REST API.|  
    |**/ DriveSize:**|Krävs. Storleken på enheter som ska användas för ett exportjobb *t.ex.*, 500 GB, 1,5 TB.|  

    Se en [exempel på kommandot PreviewExport](#example-of-previewexport-command).
 
5. Kontrollera att du kan läsa/skriva till enheter som ska levereras för exportjobbet.

### <a name="example-of-previewexport-command"></a>Exempel på PreviewExport kommando

I följande exempel visas den `PreviewExport` kommando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Exportfilen blob listan kan innehålla blobbnamnen och blob-prefix, som visas här:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Verktyget Azure Import/Export listar alla BLOB som ska exporteras och beräknar så pack dem till enheter i den angivna storleken med hänsyn till alla nödvändiga kostnader och sedan beräknar antalet enheter som behövs för blobbar och användningsinformation för enheten.  
  
Här är ett exempel på utdata med informativt loggar utelämnas:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Exempel på giltiga blob sökvägar

I följande tabell visas exempel på giltiga blob-sökvägar:
   
   | Väljare | BLOB-sökväg | Beskrivning |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla BLOB storage-konto |
   | Börjar med |/$root / |Exporterar alla blobbar i behållaren rot |
   | Börjar med |/Book |Exporterar alla blobbar i en behållare som börjar med prefixet **book** |
   | Börjar med |/Music/ |Exporterar alla blobbar i behållaren **musik** |
   | Börjar med |/ musik/kärlek |Exporterar alla blobbar i behållaren **musik** som börjar med prefixet **gillar** |
   | Lika med |$root/logo.bmp |Export blob **logo.bmp** i behållaren rot |
   | Lika med |videos/Story.mp4 |Export blob **story.mp4** i behållaren **videor** |

## <a name="next-steps"></a>Nästa steg

* [Visa status för jobbet och enhet](storage-import-export-view-drive-status.md)
* [Granska kraven för Import/Export](storage-import-export-requirements.md)


