---
title: Självstudie för att kopiera data via SMB på Azure Data Box Heavy | Microsoft Docs
description: Lär dig hur du kopierar data till din Azure Data Box Heavy via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 4267b8299e13f1705b218e65b268c45bd5a658e2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240302"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Självstudier: Kopiera data till Azure Data Box Heavy via SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopiera data till Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Anslut till Data Box Heavy
> * Kopiera data till Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Du kan kopiera data från käll servern till din Data Box-enhet via SMB, NFS, REST, data kopierings tjänst eller till hanterade diskar.

I varje fall kontrollerar du att resurs-och mappnamn och data storleken följer rikt linjerna som beskrivs i [Azure Storage och data Box Heavy tjänst begränsningar](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Du har fått din Data Box Heavy och order statusen i portalen har **levererats**.
3. Du har en värddator som har de data som du vill kopiera till Data Box Heavy. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. För snabbaste kopierings hastigheter kan 2 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har 40-GbE-anslutning, rekommenderar vi att du har minst 2 10-GbE-anslutningar (en per nod).
   

## <a name="connect-to-data-box-heavy-shares"></a>Anslut till Data Box Heavy resurser

Utifrån det valda lagrings kontot skapar Data Box Heavy upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för Premium Storage.
- En resurs för Blob Storage-konto.

Dessa resurser skapas på båda noderna i enheten.

Under Block Blob-och Page BLOB-resurser:
- Enheter på den första nivån är behållare.
- Enheter på andra nivån är blobbar.

Under resurser för Azure Files:
- Enheter på den första nivån är resurser.
- Enheter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till resurserna på din Data Box Heavy och Azure Storage Sök vägs-URL: en där data har laddats upp. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Stegen för att ansluta med en Windows-eller Linux-klient skiljer sig åt.

> [!NOTE]
> Följ samma steg för att ansluta till båda noderna för enheten parallellt.

### <a name="connect-on-a-windows-system"></a>Anslut till ett Windows-system

Om du använder en Windows Server-värddator följer du dessa steg för att ansluta till Data Box Heavy.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto.

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Öppna ett kommando fönster för att få åtkomst till de resurser som är kopplade till ditt lagrings konto (*databoxe2etest* i följande exempel) från värddatorn. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Beroende på ditt dataformat är resursssökvägarna följande:
    - Azure-blockblob – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-sidblob – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Du bör nu se resurserna som mappar.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.
    
### <a name="connect-on-a-linux-system"></a>Ansluta till ett Linux-system

Om du använder en Linux-klient använder du följande kommando för att montera SMB-resursen.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` Parametern är den version av SMB som din Linux-värd stöder. Anslut rätt version i kommandot ovan.

För versioner av SMB som Data Box Heavy stöder, se [fil system som stöds för Linux-klienter](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du är ansluten till Data Box Heavy resurserna är nästa steg att kopiera data.

### <a name="copy-considerations"></a>Kopierings överväganden

Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera de virtuella hårddiskarna till en sidblob.

    Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data måste du se till att data storleken överensstämmer med storleks begränsningarna som beskrivs i [Azure Storage och data Box Heavy gränser](data-box-heavy-limits.md).
- Om data som laddas upp av Data Box Heavy, samtidigt laddas upp av andra program utanför Data Box Heavy, kan detta leda till att det inte går att överföra jobb och skadade data.
- Vi rekommenderar att du:
    - Inte använder SMB och NFS samtidigt.
    - Kopierar samma data till samma mål på Azure.
     
  I sådana fall kan slutresultatet inte fastställas.
- Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

När du har anslutit till SMB-resursen kan du påbörja en datakopiering.

1. Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. Ange följande kommando:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Attributen beskrivs i följande tabell.
    
    |Attribut  |Beskrivning  |
    |---------|---------|
    |/e      |Kopierar underkataloger, inklusive tomma kataloger.         |
    |/r:     |Anger antalet återförsök vid misslyckade kopieringar.         |
    |/w:     |Anger väntetiden mellan återförsök i sekunder.         |
    |/is     |Innehåller samma filer.         |
    |/nfl    |Anger att filnamn inte ska loggas.         |
    |/ndl    |Anger att katalognamn inte ska loggas.        |
    |/np     |Anger att förloppet för kopieringsåtgärden (antalet filer eller kataloger som kopierats hittills) inte visas. Att visa förloppet ger betydligt läge prestanda.         |
    |/MT     | Använd flertrådsteknik, 32 eller 64 trådar rekommenderas. Det här alternativet används inte med krypterade filer. Du kan behöva separera krypterade och okrypterade filer. Men enkeltrådig kopiering ger betydligt lägre prestanda.           |
    |/fft    | Används för att minska tidsstämpelkornighet för alla filsystem.        |
    |/b      | Kopierar filer i säkerhetskopieringsläge.        |
    |/z      | Kopierar filer i omstartsläge, används om miljön är instabil. Det här alternativet minskar dataflödet på grund av ytterligare loggning.      |
    | /zb    | Använder omstartsläge. Om åtkomst nekas använder det här alternativet omstartsläge. Det här alternativet minskar dataflödet på grund av kontrollpunkter.         |
    |/efsraw | Kopierar alla krypterade filer i EFS RAW-läge. Används bara med krypterade filer.         |
    |log +:\<loggfil >| Lägger till utdata till den befintliga loggfilen.|
    
 
    I följande exempel visas utdata från kommandot Robocopy för att kopiera filer till Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
    ```       

2. Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data. (Siffrorna nedan representerar bästa fall-scenarier.)

    | Plattform    | Främst små filer < 512 KB    | Huvudsakligen medium Files 512 KB-1 MB  | Främst stora filer > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box – tung | 6 Robocopy-sessioner <br> 24 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session |


    Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

3. Öppna målmappen för att visa och verifiera de kopierade filerna.

    ![Visa kopierade filer](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. När data kopieras:

    - Fil namnen, storlekarna och formatet verifieras för att säkerställa att de uppfyller Azure-objekten och lagrings gränserna samt namngivnings konventionerna för Azure-filer och behållare.
    - För att säkerställa data integriteten beräknas även kontroll summorna på samma nivå.

    Om det uppstod fel under kopieringsprocessen laddar du ned felfilerna för felsökning. Välj pilen för att ladda ned felfilerna.

    ![Hämta felfiler](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Mer information finns i [Visa fel loggar under data kopiering till data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel under data kopiering finns i [felsöka data Box Heavy problem](data-box-troubleshoot.md).

5. Öppna fel filen i anteckningar. Följande felfil visar att data inte justeras korrekt.

    ![Öppna felfilen](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    För en Page BLOB måste data vara 512 byte justerade. När dessa data har tagits bort löses felet som visas i följande skärm bild.

    ![Fel löst](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. När kopieringen är klar går du till **Visa instrument panels** sida. Kontrol lera det använda utrymmet och det lediga utrymmet på enheten.
    
    ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Upprepa stegen ovan för att kopiera data till den andra noden på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box Heavy ämnen som:

> [!div class="checklist"]
> * Anslut till Data Box Heavy
> * Kopiera data till Data Box Heavy


Gå vidare till nästa självstudie och lär dig hur du skickar Data Box Heavy tillbaka till Microsoft.

> [!div class="nextstepaction"]
> [Leverera din Azure Data Box Heavy till Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopiera data via SMB

1. Om du använder en Windows-värd använder du följande kommando för att ansluta till SMB-resurserna:

    `\\<IP address of your device>\ShareName`

2. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box.

3. Använd ett SMB-kompatibelt fil kopierings verktyg som Robocopy för att kopiera data till resurser.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Kopiera data via NFS

1. Om du använder en NFS-värd använder du följande kommando för att montera NFS-resurserna:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. För att få åtkomst till delnings behörigheter går du till **sidan anslut & kopia** i det lokala webb gränssnittet för data Box Heavy.
3. Använd `cp` eller`rsync` kommandot för att kopiera dina data. 
4. Upprepa de här stegen för att ansluta och kopiera data till den andra noden i Data Box Heavy.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Kopiera data via REST

1. Om du vill kopiera data med Data Box-enhet Blob Storage via REST-API: er kan du ansluta via *http* eller *https*.
2. Om du vill kopiera data till Data Box-enhet Blob Storage kan du använda AzCopy.
3. Upprepa de här stegen för att ansluta och kopiera data till den andra noden i Data Box Heavy.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box blob-lagring via REST](data-box-heavy-deploy-copy-data-via-rest.md)-API: er.

### <a name="copy-data-via-data-copy-service"></a>Kopiera data via tjänsten för data kopiering

1. Om du vill kopiera data med hjälp av tjänsten data kopiering måste du skapa ett jobb. I det lokala webb gränssnittet för din Data Box Heavy går du till **hantera > Kopiera Data > skapa**.
2. Fyll i parametrarna och skapa ett jobb.
3. Upprepa de här stegen för att ansluta och kopiera data till den andra noden i Data Box Heavy.

Stegvisa instruktioner finns i [Självstudier: Använd data kopierings tjänsten för att kopiera data till](data-box-heavy-deploy-copy-data-via-copy-service.md)Azure Data Box Heavy.

### <a name="copy-data-to-managed-disks"></a>Kopiera data till Managed disks

1. När du beställer Data Box Heavy enheten bör du ha valt hanterade diskar som lagrings mål.
2. Du kan ansluta till Data Box Heavy via SMB-eller NFS-resurser.
3. Du kan sedan kopiera data via SMB-eller NFS-verktyg.
4. Upprepa de här stegen för att ansluta och kopiera data till den andra noden i Data Box Heavy.

Stegvisa instruktioner finns i [Självstudier: Använd Data Box-enhet för att tung importera data som Managed disks](data-box-heavy-deploy-copy-data-from-vhds.md)i Azure.

::: zone-end


