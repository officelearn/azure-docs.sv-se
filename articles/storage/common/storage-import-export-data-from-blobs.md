---
title: Använda Azure Import/Export för att exportera data från Azure Blobs | Microsoft Docs
description: Lär dig hur du skapar export-jobb i Azure portal för att överföra data från Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: a7456bedeb6bc870ad5d6892fe697fb2dfcd3bc1
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527812"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Använda Azure Import/Export-tjänsten för att exportera data från Azure Blob storage
Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att exportera stora mängder data på ett säkert sätt från Azure Blob storage. Tjänsten kräver att du kan leverera tomma enheter till Azure-datacentret. Tjänsten exporterar data från ditt lagringskonto till enheterna och sedan levereras enheter tillbaka.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar en export-jobbet för att överföra data från Azure Blob Storage måste du noggrant granska och slutför följande lista över kraven för den här tjänsten. Måste du:

- Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
- Har minst en Azure Storage-konto. Se en lista över [storage-konton och lagringstyper stöds för tjänsten Import/Export](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns i [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
- Ha ett FedEx/DHL-konto.  
    - Kontot måste vara giltig, bör ha saldo och måste ha returfrakt funktioner.
    - Generera en spårningsnummer för export-jobbet.
    - Alla jobb bör ha en separat spårningsnummer. Flera jobb med samma Spårningsnumret stöds inte. 
    - Om du inte har en transportföretagskonto går du till:
        - [Skapa ett konto för FedEX](https://www.fedex.com/en-us/create-account.html), eller 
        - [Skapa ett konto för DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Steg 1: Skapa ett exportjobb

Utför följande steg för att skapa ett exportjobb i Azure-portalen.

1. Logga in på https://portal.azure.com/.
2. Gå till **alla tjänster > Storage > Import/export-jobb**. 

    ![Gå till Import/export-jobb](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klicka på **skapa Import/export-jobbet**.

    ![Klicka på Import/export-jobbet](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. I **grunderna**:
    
    - Välj **exportera från Azure**. 
    - Ange ett beskrivande namn för export-jobbet. Använd det namn du väljer att spåra förloppet för dina jobb. 
        - Namnet får innehålla endast gemener, siffror, bindestreck och understreck.
        - Namnet måste börja med en bokstav och får inte innehålla blanksteg. 
    - Välj en prenumeration.
    - Ange eller välj en resursgrupp.

        ![Grundläggande inställningar](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. I **Jobbdetaljer**:

    - Välj det lagringskonto där det finns data som ska exporteras. Använd ett lagringskonto nära där du befinner dig.
    - Samlingsplats fylls i automatiskt baserat på regionen som det valda lagringskontot. 
    - Ange blob-data som du vill exportera från ditt lagringskonto till din tom eller enheter. 
    - Välja att **exportera alla** blobdata i lagringskontot.
    
         ![Exportera alla](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Du kan ange vilka behållare och blobbar som ska exporteras.
        - **Ange en blob till exportera**: Använd den **Equal To** väljare. Ange den relativa sökvägen till bloben från och med behållarens namn. Använd *$root* ange root-behållaren.
        - **Ange alla blobar som börjar med prefixet**: Använd den **börjar med** väljare. Ange det prefix som börjar med ett snedstreck (/). Prefixet kan vara prefixet för behållarens namn, fullständig behållarens namn eller fullständig behållarens namn följt av prefixet för blob-namnet. Du måste ange blob-sökvägar i giltigt format för att undvika fel under bearbetning, som visas i den här skärmbilden. Mer information finns i [exempel på giltiga blob sökvägar](#examples-of-valid-blob-paths). 
   
           ![Exportera valda behållare och blobbar](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Du kan exportera från bloblistfilen.

        ![Exportera från blobblistfil](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Om blobben som ska exporteras används vid kopiering av data, Azure Import/Export-tjänsten tar en ögonblicksbild av blobben och kopierar ögonblicksbilden.
 

4. I **returnera leveransinformation**:

    - Välj vilken operatör i listrutan.
    - Ange en giltig transportföretagets kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter till dig när importjobbet har slutförts. 
    - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region.

        > [!TIP] 
        > Ange en gruppens e-post istället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar företaget.
   
5. I **sammanfattning**:

    - Granska information om jobbet.
    - Anteckna Jobbnamnet och angivna Azure-datacenter som leveransadress för att skicka diskar till Azure. 

        > [!NOTE] 
        > Skicka alltid diskarna i datacentret som anges i Azure-portalen. Om hårddiskarna skickas till fel datacentret, kommer jobbet inte att bearbetas.

    - Klicka på **OK** för att skapa för export-jobbet.

## <a name="step-2-ship-the-drives"></a>Steg 2: Skicka enheterna

Om du inte vet hur många enheter som du behöver gå till den [kontrollera antalet enheter](#check-the-number-of-drives). Om du vet hur många enheter kan fortsätta att skicka enheterna.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Steg 3: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Steg 4: Har tagit emot diskarna
När instrumentpanelen rapporterar jobbet har slutförts, hårddiskarna skickas till dig och leveransens spårningsnummer är tillgänglig på portalen.

1. När du får enheter med exporterade data, måste du hämta BitLocker-nycklar för att låsa upp enheter. Gå till export-jobbet i Azure-portalen. Klicka på **Import/Export** fliken. 
2. Välj och klicka på din export-jobb i listan. Gå till **BitLocker-nycklar** och kopiera nycklarna.
   
   ![Visa BitLocker-nycklar för export-jobbet](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Använd BitLocker för att låsa upp diskarna.

Exporten har slutförts. Du kan ta bort jobbet just nu, eller så kan automatiskt hämtar bort efter 90 dagar.


## <a name="check-the-number-of-drives"></a>Kontrollera antalet enheter

Detta *valfritt* steg hjälper till att du bestämmer hur många enheter som krävs för export-jobbet. Utför det här steget på ett Windows-system som kör en [stöd för OS-version](storage-import-export-requirements.md#supported-operating-systems).

1. [Ladda ned WAImportExport version 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) på Windows-system. 
2. Packa upp i standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.
3. Öppna ett fönster med PowerShell eller Kommandotolken med administratörsbehörighet. Om du vill ändra katalogen till mappen uppzippade, kör du följande kommando:
    
    `cd C:\WaImportExportV1`

4. Om du vill kontrollera antalet diskar som krävs för de valda blobarna, kör du följande kommando:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    I följande tabell beskrivs parametrarna:
    
    |Kommandoradsparametern|Beskrivning|  
    |--------------------------|-----------------|  
    |**/logdir:**|Valfri. Loggkatalogen. Utförliga loggfiler skrivs till den här katalogen. Om den inte anges används den aktuella katalogen som log-katalogen.|  
    |**/SN:**|Krävs. Namnet på lagringskontot för export-jobbet.|  
    |**/Sk:**|Krävs endast om en SAS-behållare inte har angetts. Kontonyckel för lagringskontot för export-jobbet.|  
    |**/csas:**|Krävs endast om en lagringskontonyckel inte har angetts. Behållaren SAS för att lista blobbar exporteras i export-jobbet.|  
    |**/ ExportBlobListFile:**|Krävs. Sökväg till XML-Datatypen filen som innehåller listan över blob-sökvägar eller blob-prefix som sökväg för BLOB-objekt som ska exporteras. Filformatet som används i den `BlobListBlobPath` elementet i den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) driften av REST-API för Import/Export-tjänsten.|  
    |**/ DriveSize:**|Krävs. Storleken på enheter som ska användas för ett exportjobb *t.ex.*, 500 GB, 1,5 TB.|  

    Se en [exempel på kommandot PreviewExport](#example-of-previewexport-command).
 
5. Kontrollera att du kan läsa/skriva till enheterna som ska publiceras för export-jobbet.

### <a name="example-of-previewexport-command"></a>Exempel på PreviewExport kommando

Exemplet nedan visar den `PreviewExport` kommando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Export-blobblistfil kan innehålla blobnamn och blob-adressprefix, som visas här:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export-verktyget visar en lista över alla blobbar exporteras och beräknar hur att bygga dem i enheter av den angivna storleken med hänsyn till eventuella nödvändiga arbetet och sedan beräknar antalet enheter som behövs för att lagra blobar och enhetsinformationen för användning.  
  
Här är ett exempel på utdata med endast i informationssyfte loggar utelämnas:  
  
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

I följande tabell visas exempel på giltiga blob sökvägar:
   
   | Väljare | Blobbsökväg | Beskrivning |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla blobar i lagringskontot |
   | Börjar med |/$root / |Exporterar alla blobar i behållaren rot |
   | Börjar med |/Book |Exporterar alla blobbar i en behållare som börjar med prefixet **bok** |
   | Börjar med |/Music/ |Exporterar alla blobar i behållaren **musik** |
   | Börjar med |/ musik/kärlek |Exporterar alla blobar i behållaren **musik** som börjar med prefixet **älskar** |
   | Lika med |$root/logo.bmp |Exporterar blob **logo.bmp** i root-behållaren |
   | Lika med |videos/Story.mp4 |Exporterar blob **story.mp4** i behållaren **videor** |

## <a name="next-steps"></a>Nästa steg

* [Visa status för jobbet och enhet](storage-import-export-view-drive-status.md)
* [Granska kraven för Import/Export](storage-import-export-requirements.md)


