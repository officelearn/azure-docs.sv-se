---
title: Azure VMware-lösning – migrering med Azure Data Box
description: Använda Azure Data Box för att överföra data till Azure VMware-lösningen.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f368ad7cf9b83195e35a2283de7a3644cc9fc317
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019765"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrera data till Azure VMware-lösning med hjälp av Azure Data Box

Med moln lösningen Microsoft Azure Data Box-enhet kan du skicka terabyte (TBs) av data till Azure på ett snabbt, Billigt och tillförlitligt sätt. Den säkra dataöverföringen påskyndas genom att skicka en upphovsrättsskyddad Data Box-lagringsenhet. Varje lagrings enhet har en maximal användbar lagrings kapacitet på 80 TB och transporteras till ditt data Center av en regional bärvåg. Enheten har ett robust hölje för att skydda och skydda dina data under överföringen.

Med hjälp av Data Box-enhet kan du överföra dina VMware-data till ditt moln privata moln. Data från din lokala VMware vSpheres miljö kopieras till Data Box-enhet via NFS-protokollet (Network File System). Med datamigreringen för flera data kan du spara en tidpunkts kopia av virtuella datorer, konfiguration och tillhör ande data till Data Box-enhet och sedan leverera den manuellt till Azure.

I den här artikeln får du lära dig om:

* Konfigurera Data Box-enhet.
* Kopiera data från den lokala VMware-miljön till Data Box-enhet via NFS.
* Förbereder för att returnera Data Box-enhet.
* Förbereda BLOB-data för kopiering till Azure VMware-lösning.
* Kopiera data från Azure till ditt moln privata moln.

## <a name="scenarios"></a>Scenarier

Använd Data Box-enhet i följande scenarier för migrering av datareplikering:

* För att migrera en stor mängd data från lokal till Azure VMware-lösning. Den här metoden upprättar en bas linje och synkroniserar skillnader över nätverket.
* För att migrera ett stort antal virtuella datorer som är inaktiverade (kall virtuella datorer).
* För att migrera data för virtuella datorer för att konfigurera utvecklings-och test miljöer.
* För att migrera ett stort antal mallar för virtuella datorer, ISO-filer och virtuella dator diskar.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrol lera kraven och [beställ data Box-enhet](../databox/data-box-deploy-ordered.md) via din Azure Portal. Under beställnings processen måste du välja ett lagrings konto som aktiverar Blob Storage. När du har fått Data Box-enhet-enheten ansluter du den till ditt lokala nätverk och [konfigurerar enheten](../databox/data-box-deploy-set-up.md) med en IP-adress som kan kommas åt från ditt vSphere-hanterings nätverk.

* Skapa ett virtuellt nätverk och ett lagrings konto i samma region som din Azure VMware-lösning har tillhandahållits.

