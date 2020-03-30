---
title: Använda Azure Import/Export för att överföra data till Azure Blobbar | Microsoft-dokument
description: Lär dig hur du skapar import- och exportjobb i Azure-portalen för att överföra data till och från Azure Blobbar.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282502"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Använda Azure Import/Export-tjänsten för att importera data till Azure Blob Storage

Den här artikeln innehåller steg-för-steg-instruktioner om hur du använder Azure Import/Export-tjänsten för att på ett säkert sätt importera stora mängder data till Azure Blob-lagring. För att importera data till Azure Blobbar kräver tjänsten att du skickar krypterade hårddiskar som innehåller dina data till ett Azure-datacenter.  

## <a name="prerequisites"></a>Krav

Innan du skapar ett importjobb för att överföra data till Azure Blob Storage bör du noggrant granska och slutföra följande lista över förutsättningar för den här tjänsten.
Du måste:

* Har en aktiv Azure-prenumeration som kan användas för tjänsten Import/Export.
* Har minst ett Azure Storage-konto med en lagringsbehållare. Se listan över [lagringskonton och lagringstyper som stöds för tjänsten Import/Export](storage-import-export-requirements.md).
  * Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto](storage-account-create.md).
  * Information om lagringsbehållare finns i [Skapa en lagringsbehållare](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
* Har ett Windows-system som kör en [OS-version som stöds](storage-import-export-requirements.md#supported-operating-systems).
* Aktivera BitLocker i Windows-systemet. Se [Så här aktiverar du BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Ladda ned den senaste VERSIONEN AV WAImportExport version 1](https://www.microsoft.com/download/details.aspx?id=42659) på Windows-systemet. Den senaste versionen av verktyget har säkerhetsuppdateringar för att tillåta ett externt skydd för BitLocker-nyckeln och den uppdaterade upplåsningslägesfunktionen.

  * Packa upp till standardmappen `waimportexportv1`. Till exempel `C:\WaImportExportV1`.
* Har ett FedEx/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL `adbops@microsoft.com`kontaktar du Azure Data Box Operations team på .  
  * Kontot måste vara giltigt, ha saldo och måste ha returfraktkapacitet.
  * Generera ett spårningsnummer för exportjobbet.
  * Varje jobb ska ha ett separat spårningsnummer. Flera jobb med samma spårningsnummer stöds inte.
  * Om du inte har ett operatörskonto går du till:
    * [Skapa ett FedEX-konto](https://www.fedex.com/en-us/create-account.html)eller
    * [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information som enhetsserienummer, krypteringsnyckel och lagringskontoinformation.

Utför följande steg för att förbereda enheterna.

1. Anslut dina hårddiskar till Windows-systemet via SATA-kontakter.
2. Skapa en enda NTFS-volym på varje enhet. Tilldela en enhetsbeteckning till volymen. Använd inte monteringspunkter.
3. Aktivera BitLocker-kryptering på NTFS-volymen. Om du använder ett Windows Server-system använder du instruktionerna i [Så här aktiverar du BitLocker på Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopiera data till krypterad volym. Använd dra och släpp eller Robocopy eller något sådant kopieringsverktyg. En journalfil (*.jrn*) skapas i samma mapp där du kör verktyget.

   Om enheten är låst och du behöver låsa upp enheten kan stegen för att låsa upp vara olika beroende på ditt användningsfall.

   * Om du har lagt till data på en förkrypterad enhet (WAImportExport-verktyget användes inte för kryptering) använder du BitLocker-tangenten (ett numeriskt lösenord som du anger) i popup-fönstret för att låsa upp enheten.

   * Om du har lagt till data på en enhet som krypterades av verktyget WAImportExport använder du följande kommando för att låsa upp enheten:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Öppna ett PowerShell- eller kommandoradsfönster med administratörsbehörighet. Om du vill ändra katalogen till mappen som inte har uppackats kör du följande kommando:

    `cd C:\WaImportExportV1`
6. Om du vill hämta BitLocker-tangenten för enheten kör du följande kommando:

    `manage-bde -protectors -get <DriveLetter>:`
7. Om du vill förbereda disken kör du följande kommando. **Beroende på datastorleken kan det ta flera timmar till dagar.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    En journalfil skapas i samma mapp där du körde verktyget. Två andra filer skapas också - en *XML-fil* (mapp där du kör verktyget) och en *drive-manifest.xml-fil* (mapp där data finns).

    De parametrar som används beskrivs i följande tabell:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j:     |Namnet på journalfilen med tillägget .jrn. En journalfil genereras per enhet. Vi rekommenderar att du använder diskserienumret som journalfilnamn.         |
    |/id:     |Sessions-ID. Använd ett unikt sessionsnummer för varje förekomst av kommandot.      |
    |/t:     |Enhetsbeteckningen på disken som ska levereras. Till exempel `D`kör .         |
    |/bk:     |BitLocker-tangenten för enheten. Dess numeriska lösenord från utdata av`manage-bde -protectors -get D:`      |
    |/srcdir:     |Enhetsbeteckningen på disken som `:\`ska levereras följt av . Till exempel `D:\`.         |
    |/dstdir:     |Namnet på målbehållaren i Azure Storage.         |
    |/blobtyp:     |Det här alternativet anger vilken typ av blobbar som du vill importera data till. För blockblobar är `BlockBlob` detta och för sidblobar `PageBlob`är det .         |
    |/skipwrite:     |Alternativet som anger att det inte finns några nya data som behöver kopieras och befintliga data på disken ska förberedas.          |
    |/enablecontentmd5:     |Alternativet när det är aktiverat, säkerställer att MD5 beräknas och anges som `Content-md5` egenskap på varje blob. Använd det här alternativet endast `Content-md5` om du vill använda fältet när data har överförts till Azure. <br> Det här alternativet påverkar inte dataintegritetskontrollen (som inträffar som standard). Inställningen ökar den tid det tar att överföra data till molnet.          |
8. Upprepa föregående steg för varje disk som måste levereras. En journalfil med det angivna namnet skapas för varje körning av kommandoraden.

    > [!IMPORTANT]
    > * Tillsammans med journalfilen `<Journal file name>_DriveInfo_<Drive serial ID>.xml` skapas även en fil i samma mapp där verktyget finns. XML-filen används i stället för journalfilen när du skapar ett jobb om journalfilen är för stor.

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

Utför följande steg för att skapa ett importjobb i Azure-portalen.

1. Logga in https://portal.azure.com/på .
2. Gå till **Alla tjänster > lagring > importera/exportera jobb**.

    ![Gå till Importera/exportera jobb](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **Skapa import-/exportjobb**.

    ![Klicka på Skapa import-/exportjobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna:**

   * Välj **Importera till Azure**.
   * Ange ett beskrivande namn för importjobbet. Använd namnet för att spåra förloppet för dina jobb.
       * Namnet får bara innehålla gemener, siffror och bindestreck.
       * Namnet måste börja med en bokstav och får inte innehålla blanksteg.
   * Välj en prenumeration.
   * Ange eller välj en resursgrupp.  

     ![Skapa importjobb - Steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. I **Jobbinformation:**

   * Ladda upp enhetsjournalfilerna som du erhöll under förberedelsesteget för enheten. Om `waimportexport.exe version1` användes laddar du upp en fil för varje enhet som du har förberett. Om journalfilstorleken överstiger 2 MB kan `<Journal file name>_DriveInfo_<Drive serial ID>.xml` du använda den som också skapats med journalfilen.
   * Välj det mållagringskonto där data ska finnas.
   * Avlämningsplatsen fylls automatiskt i baserat på regionen för det valda lagringskontot.

   ![Skapa importjobb - Steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. I **Retur frakt info:**

   * Välj transportören i listrutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box `adbops@microsoft.com` Operations team på med information om den operatör du planerar att använda.
   * Ange ett giltigt operatörskontonummer som du har skapat med den operatören. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när importjobbet är klart. Om du inte har ett kontonummer skapar du ett [FedEx-](https://www.fedex.com/us/oadr/) eller DHL-operatörskonto. [DHL](https://www.dhl.com/)
   * Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, stad, zip, stat/provins och land/region.

       > [!TIP]
       > I stället för att ange en e-postadress för en enskild användare anger du ett gruppmeddelande. Detta säkerställer att du får aviseringar även om en administratör lämnar.

     ![Skapa importjobb - Steg 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. I **sammanfattningen:**

   * Granska jobbinformationen i sammanfattningen. Anteckna jobbnamnet och Azure-datadataleveransadressen för att skicka tillbaka diskar till Azure. Den här informationen används senare på fraktetiketten.
   * Klicka på **OK** om du vill skapa importjobbet.

     ![Skapa importjobb - Steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Steg 3 (valfritt): Konfigurera kundhanterad nyckel

Hoppa över det här steget och gå till nästa steg om du vill använda Microsofts hanterade nyckel för att skydda BitLocker-nycklarna för enheterna. Om du vill konfigurera din egen nyckel för att skydda BitLocker-nyckeln följer du instruktionerna i [Konfigurera kundhanterade nycklar med Azure Key Vault för Azure Import/Export i Azure-portalen](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Steg 4: Skicka enheterna

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Steg 5: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Steg 6: Verifiera dataöverföring till Azure

Spåra jobbet till slutförande. När jobbet är klart kontrollerar du att dina data har överförts till Azure. Ta bort lokala data först efter att du har verifierat att överföringen lyckades.

## <a name="next-steps"></a>Nästa steg

* [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
* [Granska import-/exportkrav](storage-import-export-requirements.md)
