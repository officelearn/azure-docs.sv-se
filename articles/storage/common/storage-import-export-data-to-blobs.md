---
title: Använda Azure Import/Export för att överföra data till Azure BLOB | Microsoft Docs
description: Lär dig hur du skapar import och export-jobb i Azure portal för att överföra data till och från Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e60a58a8d2f1c69728a2d049fe1414ca1997893e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479311"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Använda Azure Import/Export-tjänsten för att importera data till Azure Blob Storage

Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att importera stora mängder data på ett säkert sätt till Azure Blob storage. Om du vill importera data till Azure Blobs, måste tjänsten du skicka krypterade hårddiskar som innehåller dina data till ett Azure-datacenter.  

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du skapar ett importjobb för att överföra data till Azure Blob Storage måste du noggrant granska och slutför följande lista över kraven för den här tjänsten. Måste du:

- Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
- Har minst en Azure Storage-konto med en lagringsbehållare. Se en lista över [storage-konton och lagringstyper stöds för tjänsten Import/Export](storage-import-export-requirements.md). 
    - Information om hur du skapar ett nytt lagringskonto finns i [hur du skapar ett Lagringskonto](storage-quickstart-create-account.md). 
    - För information om storage-behållare, går du till [skapa en lagringsbehållare](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks). 
- Har ett Windows-system som kör en [stöd för OS-version](storage-import-export-requirements.md#supported-operating-systems). 
- Aktivera BitLocker på Windows-system. Se [hur du aktiverar BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Ladda ned WAImportExport version 1](https://aka.ms/waiev1) på Windows-system. Packa upp i standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.
- Ha ett FedEx/DHL-konto. Om du vill använda en operatör än FedEx/DHL kontakta Azure Data Box Operations-teamet på `adbops@microsoft.com`.  
    - Kontot måste vara giltig, bör ha saldo och måste ha returfrakt funktioner.
    - Generera en spårningsnummer för export-jobbet.
    - Alla jobb bör ha en separat spårningsnummer. Flera jobb med samma Spårningsnumret stöds inte.
    - Om du inte har en transportföretagskonto går du till:
        - [Skapa ett konto för FedEX](https://www.fedex.com/en-us/create-account.html), eller 
        - [Skapa ett konto för DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbereda enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information, till exempel serienumret för enheten och krypteringsnyckeln lagringskontouppgifter. 

Utför följande steg för att förbereda enheterna.

1.  Ansluta dina enheter till Windows-systemet via SATA-kopplingar.
1.  Skapa en enskild NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
2.  Aktivera BitLocker-kryptering på NTFS-volym. Om du använder ett Windows Server-system, följ instruktionerna i [hur du aktiverar BitLocker på Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopiera data till krypterade volymen. Använd dra och släppa eller Robocopy eller sådana kopieringsverktyget.
4.  Öppna ett fönster med PowerShell eller Kommandotolken med administratörsbehörighet. Om du vill ändra katalogen till mappen uppzippade, kör du följande kommando:
    
    `cd C:\WaImportExportV1`
5.  Hämta BitLocker-nyckel på enheten genom att köra följande kommando:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Kör följande kommando för att förbereda disken. **Det kan ta flera timmar och dagar beroende på storleken på data.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    En journalfil skapas i samma mapp där du körde verktyget. Två filer skapas också – en *.xml* fil (mapp där du kör verktyget) och en *enhet manifest.xml* fil (mapp där data finns).
    
    I följande tabell beskrivs de parametrar som används:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j:     |Namnet på journal-fil med tillägget .jrn. En journalfil genereras per enhet. Vi rekommenderar att du använder disk-serienummer som namn på filen.         |
    |/ ID:     |Sessions-ID. Använd ett unikt sessions-nummer för varje instans av kommandot.      |
    |/sk:     |Azure Storage-kontonyckel.         |
    |/t:     |Enhetsbeteckningen för disken som ska levereras. Till exempel enhet `D`.         |
    |/bk:     |BitLocker-nyckel för enheten. Dess numeriskt lösenord från utdata för `manage-bde -protectors -get D:`      |
    |/srcdir:     |Enhetsbeteckningen för disken som ska levereras följt av `:\`. Till exempel `D:\`.         |
    |/dstdir:     |Namnet på Målbehållaren i Azure Storage.         |
    |/skipwrite:     |Det alternativ som anger att det finns inga nya data som krävs för att kopiera och befintliga data på disken är förberedas.          |
7. Upprepa föregående steg för varje disk som ska levereras. En journalfil med det angivna namnet skapas för varje körning av kommandoraden.
    
    > [!IMPORTANT]
    > - Tillsammans med journalfil, en `<Journal file name>_DriveInfo_<Drive serial ID>.xml` fil skapas också i samma mapp där verktyget finns. XML-filen används i stället journalfil när du skapar ett jobb om ändringsjournalen-filen är för stor. 

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

Utför följande steg för att skapa ett importjobb i Azure-portalen.

1. Logga in på https://portal.azure.com/.
2. Gå till **alla tjänster > Storage > Import/export-jobb**. 
    
    ![Gå till Import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa Import/export-jobbet**.

    ![Klicka på Skapa Import/export-jobbet](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

   - Välj **importera till Azure**.
   - Ange ett beskrivande namn för importjobbet. Använd namnet för att följa förloppet för dina jobb.
       - Namnet får innehålla endast gemener, siffror och bindestreck.
       - Namnet måste börja med en bokstav och får inte innehålla blanksteg.
   - Välj en prenumeration.
   - Ange eller välj en resursgrupp.  

     ![Skapa importjobb – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **Jobbdetaljer**:

    - Ladda upp journalfiler för enheten som du fick vid förberedelsen för enheten. Om `waimportexport.exe version1` har använt, ladda upp en fil för varje enhet som du förberedde. Om ändringsjournalen filstorleken överskrider 2 MB, så kan du använda den `<Journal file name>_DriveInfo_<Drive serial ID>.xml` också skapats med journal-fil. 
    - Välj mållagringskontot var data kommer att finnas. 
    - Dropoff platsen fylls i automatiskt baserat på regionen som det valda lagringskontot.
   
   ![Skapa importjobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **returnera leveransinformation**:

   - Välj vilken operatör i listrutan. Om du vill använda en operatör än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box Operations team på `adbops@microsoft.com` med information om vilken operatör du tänker använda.
   - Ange en giltig transportföretagets kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter till dig när importjobbet har slutförts. Om du inte har ett kontonummer, skapar du en [FedEx](https://www.fedex.com/us/oadr/) eller [DHL](http://www.dhl.com/) transportföretagskonto.
   - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region. 
        
       > [!TIP] 
       > Ange en gruppens e-post istället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar företaget.

     ![Skapa importjobb – steg3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. I den **sammanfattning**:

   - Granska Jobbinformationen i sammanfattningen. Anteckna Jobbnamnet på och Azure-datacentret leveransadress för att skicka tillbaka diskarna till Azure. Den här informationen används senare på adressetikett.
   - Klicka på **OK** skapa importjobbet.

     ![Skapa importjobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Steg 3: Leverera enheterna 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: Kontrollera datauppladdning till Azure

Spåra jobbet kan slutföras. När jobbet har slutförts, kan du kontrollera att dina data har överförts till Azure. Ta bort lokalt data endast när du har kontrollerat att överföringen har slutförts.

## <a name="next-steps"></a>Nästa steg

* [Visa status för jobbet och enhet](storage-import-export-view-drive-status.md)
* [Granska kraven för Import/Export](storage-import-export-requirements.md)


