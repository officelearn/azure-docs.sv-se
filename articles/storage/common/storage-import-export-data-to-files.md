---
title: Använda Azure Import/Export för att överföra data till Azure Files | Microsoft Docs
description: Lär dig hur du skapar importjobb i Azure portal för att överföra data till Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 1cf2276ca1995df19cc7068764a31916e4981100
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452702"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Använda Azure Import/Export-tjänsten för att importera data till Azure Files

Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att importera stora mängder data på ett säkert sätt till Azure Files. För att importera data, måste tjänsten du skicka stöds diskenheter som innehåller dina data till ett Azure-datacenter.  

Import/Export-tjänsten stöder bara importera av Azure Files till Azure Storage. Exportera Azure Files stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett importjobb för att överföra data till Azure Files noggrant granska och slutför följande lista med krav. Måste du:

- Ha en aktiv Azure-prenumeration ska användas med tjänsten Import/Export.
- Har minst en Azure Storage-konto. Se en lista över [storage-konton och lagringstyper stöds för tjänsten Import/Export](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns i [hur du skapar ett Lagringskonto](storage-quickstart-create-account.md).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks). 
- Har ett Windows-system som kör en [stöd för OS-version](storage-import-export-requirements.md#supported-operating-systems).
- [Ladda ned WAImportExport version 2](https://aka.ms/waiev2) på Windows-system. Packa upp i standardmappen `waimportexport`. Till exempel `C:\WaImportExport`.
- Ha ett FedEx/DHL-konto. 
    - Kontot måste vara giltig, bör ha saldo och måste ha returfrakt funktioner.
    - Generera en spårningsnummer för export-jobbet.
    - Alla jobb bör ha en separat spårningsnummer. Flera jobb med samma Spårningsnumret stöds inte.
    - Om du inte har en transportföretagskonto går du till:
        - [Skapa ett konto för FedEX](https://www.fedex.com/en-us/create-account.html), eller 
        - [Skapa ett konto för DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbereda enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information, till exempel serienumret för enheten och krypteringsnyckeln lagringskontouppgifter.

Utför följande steg för att förbereda enheterna.

1. Ansluta vår diskenheter till Windows-systemet via SATA-kopplingar.
2. Skapa en enskild NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
3. Ändra den *dataset.csv* filen i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda, lägger du till poster i den *dataset.csv* filen liknar följande exempel.  

    - **Att importera en fil**: I följande exempel finns data som ska kopieras i C:-enheten. Din fil *MyFile1.txt* kopieras till roten för den *MyAzureFileshare1*. Om den *MyAzureFileshare1* finns inte, den skapas i Azure Storage-kontot. Mappstruktur.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Importera en mapp**: Alla filer och mappar under *MyFolder2* rekursivt kopieras till filresursen. Mappstruktur.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Flera poster kan göras i samma fil för mappar eller filer som har importerats. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
        ```
    Läs mer om [förbereder CSV-filen datauppsättning](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Ändra den *driveset.csv* filen i rotmappen där verktyget finns. Lägga till poster i den *driveset.csv* filen liknar följande exempel. Filen driveset med en lista över diskar och motsvarande enhetsbeteckningar så att verktyget korrekt kan välja listan över diskar som ska förberedas.

    Det här exemplet förutsätter att två diskar är anslutna och grundläggande NTFS-volymer G:\ och H:\ skapas. H:\is inte krypterat när G: redan är krypterad. Verktyget formaterar och krypterar den disk som är värd för H:\ endast (och inte G:\).

    - **För en disk som inte är krypterad**: Ange *Encrypt* att aktivera BitLocker-kryptering på disken.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **För en disk som redan är krypterat**: Ange *AlreadyEncrypted* och ange BitLocker-nyckel.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Flera poster kan göras i samma fil för flera enheter. Läs mer om [förbereder CSV-filen driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Använd den `PrepImport` alternativet för att kopiera och förbereda data till diskenheten. För den första kopia-sessionen att kopiera kataloger och/eller filer med en ny kopia-session kör du följande kommando:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Importera exempel visas nedan.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. En journal-fil med namnet som du tillhandahöll med `/j:` parametern skapas för varje körning av kommandoraden. Varje enhet som du förbereder har en journalfil måste laddas upp när du skapar importjobbet. Enheter utan journalen filerna inte bearbetas.

    > [!IMPORTANT]
    > - Ändra inte data på diskenheterna eller journal-fil när du har slutfört förberedelse av disk.

Ytterligare exempel går du till [-exempel för journalfiler](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb 

Utför följande steg för att skapa ett importjobb i Azure-portalen.
1. Logga in på https://portal.azure.com/.
2. Gå till **alla tjänster > Storage > Import/export-jobb**. 

    ![Gå till Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa Import/export-jobbet**.

    ![Klicka på Import/export-jobbet](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

    - Välj **importera till Azure**.
    - Ange ett beskrivande namn för importjobbet. Du kan använda det här namnet för att spåra dina jobb medan de är håller på att skapas och när de har slutförts.
        -  Det här namnet får innehålla endast gemener, siffror, bindestreck och understreck.
        -  Namnet måste börja med en bokstav och får inte innehålla blanksteg. 
    - Välj en prenumeration.
    - Välj en resursgrupp. 

        ![Skapa importjobb – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **Jobbdetaljer**:
    
    - Ladda upp journalfiler som du skapade under det föregående [steg 1: Förbereda enheterna](#step-1-prepare-the-drives). 
    - Välj det lagringskonto som data ska importeras till. 
    - Dropoff platsen fylls i automatiskt baserat på regionen som det valda lagringskontot.
   
       ![Skapa importjobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **returnera leveransinformation**:

    - Välj vilken operatör från den nedrullningsbara listan.
    - Ange en giltig transportföretagets kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter till dig när importjobbet har slutförts. 
    - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region.

        > [!TIP] 
        > Ange en gruppens e-post istället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar företaget.

       ![Skapa importjobb – steg3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. I den **sammanfattning**:

    - Ange Azure-datacentret leveransadress för att skicka tillbaka diskarna till Azure. Se till att Jobbnamnet och fullständig adress anges på adressetikett.
    - Klicka på **OK** för att importera jobbet Skapa.

        ![Skapa importjobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: Leverera enheter till Azure-datacentret 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: Kontrollera datauppladdning till Azure

Spåra jobbet kan slutföras. När jobbet har slutförts, kan du kontrollera att dina data har överförts till Azure. Ta bort lokalt data endast när du har kontrollerat att överföringen har slutförts.

## <a name="samples-for-journal-files"></a>Exempel för journalfiler

Att **lägga till fler enheter**, skapa en ny driveset-fil och kör kommandot enligt nedan. 

För efterföljande kopia sessioner till olika hårddiskar än vad som anges i *InitialDriveset .csv* fil, ange en ny driveset *.csv* filen och anger du det som ett värde för parametern `AdditionalDriveSet`. Använd den **samma journalfil** ge namn och en **nytt sessions-ID**. Formatet på AdditionalDriveset CSV-filen är samma som InitialDriveSet format.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Importera exempel visas nedan.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Om du vill lägga till ytterligare data i samma driveset kommandot PrepImport för efterföljande kopia sessioner du kopierar ytterligare filer eller katalogen.

För efterföljande kopia sessioner till samma hårddiskar anges i *InitialDriveset.csv* fil, ange den **samma journalfil** ge namn och en **nytt sessions-ID**; Det finns inget behov att tillhandahålla lagringskontonyckeln.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Importera exempel visas nedan.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Nästa steg

* [Visa status för jobbet och enhet](storage-import-export-view-drive-status.md)
* [Granska kraven för Import/Export](storage-import-export-requirements.md)


