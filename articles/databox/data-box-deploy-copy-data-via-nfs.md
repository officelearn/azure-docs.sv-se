---
title: Kopiera data till Microsoft Azure Data Box via NFS | Microsoft Docs
description: Lär dig hur du kopierar data till Azure Data Box via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 423db264c8035f9b089524eb4b19a13baccdf2e0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404713"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Självstudier: Kopiera data till Azure Data Box via NFS

Den här självstudien beskriver hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förutsättningar
> * Ansluta till Data Box
> * Kopiera data till Data Box

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en anslutning på 10 GbE inte är tillgänglig kan en datalänk på 1 GbE användas, men i så fall påverkas kopieringshastigheten. 

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Utifrån det lagringskontot som väljs skapar Data Box upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premium storage. 
- En resurs för blob storage-konto. 

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

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
    
    För Mac-klienter måste du lägga till ytterligare ett alternativ på följande sätt: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-resurser är nästa steg att kopiera data. Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera virtuella hårddiskar till sidblobar. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md). 
- Om data som laddas upp av Data Box samtidigt överförs av andra program utanför Data Box, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
- Vi rekommenderar att du inte använda både SMB och NFS samtidigt eller kopierar samma data till samma mål i slutet på Azure. I sådana fall kan slutresultatet inte fastställas.
- **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av alternativen som är tillgängliga i Linux är [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

Om du använder rsync-alternativet för en flertrådig kopia följer du dessa riktlinjer:

 - Installera **CIFS Utils**- eller **NFS Utils**-paketet, beroende på vilket filsystem din Linux-klient använder.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

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

> [!IMPORTANT]
> Följande Linux-filtyper stöds inte: symboliska länkar, filer, filer, sockets och pipes. De här filtyperna leder till fel under den **Förbered för att skicka** steg.

- För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
   ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Förutsättningar
> * Ansluta till Data Box
> * Kopiera data till Data Box


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)

