---
title: Självstudie för att kopiera data via SMB på Azure Data Box tung | Microsoft Docs
description: Lär dig hur du kopierar data till din Azure Data Box tung via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 1c45e06159e4c2850efa2d3ab3290647961fb7e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592423"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Självstudier: Kopiera data till Azure Data Box tung via SMB

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till Data Box-aktiverat
> * Kopiera data till Data Box tung


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box tung](data-box-deploy-set-up.md).
2. Du har fått din Data Box tung och orderstatus i portalen är **levererade**.
3. Du har en värddator som innehåller de data som du vill kopiera över till Data Box tung. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. För den snabbaste kopia hastighet användas två 40 GbE-anslutningar (en per nod) parallellt. Om du inte har tillgänglig 40 GbE-anslutning, rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod).

## <a name="connect-to-data-box-heavy-shares"></a>Ansluta till Data Box tunga resurser

Baserat på det valda lagringskontot kan skapar Data Box tung upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premium storage.
- En resurs för blob storage-konto.

Dessa resurser skapas på båda noderna i enheten.

Under block blob- och blob-resurser:
- Första nivån entiteter är behållare.
- Andra nivån entiteter är BLOB-objekt.

Under resurser för Azure Files:
- Första nivån entiteter är resurser.
- Andra nivån entiteter är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box stor och Azure Storage sökväg URL där data har överförts. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Stegen för att ansluta med hjälp av en Windows- eller Linux-klient är olika.

> [!NOTE]
> Följ samma steg för att ansluta till båda noderna i enheten parallellt.

### <a name="connect-on-a-windows-system"></a>Ansluta på ett Windows-system

Om du använder en Windows Server-värddator, följer du dessa steg för att ansluta till Data Box-tung.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto.

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Komma åt resurser som är associerade med ditt lagringskonto (*databoxe2etest* i följande exempel) från värddatorn, öppna ett kommandofönster. Skriv följande i kommandotolken:

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
    
### <a name="connect-on-a-linux-system"></a>Ansluta på ett Linux-system

Om du använder en Linux-klient använder du följande kommando för att montera SMB-resursen.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

Den `vers` parametern är versionen av SMB som har stöd för Linux-värd. Anslut lämplig version i kommandot ovan.

Versioner av SMB med stöd för Data Box-tung Se [filsystem som stöds för Linux-klienter](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box tung

När du är ansluten till Data Box tunga resurser, är nästa steg att kopiera data.

### <a name="copy-considerations"></a>Kopiera överväganden

Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera de virtuella hårddiskarna till en sidblob.

    Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data, se till att storleken på data som överensstämmer med begränsningar för meddelandestorlek som beskrivs i den [Azure storage och Data Box tung gränser](data-box-heavy-limits.md).
- Om data som överförs genom att Data Box tung, laddas samtidigt av andra program utanför Data Box tung, kan detta resultera i överföring jobbet fel och skadad data.
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
    |LOG +:\<LogFile >| Lägger till utdata till den befintliga loggfilen.|
    
 
    I följande exempel visar utdata från kommandot robocopy för att kopiera filer till Data Box-tung.

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

2. Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data. (Talen nedan representerar de bästa användningsfall.)

    | Plattform    | Främst små filer < 512 KB    | Främst medelstora filer 512 KB - 1 MB  | Främst stora filer > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box – tung | 6 Robocopy-sessioner <br> 24 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session |


    Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

3. Öppna målmappen för att visa och verifiera de kopierade filerna.

    ![Visa kopierade filer](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. När data kopieras:

    - Det filnamn, storlek och format verifieras för att säkerställa att de uppfyller Azure-objekt och Lagringsgränser samt Azure fil- och behållaren namngivningskonventioner.
    - Kontrollsumman är också beräknade infogade för att säkerställa dataintegriteten.

    Om det uppstod fel under kopieringsprocessen laddar du ned felfilerna för felsökning. Väljer du pilikonen för att hämta felfiler.

    ![Ladda ned felfiler](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Mer information finns i [visa felloggarna under Datakopieringen till Data Box tung](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel vid kopiering av data finns i [felsöka Data Box tung utfärdar](data-box-troubleshoot.md).

5. Öppna felfilen i anteckningar. Följande fel-filen anger att data inte justerats korrekt.

    ![Öppna felfilen](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Data måste vara 512 byte justerad för en sidblob. När dessa data tas bort löser felet enligt följande skärmbild.

    ![Fel har åtgärdats](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. När kopieringen är klar, går du till **Vyinstrumentpanel** sidan. Kontrollera det använda utrymmet och det lediga utrymmet på enheten.
    
    ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Upprepa stegen ovan för att kopiera data på den andra noden på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box tung ämnen som:

> [!div class="checklist"]
> * Ansluta till Data Box-aktiverat
> * Kopiera data till Data Box tung


Gå vidare till nästa självstudie och lär dig att leverera din Data Box tung till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box-aktiverat till Microsoft](./data-box-heavy-deploy-picked-up.md)

