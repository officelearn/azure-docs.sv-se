---
title: Använda Azure Import/Export för att exportera data från Azure Blobbar | Microsoft-dokument
description: Lär dig hur du skapar exportjobb i Azure-portalen för att överföra data från Azure Blobbar.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a5afa6439caa6b7c1572447e3b212f3357bf296a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282519"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Använda Azure Import/Export-tjänsten till att exportera data från Azure Blob Storage

Den här artikeln innehåller steg-för-steg-instruktioner om hur du använder Azure Import/Export-tjänsten för att på ett säkert sätt exportera stora mängder data från Azure Blob-lagring. Tjänsten kräver att du skickar tomma enheter till Azure-datacentret. Tjänsten exporterar data från ditt lagringskonto till enheterna och skickar sedan tillbaka enheterna.

## <a name="prerequisites"></a>Krav

Innan du skapar ett exportjobb för att överföra data från Azure Blob Storage bör du noggrant granska och fylla i följande lista över förutsättningar för den här tjänsten.
Du måste:

- Har en aktiv Azure-prenumeration som kan användas för tjänsten Import/Export.
- Har minst ett Azure Storage-konto. Se listan över [lagringskonton och lagringstyper som stöds för tjänsten Import/Export](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto](storage-account-create.md).
- Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
- Har ett FedEx/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL `adbops@microsoft.com`kontaktar du Azure Data Box Operations team på .
  - Kontot måste vara giltigt, ha saldo och måste ha returfraktkapacitet.
  - Generera ett spårningsnummer för exportjobbet.
  - Varje jobb ska ha ett separat spårningsnummer. Flera jobb med samma spårningsnummer stöds inte.
  - Om du inte har ett operatörskonto går du till:
    - [Skapa ett FedEX-konto](https://www.fedex.com/en-us/create-account.html)eller
    - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Steg 1: Skapa ett exportjobb

Utför följande steg för att skapa ett exportjobb i Azure-portalen.

1. Logga in https://portal.azure.com/på .
2. Gå till **Alla tjänster > lagring > importera/exportera jobb**.

    ![Gå till Importera/exportera jobb](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klicka på **Skapa import-/exportjobb**.

    ![Klicka på Importera/exportera jobb](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. I **grunderna:**

    - Välj **Exportera från Azure**.
    - Ange ett beskrivande namn för exportjobbet. Använd det namn du väljer för att spåra förloppet för dina jobb.
        - Namnet får bara innehålla gemener, siffror, bindestreck och understreck.
        - Namnet måste börja med en bokstav och får inte innehålla blanksteg.
    - Välj en prenumeration.
    - Ange eller välj en resursgrupp.

        ![Grundläggande inställningar](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. I **Jobbinformation:**

    - Välj det lagringskonto där de data som ska exporteras finns. Använd ett lagringskonto nära den plats där du befinner dig.
    - Avlämningsplatsen fylls automatiskt i baserat på regionen för det valda lagringskontot.
    - Ange de blob-data som du vill exportera från ditt lagringskonto till din tomma enhet eller enheter.
    - Välj att **exportera alla** blob-data i lagringskontot.

         ![Exportera alla](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Du kan ange vilka behållare och blobbar som ska exporteras.
        - **Så här anger du en blob att exportera**: Använd väljaren **Lika med.** Ange den relativa sökvägen till blobben, som börjar med behållarnamnet. Använd *$root* för att ange rotbehållaren.
        - **Så här anger du alla blobbar som börjar med ett prefix**: Använd väljaren **Starts With.** Ange prefixet, som börjar med ett snedstreck för framåt/. Prefixet kan vara prefixet för behållarnamnet, det fullständiga behållarnamnet eller det fullständiga behållarnamnet följt av prefixet med blobnamnet. Du måste ange blob-sökvägarna i giltigt format för att undvika fel under bearbetningen, vilket visas i den här skärmbilden. Mer information finns i [Exempel på giltiga blob-sökvägar](#examples-of-valid-blob-paths).

           ![Exportera valda behållare och blobbar](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Du kan exportera från blob-listfilen.

        ![Exportera från blob-listfil](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Om bloben som ska exporteras används under datakopiering, tar Azure Import/Export-tjänsten en ögonblicksbild av blobben och kopierar ögonblicksbilden.

6. I **Retur frakt info:**

    - Välj transportören i listrutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box `adbops@microsoft.com` Operations team på med information om den operatör du planerar att använda.
    - Ange ett giltigt operatörskontonummer som du har skapat med den operatören. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när exportjobbet är klart.
    - Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, stad, zip, stat/provins och land/region.

        > [!TIP]
        > I stället för att ange en e-postadress för en enskild användare anger du ett gruppmeddelande. Detta säkerställer att du får aviseringar även om en administratör lämnar.

7. **Sammanfattningsvis:**

    - Gå igenom detaljerna i jobbet.
    - Anteckna jobbnamnet och ange Azure datacenter-leveransadress för leveransdiskar till Azure.

        > [!NOTE]
        > Skicka alltid diskarna till det datacenter som anges i Azure-portalen. Om diskarna levereras till fel datacenter bearbetas inte jobbet.

    - Klicka på **OK** för att slutföra skapandet av exportjobb.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Steg 2: Skicka enheterna

Om du inte vet hur många enheter du behöver går du till [Kontrollera antalet enheter](#check-the-number-of-drives). Om du vet hur många enheter, fortsätt att skicka enheterna.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Steg 3: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Steg 4: Ta emot diskarna

När instrumentpanelen rapporterar att jobbet är klart levereras diskarna till dig och spårningsnumret för leveransen är tillgängligt på portalen.

1. När du har fått enheterna med exporterade data måste du hämta BitLocker-tangenterna för att låsa upp enheterna. Gå till exportjobbet i Azure-portalen. Klicka på **fliken Importera/exportera.**
2. Markera och klicka på exportjobbet i listan. Gå till **Kryptering** och kopiera nycklarna.

   ![Visa BitLocker-nycklar för exportjobb](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. Använd BitLocker-tangenterna för att låsa upp diskarna.

Exporten är klar.

## <a name="step-5-unlock-the-disks"></a>Steg 5: Lås upp diskarna

Om du använder version 1.4.0.300 av WAImportExport-verktyget använder du följande kommando för att låsa upp enheten:

    `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`  

Om du använder tidigare versioner av verktyget använder du dialogrutan BitLocker för att låsa upp enheten.

Just nu kan du ta bort jobbet eller lämna det. Jobb tas automatiskt bort efter 90 dagar.

## <a name="check-the-number-of-drives"></a>Kontrollera antalet enheter

Det här *valfria* steget hjälper dig att bestämma antalet enheter som krävs för exportjobbet. Utför det här steget på ett Windows-system som kör en [OS-version som stöds](storage-import-export-requirements.md#supported-operating-systems).

1. [Hämta VERSION 1 för WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) på Windows-systemet.
2. Packa upp till standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.
3. Öppna ett PowerShell- eller kommandoradsfönster med administratörsbehörighet. Om du vill ändra katalogen till mappen som inte har uppackats kör du följande kommando:

    `cd C:\WaImportExportV1`

4. Om du vill kontrollera antalet diskar som krävs för de valda blobbar kör du följande kommando:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametrarna beskrivs i följande tabell:

    |Kommandoradsparameter|Beskrivning|  
    |--------------------------|-----------------|  
    |**/logdir:**|Valfri. Loggkatalogen. Utförliga loggfiler skrivs till den här katalogen. Om inget anges används den aktuella katalogen som loggkatalog.|  
    |**/sn:**|Krävs. Namnet på lagringskontot för exportjobbet.|  
    |**/sk:**|Krävs endast om en behållar-SAS inte har angetts. Kontonyckeln för lagringskontot för exportjobbet.|  
    |**/csas:**|Krävs bara om en lagringskontonyckel inte har angetts. Behållaren SAS för att lista de blobbar som ska exporteras i exportjobbet.|  
    |**/ExportBlobListFile:**|Krävs. Sökväg till XML-filen som innehåller en lista över blob-sökvägar eller blob-sökvägsprefix för de blobbar som ska exporteras. Filformatet som `BlobListBlobPath` används i elementet i åtgärden [Placera jobb](/rest/api/storageimportexport/jobs) för REST-APIN för tjänsten Import/Export.|  
    |**/DriveSize:**|Krävs. Storleken på enheter som ska användas för ett exportjobb, *t.ex.*|  

    Se ett [exempel på kommandot PreviewExport](#example-of-previewexport-command).

5. Kontrollera att du kan läsa/skriva till de enheter som ska levereras för exportjobbet.

### <a name="example-of-previewexport-command"></a>Exempel på PreviewExport-kommandon

I följande exempel `PreviewExport` visas kommandot:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

Exportblolob listfilen kan innehålla blobnamn och blobprefix, som visas här:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export Tool listar alla blobbar som ska exporteras och beräknar hur du packar dem i enheter av den angivna storleken, med hänsyn till eventuella nödvändiga omkostnader och uppskattar sedan antalet enheter som behövs för att hålla blobbar och enhetsanvändningsinformation.  

Här är ett exempel på utdata, med informationsloggar utelämnas:  

```powershell
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

## <a name="examples-of-valid-blob-paths"></a>Exempel på giltiga blob-sökvägar

I följande tabell visas exempel på giltiga blob-sökvägar:

   | Väljare | Blob-sökväg | Beskrivning |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla blobbar i lagringskontot |
   | Börjar med |/$root/ |Exporterar alla blobbar i rotbehållaren |
   | Börjar med |/bok |Exporterar alla blobbar i alla behållare som börjar med **prefixbok** |
   | Börjar med |/musik/ |Exporterar alla blobbar i **behållarmusik** |
   | Börjar med |/musik/kärlek |Exporterar alla blobbar i **behållarmusik** som börjar med **prefixkärlek** |
   | Lika med |$root/logo.bmp |Exporterar blob **logo.bmp** i rotbehållaren |
   | Lika med |videor/story.mp4 |Exporterar blob **story.mp4** i **behållarvideor** |

## <a name="next-steps"></a>Nästa steg

- [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
- [Granska import-/exportkrav](storage-import-export-requirements.md)
