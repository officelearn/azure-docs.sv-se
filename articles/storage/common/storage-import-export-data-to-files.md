---
title: Använda Azure import/export för att överföra data till Azure Files | Microsoft Docs
description: Lär dig hur du skapar import jobb i Azure Portal för att överföra data till Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a88cf9981d4f3a69a503c9caa56be1b5f35029f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105191"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Importera data till Azure Files med Import/Export-tjänsten i Azure

Den här artikeln innehåller stegvisa instruktioner för hur du använder Azure import/export-tjänsten för att importera stora mängder data på ett säkert sätt till Azure Files. För att importera data kräver tjänsten att du levererar disk enheter som stöds och som innehåller dina data till ett Azure-datacenter.  

Import/export-tjänsten stöder endast import av Azure Files till Azure Storage. Det finns inte stöd för att exportera Azure Files.

## <a name="prerequisites"></a>Krav

Innan du skapar ett import jobb för att överföra data till Azure Files bör du noggrant granska och slutföra följande lista över krav. Du måste:

- Ha en aktiv Azure-prenumeration som ska användas med import/export-tjänsten.
- Ha minst ett Azure Storage konto. Se listan över [lagrings konton och lagrings typer som stöds för import/export-tjänsten](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](storage-account-create.md).
- Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
- Ha ett Windows-system som kör en [operativ system version som stöds](storage-import-export-requirements.md#supported-operating-systems).
- [Ladda ned WAImportExport version 2](https://aka.ms/waiev2) på Windows-systemet. Zippa upp till standardmappen `waimportexport` . Exempelvis `C:\WaImportExport`.
- Ha ett FedEx-/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL kontaktar du Azure Data Box drifts team på `adbops@microsoft.com` .  
    - Kontot måste vara giltigt, måste ha ett saldo och måste ha funktioner för retur leverans.
    - Generera ett spårnings nummer för export jobbet.
    - Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
    - Om du inte har ett transport företags konto går du till:
        - [Skapa ett FedEX-konto](https://www.fedex.com/en-us/create-account.html)eller
        - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

I det här steget skapas en journal fil. Journal filen lagrar grundläggande information, till exempel enhets serie nummer, krypterings nyckel och lagrings konto information.

Utför följande steg för att förbereda enheterna.

1. Anslut våra disk enheter till Windows-systemet via SATA-anslutningar.
2. Skapa en enda NTFS-volym på varje enhet. Tilldela volymen en enhets beteckning. Använd inte mountpoints.
3. Ändra *dataset.csv* -filen i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda lägger du till poster i *dataset.csv* -filen som liknar följande exempel.  

   - **Så här importerar du en fil**: i följande exempel finns de data som ska kopieras på enheten F:. Filen *MyFile1.txt*  kopieras till roten i *MyAzureFileshare1*. Om *MyAzureFileshare1* inte finns skapas den i Azure Storage-kontot. Mappstrukturen upprätthålls.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Så här importerar du en mapp**: alla filer och mappar under *MyFolder2* kopieras till fileshare rekursivt. Mappstrukturen upprätthålls.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Flera poster kan göras i samma fil som motsvarar mappar eller filer som importeras.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Läs mer om hur [du förbereder data uppsättningens CSV-fil](storage-import-export-tool-preparing-hard-drives-import.md).


4. Ändra *driveset.csv* -filen i rotmappen där verktyget finns. Lägg till poster i *driveset.csv* -filen som liknar följande exempel. Driveset-filen innehåller en lista över diskar och motsvarande enhets beteckningar så att verktyget kan välja listan över diskar som ska förberedas korrekt.

    I det här exemplet förutsätts att två diskar är kopplade till och enkla NTFS-volymer G:\ och H:\ skapas. H:\is har inte krypterats medan G: redan är krypterad. Verktyget formaterar och krypterar den disk som är värd för H:\ endast (och inte G: \) .

   - **För en disk som inte är krypterad**: ange *kryptera* för att aktivera BitLocker-kryptering på disken.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **För en disk som redan är krypterad**: ange *AlreadyEncrypted* och ange BitLocker-nyckeln.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Flera poster kan göras i samma fil som motsvarar flera enheter. Lär dig mer om [att förbereda DRIVESET CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md).

5. Använd `PrepImport` alternativet för att kopiera och förbereda data till disk enheten. Kör följande kommando för att kopiera kataloger och/eller filer med en ny Copy-session för den första kopierings sessionen:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

   Ett exempel på en import visas nedan.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. En journal fil med namnet du angav med `/j:` parameter skapas för varje körning av kommando raden. Varje enhet som du förbereder har en journal fil som måste överföras när du skapar import jobbet. Enheter utan Journal-filer bearbetas inte.

    > [!IMPORTANT]
    > - Ändra inte data på disk enheterna eller journal filen när du har slutfört disk förberedelsen.

Fler exempel finns i [exempel på Journal-filer](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Steg 2: skapa ett import jobb

Utför följande steg för att skapa ett import jobb i Azure Portal.
1. Logga in på https://portal.azure.com/ .
2. Gå till **alla tjänster > lagring > import/export-jobb**.

    ![Gå till import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa import/export-jobb**.

    ![Klicka på import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

    - Välj **Importera till Azure**.
    - Ange ett beskrivande namn för import jobbet. Använd det här namnet för att spåra jobb medan de pågår och när de har slutförts.
        -  Namnet får bara innehålla gemena bokstäver, siffror, bindestreck och under streck.
        -  Namnet måste börja med en bokstav och får inte innehålla blank steg.
    - Välj en prenumeration.
    - Välj en resursgrupp.

        ![Skapa import jobb – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **jobb information**:

    - Ladda upp de Journal-filer som du skapade under föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives).
    - Välj det lagrings konto som data ska importeras till.
    - DropOff-platsen fylls i automatiskt baserat på den region där det valda lagrings kontot finns.

       ![Skapa import jobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **information om retur leverans**:

    - Välj transport företags leverantör i list rutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i list rutan. Kontakta Azure Data Box drifts teamet på `adbops@microsoft.com`  med information om den operatör som du planerar att använda.
    - Ange ett giltigt transportföretags konto nummer som du har skapat med transport företaget. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när ditt import jobb har slutförts.
    - Ange ett fullständigt och giltigt kontakt namn, telefon, e-postadress, gatuadress, ort, post, delstat/provins och land/region.

        > [!TIP]
        > Ange en grupp-e-postadress i stället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar.

       ![Skapa import jobb – steg 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. I **sammanfattningen**:

    - Ange leverans adressen till Azure-datacenter för att leverera diskar tillbaka till Azure. Se till att jobb namnet och den fullständiga adressen anges på frakt etiketten.
    - Slutför skapandet av jobb genom att klicka på **OK** .

        ![Skapa import jobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: leverera enheterna till Azure-datacentret

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: uppdatera jobbet med spårnings information

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: kontrol lera att data laddas upp till Azure

Spåra jobbet till slutfört. När jobbet har slutförts kontrollerar du att dina data har laddats upp till Azure. Ta bara bort lokala data när du har verifierat att överföringen har slutförts.

## <a name="samples-for-journal-files"></a>Exempel på Journal-filer

Om du vill **lägga till fler enheter**skapar du en ny driveset-fil och kör kommandot enligt nedan.

För efterföljande kopierings sessioner till de olika disk enheterna än vad som anges i *InitialDriveset. csv* -filen anger du en ny driveset *. csv* -fil och anger den som ett värde för parametern `AdditionalDriveSet` . Använd **samma Journal fil** namn och ange ett **nytt sessions-ID**. Formatet på AdditionalDriveset CSV-filen är samma som InitialDriveSet-formatet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Ett exempel på en import visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Om du vill lägga till ytterligare data till samma driveset använder du kommandot PrepImport för efterföljande kopierings-sessioner för att kopiera ytterligare filer/kataloger.

För efterföljande kopierings sessioner till samma hård disk enheter som anges i *InitialDriveset.csv* -filen anger du **samma Journal fil** namn och anger ett **nytt sessions-ID**. du behöver inte ange lagrings konto nyckeln.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Ett exempel på en import visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Nästa steg

* [Visa jobb-och enhets status](storage-import-export-view-drive-status.md)
* [Granska import/export-krav](storage-import-export-requirements.md)
