---
title: Använda Azure Import/Export för att överföra data till Azure Files | Microsoft-dokument
description: Lär dig hur du skapar importjobb i Azure-portalen för att överföra data till Azure-filer.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268306"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Importera data till Azure Files med Import/Export-tjänsten i Azure

Den här artikeln innehåller steg-för-steg-instruktioner om hur du använder Azure Import/Export-tjänsten för att på ett säkert sätt importera stora mängder data till Azure-filer. För att importera data kräver tjänsten att du skickar diskenheter som stöds som innehåller dina data till ett Azure-datacenter.  

Tjänsten Import/Export stöder endast import av Azure-filer till Azure Storage. Det går inte att exportera Azure-filer.

## <a name="prerequisites"></a>Krav

Innan du skapar ett importjobb för att överföra data till Azure-filer bör du noggrant granska och fylla i följande lista över förutsättningar. Du måste:

- Har en aktiv Azure-prenumeration att använda med tjänsten Import/Export.
- Har minst ett Azure Storage-konto. Se listan över [lagringskonton och lagringstyper som stöds för tjänsten Import/Export](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto](storage-account-create.md).
- Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
- Har ett Windows-system som kör en [OS-version som stöds](storage-import-export-requirements.md#supported-operating-systems).
- [Hämta VERSION 2 för WAImportExport](https://aka.ms/waiev2) på Windows-systemet. Packa upp till standardmappen `waimportexport`. Till exempel `C:\WaImportExport`.
- Har ett FedEx/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL `adbops@microsoft.com`kontaktar du Azure Data Box Operations team på .  
    - Kontot måste vara giltigt, ha saldo och måste ha returfraktkapacitet.
    - Generera ett spårningsnummer för exportjobbet.
    - Varje jobb ska ha ett separat spårningsnummer. Flera jobb med samma spårningsnummer stöds inte.
    - Om du inte har ett operatörskonto går du till:
        - [Skapa ett FedEX-konto](https://www.fedex.com/en-us/create-account.html)eller
        - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information som enhetsserienummer, krypteringsnyckel och lagringskontoinformation.

Utför följande steg för att förbereda enheterna.

1. Anslut våra hårddiskar till Windows-systemet via SATA-kontakter.
2. Skapa en enda NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
3. Ändra *filen dataset.csv* i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda lägger du till poster i filen *dataset.csv* som liknar följande exempel.  

   - **Så här importerar du en fil:** I följande exempel finns de data som ska kopieras i F:-enheten. Filen *MyFile1.txt* kopieras till roten till *MyAzureFileshare1*. Om *MyAzureFileshare1* inte finns skapas den i Azure Storage-kontot. Mappstrukturen bibehålls.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Så här importerar du en mapp**: Alla filer och mappar under *MyFolder2* kopieras rekursivt till fildelning. Mappstrukturen bibehålls.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Flera poster kan göras i samma fil som motsvarar mappar eller filer som importeras.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Läs mer om [hur du förbereder CSV-filen för datauppsättningen](storage-import-export-tool-preparing-hard-drives-import.md).


4. Ändra *filen driveset.csv* i rotmappen där verktyget finns. Lägg till poster i filen *driveset.csv* som liknar följande exempel. Diskuppsättningsfilen har en lista över diskar och motsvarande enhetsbeteckningar så att verktyget korrekt kan välja listan över diskar som ska förberedas.

    Det här exemplet förutsätter att två diskar är kopplade och grundläggande NTFS-volymer G:\ och H:\ skapas. H:\är inte krypterad medan G: redan är krypterad. Verktyget formaterar och krypterar disken som är värd för H:\ endast (och inte\)G: .

   - **För en disk som inte är krypterad:** Ange *Kryptera* för att aktivera BitLocker-kryptering på disken.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **För en disk som redan är krypterad:** Ange *redanKrypterad* och ange BitLocker-tangenten.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Flera poster kan göras i samma fil som motsvarar flera enheter. Läs mer om [hur du förbereder driveet CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md).

5. Använd `PrepImport` alternativet för att kopiera och förbereda data till diskenheten. För den första kopieringssessionen för att kopiera kataloger och/eller filer med en ny kopieringssession kör du följande kommando:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Ett importexempel visas nedan.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. En journalfil med namnet `/j:` som du har angett med parametern skapas för varje körning på kommandoraden. Varje enhet som du förbereder har en journalfil som måste överföras när du skapar importjobbet. Enheter utan journalfiler bearbetas inte.

    > [!IMPORTANT]
    > - Ändra inte data på diskenheterna eller journalfilen när diskförberedelserna har slutförts.

Ytterligare exempel finns i [Exempel för journalfiler](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

Utför följande steg för att skapa ett importjobb i Azure-portalen.
1. Logga in https://portal.azure.com/på .
2. Gå till **Alla tjänster > lagring > importera/exportera jobb**.

    ![Gå till Importera/exportera](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **Skapa import-/exportjobb**.

    ![Klicka på Importera/exportera jobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna:**

    - Välj **Importera till Azure**.
    - Ange ett beskrivande namn för importjobbet. Använd det här namnet för att spåra dina jobb medan de pågår och när de är klara.
        -  Det här namnet får bara innehålla gemener, siffror, bindestreck och understreck.
        -  Namnet måste börja med en bokstav och får inte innehålla blanksteg.
    - Välj en prenumeration.
    - Välj en resursgrupp.

        ![Skapa importjobb - Steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **Jobbinformation:**

    - Ladda upp de journalfiler som du skapade under föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives).
    - Välj det lagringskonto som data ska importeras till.
    - Avlämningsplatsen fylls automatiskt i baserat på regionen för det valda lagringskontot.

       ![Skapa importjobb - Steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **Retur frakt info:**

    - Välj transportören i listrutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box `adbops@microsoft.com` Operations team på med information om den operatör du planerar att använda.
    - Ange ett giltigt operatörskontonummer som du har skapat med den operatören. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när importjobbet är klart.
    - Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, stad, zip, stat/provins och land/region.

        > [!TIP]
        > I stället för att ange en e-postadress för en enskild användare anger du ett gruppmeddelande. Detta säkerställer att du får aviseringar även om en administratör lämnar.

       ![Skapa importjobb - Steg 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. I **sammanfattningen:**

    - Ange azure-dataleveransadressen för leveransdiskar tillbaka till Azure. Se till att jobbnamnet och den fullständiga adressen nämns på fraktetiketten.
    - Klicka på **OK** för att slutföra skapandet av arbetstillfällen.

        ![Skapa importjobb - Steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: Skicka enheterna till Azure-datacentret

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: Verifiera dataöverföring till Azure

Spåra jobbet till slutförande. När jobbet är klart kontrollerar du att dina data har överförts till Azure. Ta bort lokala data först efter att du har verifierat att överföringen lyckades.

## <a name="samples-for-journal-files"></a>Exempel på journalfiler

Om du vill **lägga till fler enheter**skapar du en ny driveset-fil och kör kommandot enligt nedan.

För efterföljande kopieringssessioner till de olika diskenheterna än vad som anges i *Filen InitialDriveset .csv* anger du en ny driveset.csv-fil och anger den som ett värde för parametern *.csv* `AdditionalDriveSet`. Använd **samma journalfilnamn** och ange ett **nytt sessions-ID**. Formatet för AdditionalDriveset CSV-filen är samma som InitialDriveSet-format.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Ett importexempel visas nedan.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Om du vill lägga till ytterligare data i samma enhetsuppsättning använder du kommandot PrepImport för efterföljande kopieringssessioner för att kopiera ytterligare filer/katalog.

För efterföljande kopieringssessioner till samma hårddiskar som anges i filen *InitialDriveset.csv* anger du **samma journalfilnamn** och anger ett **nytt sessions-ID.** det finns ingen anledning att ange lagringskontonyckeln.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Ett importexempel visas nedan.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Nästa steg

* [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
* [Granska import-/exportkrav](storage-import-export-requirements.md)
