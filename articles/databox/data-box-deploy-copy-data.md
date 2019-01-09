---
title: Kopiera data till Microsoft Azure Data Box via SMB | Microsoft Docs
description: Lär dig hur du kopierar data till Azure Data Box via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: alkohli
ms.openlocfilehash: 6349ced07385ede42b21c9a8401dd3e0a23bcfbe
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790308"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Självstudier: Kopiera data till Azure Data Box via SMB

I den här självstudiekursen beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet och sedan förbereder för att skicka Data Box.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till Data Box
> * Kopiera data till Data Box
> * Förbered för att skicka Data Box.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört [Självstudie: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas. 

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Utifrån det lagringskontot som väljs skapar Data Box upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premium- eller bloblagringskonto.

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto. 

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. För att komma åt resurser som är associerade med ditt lagringskonto (*devicemanagertest1* i följande exempel) från värddatorn öppnar du ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Beroende på ditt dataformat är resursssökvägarna följande:
    - Azure-blockblob – `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure-sidblob – `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure Files – `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Du bör nu se resurserna som mappar.
    
    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *$root*-mappen i lagringskontot.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-resurser är nästa steg att kopiera data. Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md).
- Om data som laddas upp av Data Box samtidigt överförs av andra program utanför Data Box, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
- Vi rekommenderar att du inte använda både SMB och NFS samtidigt eller kopierar samma data till samma mål i slutet på Azure. I sådana fall kan slutresultatet inte fastställas.
- Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *$root*-mappen i lagringskontot.

När du har anslutit till SMB-resursen kan du påbörja en datakopiering. Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. Ange följande kommando:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Attributen beskrivs i följande tabell.
    
|Attribut  |Beskrivning  |
|---------|---------|
|/e     |Kopierar underkataloger, inklusive tomma kataloger.         |
|/r:     |Anger antalet återförsök vid misslyckade kopieringar.         |
|/w:     |Anger väntetiden mellan återförsök i sekunder.         |
|/is     |Innehåller samma filer.         |
|/nfl     |Anger att filnamn inte ska loggas.         |
|/ndl    |Anger att katalognamn inte ska loggas.        |
|/np     |Anger att förloppet för kopieringsåtgärden (antalet filer eller kataloger som kopierats hittills) inte visas. Att visa förloppet ger betydligt läge prestanda.         |
|/MT     | Använd flertrådsteknik, 32 eller 64 trådar rekommenderas. Det här alternativet används inte med krypterade filer. Du kan behöva separera krypterade och okrypterade filer. Men enkeltrådig kopiering ger betydligt lägre prestanda.           |
|/fft     | Används för att minska tidsstämpelkornighet för alla filsystem.        |
|/b     | Kopierar filer i säkerhetskopieringsläge.        |
|/z    | Kopierar filer i omstartsläge, används om miljön är instabil. Det här alternativet minskar dataflödet på grund av ytterligare loggning.      |
| /zb     | Använder omstartsläge. Om åtkomst nekas använder det här alternativet omstartsläge. Det här alternativet minskar dataflödet på grund av kontrollpunkter.         |
|/efsraw     | Kopierar alla krypterade filer i EFS RAW-läge. Används bara med krypterade filer.         |
|log+:<LogFile>| Lägger till utdata till den befintliga loggfilen.|    
 
Följande exempel visar utdata från robocopy-kommandot för filkopiering till Data Box.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
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
       

Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data.

|    Plattform    |    Främst små filer < 512 KB                           |    Främst medelstora filer 512 KB–1 MB                      |    Främst stora filer > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy-sessioner <br> 16 trådar per session    |    3 Robocopy-sessioner <br> 16 trådar per session    |    2 Robocopy-sessioner <br> 24 trådar per session    |  |


Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

Öppna målmappen för att visa och verifiera de kopierade filerna. Om det uppstod fel under kopieringsprocessen laddar du ned felfilerna för felsökning.

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
   ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Ansluta till Data Box
> * Kopiera data till Data Box
> * Förbereda för att skicka Data Box

Gå vidare till nästa självstudie och lär dig hur du skickar din Data Box tillbaka till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)

