---
title: Kopiera data till Microsoft Azure Data Box | Microsoft Docs
description: Lär dig hur du kopierar data till Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: alkohli
ms.openlocfilehash: 9bc84a9b08c4cfbdf7f24416c923e0dbd7076556
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161938"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Självstudie: Kopiera data till Azure Data Box 

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
    - Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har minst en 10 GbE anslutning. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas med kopieringshastigheten påverkas. 

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Utifrån det lagringskontot som väljs skapar Data Box upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premium- eller bloblagringskonto. 

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

Se följande exempel. 

- Lagringskonto: *Mystoracct*
- Resurs för blockblob: *Mystoracct_BlockBlob/my-container/blob*
- Resurs för sidblob: *Mystoracct_PageBlob/my-container/blob*
- Resurs för fil: *Mystoracct_AzFile/my-share*

Beroende på om din Data Box är ansluten till en Windows Server-värddator eller till en Linux-värd kan stegen för att ansluta och kopiera skilja sig.

### <a name="connect-via-smb"></a>Ansluta via SSH 

Om du använder en Windows Server-värddator utför du stegen nedan för att ansluta till Data Box.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto. 

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Använd de resurser som är associerade med ditt lagringskonto (Mystoracct i följande exempel). Få åtkomst till resurserna via sökvägen `\\<IP of the device>\ShareName`. Beroende på ditt dataformat ansluter du till resurserna (använd resursnamnet) på följande adress: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Om du vill ansluta till resurserna via värddatorn öppnar du ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Klicka på **OK**. Utforskaren öppnas.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. Du bör nu kunna se resurserna som mappar. Skapa en mapp för de filer du vill kopiera (mallar i det här fallet). Ibland kan mapparna visas med ett grått kors. Korset anger inte ett feltillstånd. Mapparna flaggas av programmet för att spåra statusen.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Ansluta via NFS 

Om du använder en Linux-värddator utför du stegen nedan för att konfigurera Data Box att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**. 

    ![Konfigurera NFS-klientåtkomst 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution. 

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-resurs. Data Box-enhetens IP-adress är `10.161.23.130`, resursen `Mystoracct_Blob` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-resurser är nästa steg att kopiera data. Tänk över följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md). 
- Om data som laddas upp av Data Box samtidigt överförs av andra program utanför Data Box, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
- Vi rekommenderar att du inte använda både SMB och NFS samtidigt eller kopierar samma data till samma mål i slutet på Azure. I sådana fall kan slutresultatet inte fastställas.

### <a name="copy-data-via-smb"></a>Kopiera data via SMB

När du har anslutit till SMB-resursen kan du initiera en datakopiering. 

Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. Ange följande kommando:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
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

### <a name="copy-data-via-nfs"></a>Kopiera data via NFS

Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av alternativen som är tillgängliga i Linux är [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

Om du använder rsync-alternativet för en flertrådig kopia följer du dessa riktlinjer:

 - Installera **CIFS Utils**- eller **NFS Utils**-paketet, beroende på vilket filsystem din Linux-klient använder.

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

 -  Installera **Rsync** och **Parallel** (varierar beroende på distribuerad Linux-version).

    `sudo apt-get install rsync`
    `sudo apt-get install parallel` 

 - Skapa en monteringspunkt.

    `sudo mkdir /mnt/databox`

 - Montera volymen.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Spegla mappkatalogstrukturen.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Kopiera filerna. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     där j anger antal parallelliseringar, X = antal parallella kopior

     Vi rekommenderar att du börjar med 16 parallella kopior och öka antalet trådar beroende på tillgängliga resurser.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

Det sista steget är att förbereda enheten för att skickas. I det här steget frånkopplas alla enhetsresurser. Det går inte att komma åt resurserna när du börjar förbereda enheten för att skickas.
1. Gå till **Förbered för att skicka** och klicka på **Starta förberedelser**. 
   
    ![Förbered för att skicka 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Som standard beräknas kontrollsummor infogat under leveransförberedelserna. Beräkningen av kontrollsumma kan ta lite tid beroende på storleken på data. Klicka på **Starta förberedelser**.
    1. Enhetsresurserna kopplas från och enheten låses när vi förbereder för att skicka.
        
        ![Förbered för att skicka 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. Enhetsstatusen uppdateras till *Klar för leverans* när enhetsförberedelserna är klara. 
        
        ![Förbered för att skicka 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Ladda ned listan över filer (manifest) som kopierades i den här processen. Du kan senare använda den här listan för att verifiera filerna som har laddats upp till Azure.
        
        ![Förbered för att skicka 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Stäng av enheten. Gå till sidan **Stäng av eller starta om** och klicka på **Stäng av**. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.
4. Ta bort kablarna. Nästa steg är att skicka enheten till Microsoft.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Ansluta till Data Box
> * Kopiera data till Data Box
> * Förbereda för att skicka Data Box

Gå vidare till nästa självstudiekurs och lär dig hur du konfigurerar och kopierar data på din Data Box.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)

