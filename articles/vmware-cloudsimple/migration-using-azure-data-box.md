---
title: Azure VMware-lösning – migrering med Azure Data Box
description: Så här använder du Azure Data Box för att massrendera data till Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019765"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrera data till Azure VMware-lösning med hjälp av Azure Data Box

Med molnlösningen i Microsoft Azure Data Box kan du skicka terabyte (TBs) med data till Azure på ett snabbt, billigt och tillförlitligt sätt. Den säkra dataöverföringen påskyndas genom att skicka en upphovsrättsskyddad Data Box-lagringsenhet. Varje lagringsenhet har en maximal användbar lagringskapacitet på 80 TB och transporteras till ditt datacenter av en regional operatör. Enheten har ett robust hölje för att skydda och skydda dina data under överföringen.

Genom att använda Data Box kan du massrena migrera dina VMware-data till ditt privata moln. Data från den lokala VMware vSphere-miljön kopieras till Data Box via NFS-protokollet (Network File System). Massdatamigrering innebär att spara en point-in-time-kopia av virtuella datorer, konfiguration och associerade data till Data Box och sedan manuellt skicka den till Azure.

I den här artikeln får du lära dig mer om:

* Ställa in dataruta.
* Kopiera data från den lokala VMware-miljön till datarutan via NFS.
* Förbereder för återlämnande av Data Box.
* Förbereda blob-data för kopiering till Azure VMware Solution.
* Kopiera data från Azure till ditt privata moln.

## <a name="scenarios"></a>Scenarier

Använd dataruta i följande scenarier för massmigrering av data:

* Så här migrerar du en stor mängd data från lokalt till Azure VMware-lösning. Den här metoden upprättar en baslinje och synkroniserar skillnader över nätverket.
* Så här migrerar du ett stort antal virtuella datorer som är avstängda (kalla virtuella datorer).
* Så här migrerar du data för virtuella datorer för att ställa in utvecklings- och testmiljöer.
* Så här migrerar du ett stort antal mallar för virtuella datorer, ISO-filer och virtuella datorer.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera förutsättningarna och [orderdatarutan](../databox/data-box-deploy-ordered.md) via din Azure-portal. Under orderprocessen måste du välja ett lagringskonto som aktiverar Blob-lagring. När du har tagit emot Data Box-enheten ansluter du den till det lokala nätverket och [konfigurerar enheten](../databox/data-box-deploy-set-up.md) med en IP-adress som kan nås från ditt vSphere-hanteringsnätverk.

* Skapa ett virtuellt nätverk och ett lagringskonto i samma region där din Azure VMware-lösning etableras.

