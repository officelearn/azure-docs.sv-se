---
title: Självstudie för att kopiera data från virtuella hårddiskar till hanterade diskar med Azure Data Box tung | Microsoft Docs
description: Lär dig hur du kopierar data från virtuella hårddiskar från en lokal VM-arbetsbelastningar till din Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a29cd142b3322c958f70aad8d5cad2bc30b87d76
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670841"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Självstudier: Använd Data Box tung för att importera data som hanterade diskar i Azure

Den här självstudien beskrivs hur du använder Azure Data Box tung för att migrera lokala virtuella hårddiskar till managed disks i Azure. De virtuella hårddiskarna från lokala virtuella datorer ska kopieras till Data Box tung som sidblobar och överförs till Azure som hanterade diskar. Dessa hanterade diskar kan sedan kopplas till virtuella Azure-datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Granska krav
> * Ansluta till Data Box-aktiverat
> * Kopiera data till Data Box tung


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box tung](data-box-heavy-deploy-set-up.md).
2. Du har fått din Data Box tung och orderstatus i portalen är **levererade**.
3. Du har anslutning till ett höghastighetsnätverk. För den snabbaste kopia hastighet användas två 40 GbE-anslutningar (en per nod) parallellt. Om du inte har tillgänglig 40 GbE-anslutning, rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod). 
4. Du har granskat den:

    - Stöds [hanterade diskar som är i Azure-objekt storleksgränser](data-box-heavy-limits.md#azure-object-size-limits).
    - [Introduktion till Azure hanterade diskar](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Ansluta till Data Box-aktiverat

Utifrån resursgrupperna angetts skapar Data Box tung en resurs för varje tillhörande resursgrupp per nod. Till exempel om `mydbmdrg1` och `mydbmdrg2` skapades när beställning, skapas följande resurser:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Inom varje resurs skapas följande tre mappar som motsvarar en behållare i ditt storage-konto.

- Premium SSD
- Standard HDD
- Standard SSD

I följande tabell visas UNC-sökvägar till filresurser på din Data Box tung.
 
|        Anslutningsprotokoll           |             UNC-sökvägen till resursen                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Baserat på om du använder SMB- eller NFS för att ansluta till Data Box tunga resurser, är stegen för att ansluta olika.

> [!NOTE]
> - Ansluta via REST stöds inte för den här funktionen.
> - Upprepa connect instruktionerna att ansluta till den andra noden för Data Box tung.

### <a name="connect-to-data-box-heavy-via-smb"></a>Ansluta till Data Box aktiverat via SMB

Om du använder en Windows Server-värddator, följer du dessa steg för att ansluta till Data Box-tung.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **hämta autentiseringsuppgifter för** att hämta autentiseringsuppgifterna för de filresurser som är associerade med resursgruppen. Du kan också hämta autentiseringsuppgifterna från den **enhetsinformation** i Azure-portalen.

    > [!NOTE]
    > Autentiseringsuppgifterna för alla resurser för hanterade diskar är identiska.

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Från åtkomst till resursen och kopiera data i dialogrutan som visas, kopiera den **användarnamn** och **lösenord** för resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Komma åt resurser som är associerade med din resurs (*mydbmdrg1* i följande exempel) från värddatorn, öppna ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Din UNC-sökvägar för resursen i det här exemplet är följande:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>\<ShareName>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Du bör nu se följande införande mapparna varje resursen.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Ansluta till Data Box aktiverat via NFS

Om du använder en Linux-värddator, utför du följande steg för att konfigurera enheten för att tillåta åtkomst till NFS-klienterna.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**.

    ![Konfigurera NFS-klientåtkomst 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution.

3. När NFS-klienten har installerats, använder du följande kommando för att montera NFS-resursen på din enhet:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box eller Data Box tung resurs. Data Box eller Data Box tung enheten IP-Adressen är `169.254.250.200`, resursen `mydbmdrg1_MDisk` är monterad på ubuntuVM montera punkt som `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box tung

När du är ansluten till data-server, är nästa steg att kopiera data. VHD-filen kopieras till mellanlagringskontot som sidblobb. Sidans blob är sedan konverteras till en hanterad disk och flyttas till en resursgrupp.

Granska följande innan du börjar kopiering av data:

- Kopiera alltid de virtuella hårddiskarna till någon av mapparna införande. Om du kopierar de virtuella hårddiskarna utanför dessa mappar eller i en mapp som du skapade de virtuella hårddiskarna ska överföras till Azure Storage-konto som sidblobar och hanterade inte diskar.
- Endast de fasta virtuella hårddiskarna kan laddas upp för att skapa hanterade diskar. VHDX-filer eller dynamiska och Differentierande virtuella hårddiskar stöds inte.
- Du kan bara ha en hanterad disk med ett givet namn i en resursgrupp i alla införande mappar. Detta innebär att de virtuella hårddiskarna som överförts till mapparna införande bör ha unika namn. Kontrollera att det angivna namnet inte matchar en redan befintlig hanterad disk i en resursgrupp.
- Granska hanterad disk begränsningar i [storleksbegränsningar för Azure-objekt](data-box-heavy-limits.md#azure-object-size-limits).

Beroende på om du ansluter via SMB- eller NFS, kan du använda:

- [Kopieringsdata via SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopieringsdata via NFS](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Vänta tills kopieringsjobben är klara. Se till att kopiera jobb har slutförts utan fel innan du går vidare till nästa steg.

![Inga fel på sidan **Anslut och kopiera**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Om det uppstår fel under kopieringen kan hämta loggar från den **Anslut och kopiera** sidan.

- Om du har kopierat en fil som är inte justerad för 512 byte överföra inte filen som sidblobb till mellanlagring storage-kontot. Ett fel i loggarna visas. Ta bort filen och kopiera en fil som är 512 byte justerad.

- Om du har kopierat en VHDX (dessa filer inte stöds) med ett långt namn, visas ett fel i loggarna.

    ![Fel i loggarna från ** sidan Anslut och kopiera **](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Åtgärda felen innan du fortsätter till nästa steg.

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

När kopieringsjobbet är klart kan du gå till **Förbered för att skicka**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box tung ämnen som:

> [!div class="checklist"]
> * Granska krav
> * Ansluta till Data Box-aktiverat
> * Kopiera data till Data Box tung


Gå vidare till nästa självstudie och lär dig att leverera din Data Box tung till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box-aktiverat till Microsoft](./data-box-heavy-deploy-picked-up.md)

