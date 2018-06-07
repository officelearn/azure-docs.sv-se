---
title: Med hjälp av Azure Import/Export för att överföra data till Azure BLOB | Microsoft Docs
description: Lär dig hur du skapar importera och exportera jobb i Azure-portalen för att överföra data till och från Azure BLOB.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: fe9292459134972b44037a58235cdd817030a956
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661007"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Använda tjänsten Azure Import/Export för att importera data till Azure Blob Storage

Den här artikeln innehåller stegvisa instruktioner om hur du använder tjänsten Azure Import/Export för att importera stora mängder data på ett säkert sätt till Azure Blob storage. Om du vill importera data till Azure BLOB, måste tjänsten du leverera krypterade hårddiskar som innehåller dina data till ett Azure-datacenter.  

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett importjobb för att överföra data till Azure Blob Storage noggrant granska och utför följande förutsättningar för den här tjänsten. Du måste:

- Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
- Ha minst ett Azure Storage-konto med en lagringsbehållare. Visa en lista över [storage-konton och lagringstyper som stöds för Import/Export service](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagringskonto finns [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account). För information om lagringsbehållaren, gå till [skapa en lagringsbehållare](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ha tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks). 
- Har ett Windows-system som kör en [stöds OS-version](storage-import-export-requirements.md#supported-operating-systems). 
- Aktivera BitLocker på Windows-system. Se [hur du aktiverar BitLocker](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Hämta WAImportExport version 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) på Windows-systemet. Packa upp i standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.


## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

Det här steget genererar en journal-fil. Journalfilen lagrar grundläggande information, till exempel serienumret för enheten, krypteringsnyckeln och information om lagringskonto. 

Utför följande steg för att förbereda enheterna.

1.  Ansluta diskenheterna till Windows-systemet via SATA-kopplingar.
1.  Skapa en NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
2.  Aktivera BitLocker-kryptering på NTFS-volym. Om du använder ett Windows Server-system, följ instruktionerna i [hur du aktiverar BitLocker på Windows Server 2012 R2](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopiera data till krypterade volymen. Använd dra och släppa eller Robocopy eller sådana kopia-verktyget.
4.  Öppna ett fönster med PowerShell eller Kommandotolken med administratörsbehörighet. Ändra katalogen till mappen uppackade genom att köra följande kommando:
    
    `cd C:\WaImportExportV1`
5.  Om du vill hämta BitLocker-nyckel för enheten, kör du följande kommando:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  Kör följande kommando för att förbereda disken. **Det kan ta flera timmar och dagar beroende på storleken på data.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    En journal-fil skapas i samma mapp där du körde verktyget. Två filer skapas också - en *.xml* fil (mapp där du kör verktyget) och en *enhet manifest.xml* fil (mapp där data finns).
    
    I följande tabell beskrivs de parametrar som används:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j:     |Namnet på journal-fil med filnamnstillägget .jrn. En journal-fil skapas per enhet. Vi rekommenderar att du använder disk serienumret som namn på filen.         |
    |/ID:     |sessions-ID. Ange ett unikt sessions-värde för varje instans av kommandot.      |
    |/Sk:     |Nyckel för Azure Storage-konto.         |
    |/ t:     |Enhetsbeteckning på disken för att skickas. Till exempel enhet `D`.         |
    |/BK:     |BitLocker-nyckel för enheten. Dess numeriskt lösenord från utdata från ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Enhetsbeteckningen för disken som ska levereras följt av `:\`. Till exempel `D:\`.         |
    |/dstdir:     |Namnet på målbehållare i Azure Storage.         |
    |/skipwrite:     |Det alternativ som anger att det finns inga nya data som krävs för att kopieras och befintliga data på disken är att vara förberedd.          |
7. Upprepa föregående steg för varje disk som ska levereras. En journal-fil med det angivna namnet skapas för varje körning av kommandoraden.
    
    > [!IMPORTANT]
    > - Tillsammans med journalfilen en `<Journal file name>_DriveInfo_<Drive serial ID>.xml` filen skapas också i samma mapp där verktyget finns. XML-filen används i stället för journal-fil när du skapar ett jobb om Journalfilen är för stor. 

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

Utför följande steg för att skapa ett importjobb i Azure-portalen.

1. Logga in på https://portal.azure.com/.
2. Gå till **alla services > Storage > Import/export jobb**. 
    
    ![Gå till Import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa Import-/ exportjobb**.

    ![Klicka på Skapa Import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

    - Välj **Import till Azure**.
    - Ange ett beskrivande namn för importjobbet. Använd namn för att spåra förloppet för jobbet.
        - Namnet får innehålla endast små bokstäver, siffror, bindestreck och understreck.
        - Namnet måste börja med en bokstav och får inte innehålla blanksteg.
    - Välj en prenumeration.
    - Ange eller välj en resursgrupp.  

    ![Skapa importjobbet – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. I **Jobbdetaljer**:

    - Överför journalfiler för enheten som du fick vid förberedelsen av enheten. Om `waimportexport.exe version1` har använts, ladda upp en fil för varje enhet som du har förberett. Om ändringsjournalen filstorleken överskrider 2 MB, så du kan använda den `<Journal file name>_DriveInfo_<Drive serial ID>.xml` skapas också med journal-fil. 
    - Välj destinationslagringskontot där data kommer att finnas. 
    - Samlingsbibliotek platsen fylls i automatiskt baserat på regionen som det lagringskonto som valts.
   
   ![Skapa importjobbet – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. I **returnera leverans info**:

    - Välj en operatör i listrutan.
    - Ange en giltig operatör kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts. Om du inte har ett kontonummer, skapar du en [FedEx](http://www.fedex.com/us/oadr/) eller [DHL](http://www.dhl.com/) operatör konto.
    - Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, region och land/region.

    ![Skapa importjobb - steg3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. I den **sammanfattning**:

    - Granska Jobbinformationen i sammanfattningen. Anteckna Jobbnamnet på och Azure-datacentret leveransadress för att leverera diskar tillbaka till Azure. Den här informationen används senare på etiketten leverans.
    - Klicka på **OK** att skapa importjobbet.

    ![Skapa importjobbet – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

## <a name="step-3-ship-the-drives"></a>Steg 3: Levereras enheterna 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="next-steps"></a>Nästa steg

* [Visa status för jobbet och enhet](storage-import-export-view-drive-status.md)
* [Granska kraven för Import/Export](storage-import-export-requirements.md)


