---
title: Med hjälp av Azure Import/Export för att överföra data till Azure-filer | Microsoft Docs
description: Lär dig hur du skapar importjobb i Azure portal för att överföra data till Azure Files.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 4349b471f960e7844511c473bffcd2177a34e055
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661022"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Använda Azure Import/Export-tjänsten för att importera data till Azure-filer

Den här artikeln innehåller stegvisa instruktioner om hur du använder tjänsten Azure Import/Export för att importera stora mängder data på ett säkert sätt till Azure-filer. För att importera data måste tjänsten du leverera stöds diskenheter som innehåller dina data till ett Azure-datacenter.  

Importera och exportera tjänsten stöder bara importera av Azure-filer till Azure Storage. Exportera Azure-filer stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett importjobb för att överföra data till Azure Files noggrant granska och utför följande förutsättningar. Du måste:

- Ha en aktiv Azure-prenumeration för användning med Import/Export service.
- Ha minst ett Azure Storage-konto. Visa en lista över [storage-konton och lagringstyper som stöds för Import/Export service](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks). 
- Har ett Windows-system som kör en [stöds OS-version](storage-import-export-requirements.md#supported-operating-systems).
- [Hämta WAImportExport version 2](https://www.microsoft.com/download/details.aspx?id=55280) på Windows-systemet. Packa upp i standardmappen `waimportexport`. Till exempel `C:\WaImportExport`.


## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

Det här steget genererar en journal-fil. Journalfilen lagrar grundläggande information, till exempel serienumret för enheten, krypteringsnyckeln och information om lagringskonto.

Utför följande steg för att förbereda enheterna.

1. Vår enheter att ansluta till Windows-systemet via SATA-kopplingar.
2. Skapa en NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
3. Ändra den *dataset.csv* filen i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda, lägger du till poster i den *dataset.csv* filen liknar följande exempel.  

    - **Importera en fil**: I följande exempel att kopiera data finns på enhet C:. Filen *MyFile1.txt* kopieras till roten av den *MyAzureFileshare1*. Om den *MyAzureFileshare1* finns inte, den har skapats i Azure Storage-konto. Mappstruktur.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Så här importerar du en mapp**: alla filer och mappar under *MyFolder2* rekursivt kopieras till filresursen. Mappstruktur.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Flera poster kan göras i samma fil motsvarar mappar eller filer som har importerats. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    Lär dig mer om [förbereder dataset CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Ändra den *driveset.csv* filen i rotmappen där verktyget finns. Lägga till poster i den *driveset.csv* filen liknar följande exempel. Filen driveset med en lista över diskar och motsvarande enhetsbeteckningar så att verktyget korrekt kan hämta listan över diskar förberedas.

    Det här exemplet förutsätter att två diskar har anslutits och grundläggande NTFS-volymer G:\ och H:\ skapas. H:\is inte krypterat när G: redan är krypterad. Verktyget formaterar och krypterar disk som är värd för H:\ endast (och inte G:\).

    - **För en disk som inte är krypterad**: Ange *kryptera* att aktivera BitLocker-kryptering på disken.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **För en disk som redan är krypterad**: Ange *AlreadyEncrypted* och ange BitLocker-nyckel.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Flera poster kan göras i samma fil motsvarar flera enheter. Lär dig mer om [förbereder driveset CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Använd den `PrepImport` alternativet för att kopiera och förbereda data till diskenheten. För den första kopia sessionen att kopiera kataloger och/eller filer med en ny kopia session, kör du följande kommando:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Importera exempel visas nedan.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. En journal-fil med namnet som du tillhandahöll med `/j:` parameter, skapas för varje körning av kommandoraden. Varje enhet som du förbereder har en journal-fil som måste överföras när du skapar importjobbet. Enheter utan journal filerna inte bearbetas.

    > [!IMPORTANT]
    > - Ändra inte data på diskenheter eller journal-fil när du har slutfört förberedelse av disken.

Ytterligare exempel Gå till [prover för journalfiler](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb 

Utför följande steg för att skapa ett importjobb i Azure-portalen.
1. Logga in på https://portal.azure.com/.
2. Gå till **alla services > Storage > Import/export jobb**. 

    ![Gå till Import/export](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa Import-/ exportjobb**.

    ![Klicka på Importera och exportera jobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

    - Välj **Import till Azure**.
    - Ange ett beskrivande namn för importjobbet. Använd det här namnet om du vill spåra dina jobb när de pågår och när de har slutförts.
        -  Det här namnet kan innehålla endast små bokstäver, siffror, bindestreck och understreck.
        -  Namnet måste börja med en bokstav och får inte innehålla blanksteg. 
    - Välj en prenumeration.
    - Välj en resursgrupp. 

        ![Skapa importjobbet – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **Jobbdetaljer**:
    
    - Överför ändringsjournalen filerna som du skapade under den föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives). 
    - Välj lagringskonto som data ska importeras till. 
    - Samlingsbibliotek platsen fylls i automatiskt baserat på regionen som det lagringskonto som valts.
   
       ![Skapa importjobbet – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **returnera leverans info**:

    - Välj en operatör från den nedrullningsbara listan.
    - Ange en giltig operatör kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts. 
    - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region.

       ![Skapa importjobb - steg3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. I den **sammanfattning**:

    - Ge Azure-datacentret leveransadress för leverans av diskar tillbaka till Azure. Se till att Jobbnamn och fullständig adress nämns på etiketten leverans.
    - Klicka på **OK** slutföra importen jobb.

        ![Skapa importjobbet – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: Levereras enheter till Azure-datacenter 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="samples-for-journal-files"></a>Exempel för journalfiler

Att **lägga till fler enheter**, skapa en ny driveset-fil och kör kommandot som nedan. 

För efterföljande kopiera sessioner till olika hårddiskar än vad som anges i *InitialDriveset .csv* fil, ange en ny driveset *.csv* filen och ange den som ett värde på parametern `AdditionalDriveSet`. Använd den **samma journalfil** ge namn och en **nytt sessions-ID**. Formatet för AdditionalDriveset CSV-fil är samma som InitialDriveSet format.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Importera exempel visas nedan.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Om du vill lägga till ytterligare data i samma driveset kommandot PrepImport för efterföljande kopiera sessioner för att kopiera ytterligare filkatalogen.

För efterföljande kopiera sessioner till samma hårddiskar anges i *InitialDriveset.csv* fil, ange den **samma journalfil** ge namn och en **nytt sessions-ID**; Det finns inget behov av att ange lagringskontots åtkomstnyckel.

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