* Skapa en [virtuell Azure-nätverksanslutning](cloudsimple-azure-network-connection.md) från ditt moln privata moln till det virtuella nätverk där lagrings kontot skapas genom att följa stegen i [ansluta Azure Virtual Network till AVS med ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Konfigurera Data Box-enhet för NFS

Anslut till din Data Box-enhet lokala webb gränssnittet genom att följa stegen i avsnittet "ansluta till din enhet" i [Självstudier: kabel och Anslut till din Azure Data Box](../databox/data-box-deploy-set-up.md). Konfigurera Data Box-enhet för att tillåta åtkomst till NFS-klienter:

1. Gå till sidan **Anslut och kopiera** i det lokala webb gränssnittet. Under **NFS-inställningar**väljer du **NFS-klient åtkomst**. 

    ![Konfigurera NFS-klientåtkomst 1](media/nfs-client-access.png)

2. Ange IP-adressen för VMware ESXi värdar och välj **Lägg till**. Du kan konfigurera åtkomst för alla värdar i ditt vSphere-kluster genom att upprepa det här steget. Välj **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *rotmappen* i lagrings kontot.

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på den första nivån delar och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Använd Azure block-blobbar för att kopiera VMware-data.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montera NFS-resursen som ett data lager i ditt lokala vCenter-kluster och kopiera data

NFS-resursen från din Data Box-enhet måste monteras som ett data lager på ditt lokala vCenter-kluster eller VMware ESXi-värd för att kunna kopiera data till NFS-datalagret:

1. Logga in på din lokala vCenter-Server.

2. Högerklicka på **Data Center**, Välj **lagring**, Välj **nytt data lager**och välj sedan **Nästa**.

   ![Lägg till nytt data lager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till data lager väljer du **NFS** under **typ**.

   ![Lägg till ny data lager typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och väljer sedan **Nästa**.

   ![Lägg till ny data lager-NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på data lagret, sökvägen och servern. Du kan använda IP-adressen för din Data Box-enhet för-servern. Mappsökvägen är i `/<StorageAccountName_BlockBlob>/<ContainerName>/` format.

   ![Lägg till ny data lager-NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att data lagret ska monteras och väljer sedan **Nästa**. I ett kluster väljer du alla värdar för att säkerställa migrering av de virtuella datorerna.

   ![Lägg till nytt data lager – Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer **Slutför**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiera data till Data Box-enhet NFS-datalager

Virtuella datorer kan migreras eller klonas till det nya data lagret. Alla oanvända virtuella datorer som du vill migrera kan migreras till Data Box-enhet NFS-datalagret med alternativet **Storage vMotion** . Aktiva virtuella datorer kan klonas till Data Box-enhet NFS-datalagret.

* Identifiera och lista de virtuella datorer som kan **flyttas**.
* Identifiera och lista de virtuella datorer som måste **klonas**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Flytta en virtuell dator till ett Data Box-enhet data lager

1. Högerklicka på den virtuella dator som du vill flytta till Data Box-enhet data lager och välj sedan **migrera**.

    ![Migrera virtuell dator](media/databox-migration-vm-migrate.png)

2. Välj **endast ändra lagrings** typ för migreringen och välj sedan **Nästa**.

    ![Migrera endast virtuell dator-lagring](media/databox-migration-vm-migrate-change-storage.png)

3. Välj **data-data lager** som mål och välj sedan **Nästa**.

    ![Migrera virtuell dator – Välj data lager](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Granska informationen och välj **Slutför**.

5. Upprepa steg 1 till 4 för ytterligare virtuella datorer.

> [!TIP]
> Du kan välja flera virtuella datorer som har samma energi tillstånd (aktiverat eller inaktiverat) och migrera dem i grupp.

Den virtuella datorn kommer att migreras till NFS-datalagret från Data Box-enhet. När alla virtuella datorer har migrerats kan du stänga av (stänga av) de aktiva virtuella datorerna som förberedelse för migrering av data till Azure VMware-lösningen.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klona en virtuell dator eller en mall för virtuella datorer till Data Box-enhet data lager

1. Högerklicka på en virtuell dator eller en mall för virtuella datorer som du vill klona. Välj **klona** > **klona till den virtuella datorn**.

    ![Kloning av virtuell dator](media/databox-migration-vm-clone.png)

2. Välj ett namn för den klonade virtuella datorn eller mallen för virtuella datorer.

3. Välj den mapp där du vill placera det klonade objektet och välj sedan **Nästa**.

4. Välj det kluster eller den resurspool där du vill placera det klonade objektet och välj sedan **Nästa**.

5. Välj **data-data lager** som lagrings plats och välj sedan **Nästa**.

    ![Kloning av virtuell dator – Välj data lager](media/databox-migration-vm-clone-select-datastore.png)

6. Om du vill anpassa alternativen för det klonade objektet väljer du anpassnings alternativ och väljer sedan **Nästa**.

7. Granska konfigurationerna och välj **Slutför**.

8. Upprepa steg 1 till 7 för ytterligare virtuella datorer eller mallar för virtuella datorer.

Virtuella datorer kommer att klonas och lagras på NFS-datalagret från Data Box-enhet. När de virtuella datorerna har klon ATS kontrollerar du att de är avstängda för att förbereda data migrering till Azure VMware-lösningen.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiera ISO-filer till Data Box-enhet data lager

1. Från ditt lokala vCenter-webbgränssnitt går du till **lagring**. Välj **data-data lager** och välj sedan **filer**. Skapa en ny mapp för att lagra ISO-filer.

    ![Kopiera ISO – skapa ny mapp](media/databox-migration-create-folder.png)

2. Ange ett namn för mappen där ISO-filer ska lagras.

3. Öppna den nya mappen genom att dubbelklicka på den.

4. Välj **överför filer** och välj sedan de ISO-filer som du vill ladda upp.
    
    ![Kopiera ISO-överför filer](media/databox-migration-upload-iso.png)

> [!TIP]
> Om du redan har ISO-filer i ditt lokala data lager kan du välja filerna och **Kopiera till** för att kopiera filerna till data Box-enhet NFS-datalager.


## <a name="prepare-data-box-for-return"></a>Förbered Data Box-enhet för retur

När alla data för virtuell dator, mall för virtuell dator och alla ISO-filer kopieras till Data Box-enhet NFS-datalager, kan du koppla från data lagret från vCenter. Alla virtuella datorer och mallar för virtuella datorer måste tas bort från lagret innan du kopplar från data lagret.

### <a name="remove-objects-from-inventory"></a>Ta bort objekt från lagret

1. Från ditt lokala vCenter-webbgränssnitt går du till **lagring**. Välj **data-data lager** och välj sedan **virtuella datorer**.

    ![Ta bort virtuella datorer från lagret – inaktiverat](media/databox-migration-select-databox-vm.png)

2. Se till att alla virtuella datorer är avstängda.

3. Välj alla virtuella datorer, högerklicka och välj **ta bort från lager**.

    ![Ta bort virtuella datorer från lagret](media/databox-migration-remove-vm-from-inventory.png)

4. Välj **mallar för virtuella datorer i mappar** och upprepa sedan steg 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Ta bort Data Box-enhet NFS-datalager från vCenter

Data Box-enhet NFS-datalagret måste vara frånkopplat från VMware ESXi värdar innan du förbereder för att returnera.

1. Från ditt lokala vCenter-webbgränssnitt går du till **lagring**.

2. Högerklicka på **data-och data lager** och välj **demontera data lager**.

    ![Demontera Data Box-enhet data lager](media/databox-migration-unmount-datastore.png)

3. Välj alla ESXi-värdar där data lagret är monterat och välj **OK**.

    ![Demontera Data Box-enhet data lager – Välj värdar](media/databox-migration-unmount-datastore-select-hosts.png)

4. Granska och godkänn eventuella varningar och välj **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Förbered Data Box-enhet för retur och returnera den sedan

Följ stegen som beskrivs i artikeln [returnera Azure Data Box och kontrol lera att data överförs till Azure](../databox/data-box-deploy-picked-up.md) för att returnera data Box-enhet. Kontrol lera statusen för data kopieringen till ditt Azure Storage-konto. När statusen visas som slutförd kan du verifiera data i ditt Azure Storage-konto.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiera data från Azure Storage till Azure VMware-lösning

Data som kopieras till Data Box-enhet-enheten kommer att finnas tillgängliga på ditt Azure Storage-konto när beställnings statusen för din Data Box-enhet visas som slutförd. Data kan nu kopieras till din Azure VMware-lösning. Data i lagrings kontot måste kopieras till virtuellt San-datalagret för ditt AVS-privata moln med hjälp av NFS-protokollet. 

Kopiera först Blob Storage-data till en hanterad disk på en virtuell Linux-dator i Azure med hjälp av **AzCopy**. Gör den hanterade disken tillgänglig via NFS, montera NFS-resursen som ett data lager i ditt moln privata moln och kopiera sedan data. Den här metoden möjliggör snabbare kopiering av data till ditt moln privata moln.

### <a name="copy-data-to-your-avs-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopiera data till ditt moln privata moln med en virtuell Linux-dator och Managed disks och exportera sedan som NFS-resurs

1. Skapa en [virtuell Linux-dator](../virtual-machines/linux/quick-create-portal.md) i Azure i samma region där ditt lagrings konto har skapats och har en Azure Virtual Network-anslutning till ditt moln privata moln.

2. Skapa en hanterad disk vars lagrings kapacitet är större än mängden BLOB-data och [koppla den till den virtuella Linux-datorn](../virtual-machines/linux/attach-disk-portal.md). Om mängden BLOB-data är större än kapaciteten för den största hanterade disken som är tillgänglig, måste data kopieras i flera steg eller med hjälp av flera hanterade diskar.

3. Anslut till den virtuella Linux-datorn och montera den hanterade disken.

4. Installera [AzCopy på den virtuella Linux-datorn](../storage/common/storage-use-azcopy-v10.md).

5. Hämta data från Azure Blob Storage till den hanterade disken med AzCopy. Kommandosyntax: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`. Ersätt `<storage-account-name>` med namnet på ditt Azure Storage-konto och `<container-name>` med den behållare som innehåller data som kopieras via Data Box-enhet.

6. Installera NFS-servern på den virtuella Linux-datorn:

    - På en Ubuntu/Debian-distribution: `sudo apt install nfs-kernel-server`.
    - I en Enterprise Linux-distribution: `sudo yum install nfs-utils`.

7. Ändra behörigheten för mappen på din hanterade disk där data från Azure Blob Storage kopierades. Ändra behörigheterna för alla mappar som du vill exportera som en NFS-resurs.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Tilldela behörigheter för klientens IP-adresser för att få åtkomst till NFS-resursen genom att redigera `/etc/exports`-filen.

    ```bash
    sudo vi /etc/exports
    ```
    
    Ange följande rader i filen för varje ESXi-värd-IP för ditt AVS-privata moln. Om du skapar resurser för flera mappar ska du lägga till alla mappar.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportera NFS-resurserna med hjälp av kommandot `sudo exportfs -a`.

10. Starta om NFS-kernel-server genom att använda kommandot `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-an-avs-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montera den virtuella Linux-datorns NFS-resurs som ett data lager i ett moln för molnets privata moln vCenter och kopiera sedan data

NFS-resursen från din virtuella Linux-dator måste monteras som ett data lager i ditt molns vCenter-kluster i molnet. När den har monterats kan data kopieras från NFS-datalagret till virtuellt San-datalagret i AVS-molnet.

1. Logga in på ditt molns vCenter-Server i molnet.

2. Högerklicka på **Data Center**, Välj **lagring**, Välj **nytt data lager**och välj sedan **Nästa**.

   ![Lägg till nytt data lager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till data lager väljer du **NFS** -typ.

   ![Lägg till ny data lager typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och väljer sedan **Nästa**.

   ![Lägg till ny data lager-NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på data lagret, sökvägen och servern. Du kan använda IP-adressen för din virtuella Linux-dator för-servern. Mappsökvägen är i `/<folder>/<subfolder>/` format.

   ![Lägg till ny data lager-NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att data lagret ska monteras och väljer sedan **Nästa**. I ett kluster väljer du alla värdar för att säkerställa migrering av de virtuella datorerna.

   ![Lägg till nytt data lager – Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer sedan **Slutför**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Lägg till virtuella datorer och mallar för virtuella datorer från ett NFS-datalager till inventeringen

1. Gå till **lagring**i ditt molns vcenters vCenter-webbgränssnitt. Välj en virtuell Linux-dators NFS-datalager och välj sedan **filer**.

    ![Välj filer från NFS-datalager](media/databox-migration-datastore-select-files.png)

2. Välj en mapp som innehåller en virtuell dator eller en mall för virtuella datorer. I informations fönstret väljer du en. VMX-fil för en virtuell dator eller en. vmtx filnamnstillägget-fil för en mall för virtuella datorer.

3. Välj **Registrera VM** för att registrera den virtuella datorn i ditt molns privata moln vCenter.

    ![Registrera virtuell dator](media/databox-migration-datastore-register-vm.png)

4. Välj data Center, mapp och kluster/resurspool där du vill att den virtuella datorn ska registreras.

4. Upprepa steg 3 och 4 för alla virtuella datorer och mallar för virtuella datorer.

5. Gå till den mapp som innehåller ISO-filerna. Välj ISO-filerna och välj sedan **Kopiera för** att kopiera filerna till en mapp på ditt virtuellt San-datalager.

Mallarna för virtuella datorer och virtuella datorer är nu tillgängliga i din moln-vCenter vCenter. De här virtuella datorerna måste flyttas från NFS-datalagret till virtuellt San data lagret innan du aktiverar dem. Du kan använda alternativet **Storage vMotion** och välja virtuellt San data lager som mål för de virtuella datorerna.

Mallarna för virtuella datorer måste klonas från ditt virtuella Linux-datalager för virtuella Linux-datorer till ditt virtuellt San-datalager.

### <a name="clean-up-your-linux-virtual-machine"></a>Rensa den virtuella Linux-datorn

När alla data har kopierats till ditt moln i molnet kan du ta bort NFS-datalagret från ditt AVS-moln:

1. Se till att alla virtuella datorer och mallar flyttas och klonas till ditt virtuellt San-datalager.

2. Ta bort från inventering alla mallar för virtuella datorer från NFS-datalagret.

3. Demontera den virtuella Linux-datorns data lager från ditt molns privata moln vCenter.

4. Ta bort den virtuella datorn och den hanterade disken från Azure.

5. Om du inte vill behålla de data som överfördes av Data Box-enhet i ditt lagrings konto tar du bort Azure Storage-kontot. 
    


## <a name="next-steps"></a>Nästa steg

* Läs mer om [data Box-enhet](../databox/data-box-overview.md).
* Läs mer om olika alternativ för [att migrera arbets belastningar till ditt moln privata moln](migrate-workloads.md).