* Skapa en [virtuell Azure-nätverksanslutning](cloudsimple-azure-network-connection.md) från ditt privata moln till det virtuella nätverket där lagringskontot skapas genom att följa stegen i [Anslut Azure virtuellt nätverk till CloudSimple med ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Konfigurera dataruta för NFS

Anslut till det lokala webbgränssnittet i Data Box genom att följa stegen i avsnittet "Anslut till din enhet" i [Självstudiekurs: Kabel och anslut till din Azure Data Box](../databox/data-box-deploy-set-up.md).  Konfigurera dataruta så att åtkomst till NFS-klienter får åtkomst:

1. Gå till sidan Anslut och **kopiera** i det lokala webbgränssnittet. Under **NFS-inställningar**väljer du **NFS-klientåtkomst**. 

    ![Konfigurera NFS-klientåtkomst 1](media/nfs-client-access.png)

2. Ange IP-adressen för VMware ESXi-värdarna och välj **Lägg till**. Du kan konfigurera åtkomst för alla värdar i vSphere-klustret genom att upprepa det här steget. Välj **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *rotmappen* i lagringskontot.

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure-filer är första nivånheter resurser och enheter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Använd Azure Block-blobbar för att kopiera VMware-data.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montera NFS-resursen som ett datalager i ditt lokala vCenter-kluster och kopiera data

NFS-resursen från databoxen måste monteras som ett datalager i ditt lokala vCenter-kluster eller VMware ESXi-värd för att kunna kopiera data till NFS-datalagret:

1. Logga in på din lokala vCenter-server.

2. Högerklicka på **Datacenter,** välj **Lagring,** välj **Nytt datalager**och välj sedan **Nästa**.

   ![Lägg till nytt datalager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till datalager väljer du **NFS** under **Typ**.

   ![Lägg till nytt datalager - typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och väljer sedan **Nästa**.

   ![Lägg till nytt datalager - NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på datalagret, sökvägen och servern. Du kan använda IP-adressen för din dataruta för servern. Mappsökvägen kommer att `/<StorageAccountName_BlockBlob>/<ContainerName>/` vara i formatet.

   ![Lägg till ny datalager - NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att datalagret ska monteras och väljer sedan **Nästa**.  I ett kluster väljer du alla värdar för att säkerställa migreringen av de virtuella datorerna.

   ![Lägg till nytt datalager - Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer **Slutför**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiera data till databoxen NFS-databutik

Virtuella datorer kan migreras eller klonas till det nya datalagret.  Oanvända virtuella datorer som du vill migrera kan migreras till datarute-NFS-datalagret med hjälp av alternativet **storage vMotion.** Aktiva virtuella datorer kan klonas till Data Box NFS-datalagret.

* Identifiera och lista de virtuella datorer som kan **flyttas**.
* Identifiera och lista de virtuella datorer som måste **klonas**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Flytta en virtuell dator till ett datalager för databox

1. Högerklicka på den virtuella dator som du vill flytta till datarutedatalagret och välj sedan **Migrera**.

    ![Migrera virtuell dator](media/databox-migration-vm-migrate.png)

2. Välj **Ändra lagring endast** för migreringstypen och välj sedan **Nästa**.

    ![Migrera virtuell dator - endast lagring](media/databox-migration-vm-migrate-change-storage.png)

3. Välj **Databox-Datastore** som mål och välj sedan **Nästa**.

    ![Migrera virtuell dator - välj datalager](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Granska informationen och välj **Slutför**.

5. Upprepa steg 1 till och med 4 för ytterligare virtuella datorer.

> [!TIP]
> Du kan välja flera virtuella datorer som är i samma energiläge (aktiverade eller inaktiverade) och migrera dem i grupp.

Den virtuella datorn migreras till NFS-datalagret från Data Box. När alla virtuella datorer har migrerats kan du stänga av (stänga av) de aktiva virtuella datorerna som förberedelse för migrering av data till Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klona en virtuell dator eller en mall för en virtuell dator till datalagringsbutiken Data Box

1. Högerklicka på en virtuell dator eller en mall för virtuella datorer som du vill klona. Välj **Klon klon till** > **virtuell dator**.

    ![Klon av virtuell dator](media/databox-migration-vm-clone.png)

2. Välj ett namn för den klonade virtuella datorn eller mallen för den virtuella datorn.

3. Markera mappen där du vill placera det klonade objektet och välj sedan **Nästa**.

4. Markera det kluster eller den resurspool där du vill placera det klonade objektet och välj sedan **Nästa**.

5. Välj **Databox-Datastore** som lagringsplats och välj sedan **Nästa**.

    ![Kloning av virtuella datorer - välj datalager](media/databox-migration-vm-clone-select-datastore.png)

6. Om du vill anpassa några alternativ för det klonade objektet markerar du anpassningsalternativen och väljer sedan **Nästa**.

7. Granska konfigurationerna och välj **Slutför**.

8. Upprepa steg 1 till och med 7 för ytterligare virtuella datorer eller mallar för virtuella datorer.

Virtuella datorer kommer att klonas och lagras på NFS-datalagret från Data Box. När de virtuella datorerna har klonats kontrollerar du att de stängs av som förberedelse för migrering av data till Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiera ISO-filer till datalagret Data Box

1. Gå till Lagring från det lokala virtuella webbgränssnittet **iCenter.**  Välj **Databox-Datastore** och välj sedan **Filer**. Skapa en ny mapp för lagring av ISO-filer.

    ![Kopiera ISO - skapa ny mapp](media/databox-migration-create-folder.png)

2. Ange ett namn för mappen där ISO-filer ska lagras.

3. Dubbelklicka på den nyskapade mappen för att öppna den.

4. Välj **Ladda upp filer** och välj sedan de ISO-filer som du vill ladda upp.
    
    ![Kopiera ISO - ladda upp filer](media/databox-migration-upload-iso.png)

> [!TIP]
> Om du redan har ISO-filer i ditt lokala datalager kan du välja filerna och **Kopiera till** att kopiera filerna till Data Box NFS-databutiken.


## <a name="prepare-data-box-for-return"></a>Förbered dataruta för retur

När alla data från virtuella datorer, malldata för virtuella datorer och alla ISO-filer kopieras till Data Box NFS-datalagret kan du koppla från datalagret från ditt vCenter. Alla virtuella datorer och mallar för virtuella datorer måste tas bort från lagret innan du kopplar från datalagret.

### <a name="remove-objects-from-inventory"></a>Ta bort objekt från lagret

1. Gå till Lagring från det lokala virtuella webbgränssnittet **iCenter.** Välj **Databox-Datastore** och välj sedan **virtuella datorer**.

    ![Ta bort virtuella datorer från lagret - inaktiverad](media/databox-migration-select-databox-vm.png)

2. Kontrollera att alla virtuella datorer är avstängda.

3. Markera alla virtuella datorer, högerklicka och välj sedan **Ta bort från lagret**.

    ![Ta bort virtuella datorer från lagret](media/databox-migration-remove-vm-from-inventory.png)

4. Välj **VM-mallar i mappar** och upprepa sedan steg 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Ta bort Data Box NFS-datalagret från vCenter

DataBox NFS-datalagret måste kopplas från VMware ESXi-värdar innan de förbereder sig för retur.

1. Gå till Lagring från det lokala virtuella webbgränssnittet **iCenter.**

2. Högerklicka på **Databox-Datastore** och välj **Avmontera datalager**.

    ![Avmontera databoxdatalager](media/databox-migration-unmount-datastore.png)

3. Välj alla ESXi-värdar där datalagret är monterat och välj **OK**.

    ![Avmontera databoxdatalager – välj värdar](media/databox-migration-unmount-datastore-select-hosts.png)

4. Granska och acceptera eventuella varningar och välj **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Förbered dataruta för retur och returnera den sedan

Följ stegen i artikeln [Returnera Azure Data Box och verifiera dataöverföring till Azure](../databox/data-box-deploy-picked-up.md) för att returnera datarutan. Kontrollera status för datakopian till ditt Azure-lagringskonto. När statusen har slutförts kan du verifiera data i ditt Azure-lagringskonto.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiera data från Azure-lagring till Azure VMware-lösning

Data som kopieras till din Data Box-enhet kommer att vara tillgängliga på ditt Azure-lagringskonto efter att orderstatusen för datarutan visas som slutförd. Data kan nu kopieras till din Azure VMware-lösning. Data i lagringskontot måste kopieras till vSAN-datalagret i ditt privata moln med hjälp av NFS-protokollet. 

Kopiera först Blob-lagringsdata till en hanterad disk på en virtuell Linux-dator i Azure med hjälp av **AzCopy**. Gör den hanterade disken tillgänglig via NFS, montera NFS-resursen som ett datalager i ditt privata moln och kopiera sedan data. Den här metoden möjliggör snabbare kopia av data till ditt privata moln.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopiera data till ditt privata moln med hjälp av en virtuell Linux-dator och hanterade diskar och exportera sedan som NFS-resurs

1. Skapa en [virtuell Linux-dator](../virtual-machines/linux/quick-create-portal.md) i Azure i samma region där ditt lagringskonto skapas och har en virtuell Azure-nätverksanslutning till ditt privata moln.

2. Skapa en hanterad disk vars lagringskapacitet är större än mängden blob-data och [bifoga den till din virtuella Linux-dator](../virtual-machines/linux/attach-disk-portal.md).  Om mängden blob-data är större än kapaciteten för den största hanterade disken som är tillgänglig, måste data kopieras i flera steg eller med hjälp av flera hanterade diskar.

3. Anslut till den virtuella Linux-datorn och montera den hanterade disken.

4. Installera [AzCopy på din virtuella Linux-dator.](../storage/common/storage-use-azcopy-v10.md)

5. Hämta data från din Azure Blob-lagring till den hanterade disken med AzCopy.  Kommandosyntax: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Ersätt `<storage-account-name>` med ditt Azure-lagringskontonamn och `<container-name>` med behållaren som innehåller data som kopieras via Data Box.

6. Installera NFS-servern på din virtuella Linux-dator:

    - På en Ubuntu/Debiandistribution: `sudo apt install nfs-kernel-server`.
    - På en Enterprise `sudo yum install nfs-utils`Linux-distribution: .

7. Ändra behörigheten för mappen på den hanterade disken där data från Azure Blob-lagring kopierades.  Ändra behörigheterna för alla mappar som du vill exportera som NFS-resurs.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Tilldela behörigheter för klient-IP-adresser för åtkomst `/etc/exports` till NFS-resursen genom att redigera filen.

    ```bash
    sudo vi /etc/exports
    ```
    
    Ange följande rader i filen för varje ESXi-värd-IP för ditt privata moln.  Om du skapar resurser för flera mappar lägger du till alla mappar.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportera NFS-resurserna med `sudo exportfs -a` kommandot.

10. Starta om NFS-kärnservern `sudo systemctl restart nfs-kernel-server` med kommandot.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montera Linux virtuell dator NFS-resurs som ett datalager på ett privat moln vCenter-kluster och kopiera sedan data

NFS-resursen från din virtuella Linux-dator måste monteras som ett datalager i ditt privata virtuella molnkluster. När den har monterats kan data kopieras från NFS-datalagret till det privata molnet vSAN-datalagret.

1. Logga in på din privata moln vCenter-server.

2. Högerklicka på **Datacenter,** välj **Lagring,** välj **Nytt datalager**och välj sedan **Nästa**.

   ![Lägg till nytt datalager](media/databox-migration-add-datastore.png)

3. I steg 1 i guiden Lägg till datalager väljer du **NFS-typen.**

   ![Lägg till nytt datalager - typ](media/databox-migration-add-datastore-type.png)

4. I steg 2 i guiden väljer du **NFS 3** som NFS-version och väljer sedan **Nästa**.

   ![Lägg till nytt datalager - NFS-version](media/databox-migration-add-datastore-nfs-version.png)

5. I steg 3 i guiden anger du namnet på datalagret, sökvägen och servern.  Du kan använda IP-adressen för din virtuella Linux-dator för servern.  Mappsökvägen kommer att `/<folder>/<subfolder>/` vara i formatet.

   ![Lägg till ny datalager - NFS-konfiguration](media/databox-migration-add-datastore-nfs-configuration.png)

6. I steg 4 i guiden väljer du de ESXi-värdar där du vill att datalagret ska monteras och väljer sedan **Nästa**.  I ett kluster väljer du alla värdar för att säkerställa migreringen av de virtuella datorerna.

   ![Lägg till nytt datalager - Välj värdar](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. I steg 5 i guiden granskar du sammanfattningen och väljer sedan **Slutför**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Lägga till virtuella datorer och mallar för virtuella datorer från ett NFS-datacenter i lagret

1. Gå till **Lagring**från ditt privata virtuella molngränssnitt.  Välj en NFS-datacenter för Linux-datorn och välj sedan **Filer**.

    ![Välj filer från NFS-datalager](media/databox-migration-datastore-select-files.png)

2. Välj en mapp som innehåller en virtuell dator eller en mall för den virtuella datorn.  I informationsfönstret väljer du en VMX-fil för en virtuell dator eller en VMTX-fil för en virtuell datormall.

3. Välj **Registrera virtuell dator** för att registrera den virtuella datorn på ditt privata moln vCenter.

    ![Registrera virtuell dator](media/databox-migration-datastore-register-vm.png)

4. Välj den datacenter-, mapp- och kluster-/resurspool där du vill att den virtuella datorn ska registreras.

4. Upprepa steg 3 och 4 för alla virtuella datorer och mallar för virtuella datorer.

5. Gå till mappen som innehåller ISO-filerna.  Markera ISO-filerna och välj sedan **Kopiera till** för att kopiera filerna till en mapp i vSAN-datalagret.

De virtuella datorerna och mallarna för virtuella datorer är nu tillgängliga på ditt privata moln vCenter. Dessa virtuella datorer måste flyttas från NFS-datalagret till vSAN-datalagret innan du aktiverar dem. Du kan använda alternativet **lagring vMotion** och välja vSAN-datalagret som mål för de virtuella datorerna.

Mallarna för virtuella datorer måste klonas från din Linux-virtual machine NFS-datalager till ditt vSAN-datalager.

### <a name="clean-up-your-linux-virtual-machine"></a>Rensa din virtuella Linux-maskin

När alla data har kopierats till ditt privata moln kan du ta bort NFS-datalagret från ditt privata moln:

1. Kontrollera att alla virtuella datorer och mallar flyttas och klonas till ditt vSAN-datalager.

2. Ta bort alla mallar för virtuella datorer från NFS-datalagret från lagret.

3. Avmontera Linux-datalager för virtuella datorer från ditt privata moln vCenter.

4. Ta bort den virtuella datorn och den hanterade disken från Azure.

5. Om du inte vill behålla data som överfördes av Data Box i ditt lagringskonto tar du bort Azure-lagringskontot.  
    


## <a name="next-steps"></a>Nästa steg

* Läs mer om [Data Box](../databox/data-box-overview.md).
* Läs mer om olika alternativ för [att migrera arbetsbelastningar till ditt privata moln](migrate-workloads.md).
