---
title: Anslut en disk till en Linux-VM i Azure | Microsoft Docs
description: Lär dig hur du ansluter en datadisk till en Linux-VM med hjälp av den klassiska distributionsmodellen och initiera disken så att den är redo för användning
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: b5bb3a9353f83cb569988a068f3ca02da85f739c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929159"
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Hur du ansluter en datadisk till en Linux-dator
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Se hur du [ansluter en datadisk med hjälp av Resource Manager-distributionsmodellen](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan koppla både tomma diskar och diskar som innehåller data för virtuella datorer i Azure. Båda typerna av diskar är .vhd-filer som finns i ett Azure storage-konto. Som med att lägga till en disk till en Linux-dator behöver när du har kopplat disken du att initiera och formatera den så att den är redo att användas. Den här artikeln beskriver koppla både tomma diskar och diskar som redan innehåller data till dina virtuella datorer, samt hur du sedan initiera och formatera en ny disk.

> [!NOTE]
> Det är en bra idé att använda en eller flera separata diskar för att lagra data för en virtuell dator. När du skapar en Azure virtuell dator har en operativsystemdisk och en tillfällig disk. **Använd inte den temporära disken för att lagra beständiga data.** Som namnet antyder ger tillfällig lagring. Det finns inga redundans eller säkerhetskopiering eftersom det inte finns i Azure storage.
> Den temporära disken är vanligtvis hanteras av Azure Linux-agenten och automatiskt monteras till **/mnt/resource** (eller **/mnt** på Ubuntu-avbildningar). Å andra sidan, en datadisk kan namn av Linux-kernel som `/dev/sdc`, och du behöver partitionera, formatera och montera den här resursen. Se den [Användarguide för Azure Linux Agent] [ Agent] information.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Initiera en ny datadisk i Linux
1. SSH till den virtuella datorn. Mer information finns i [hur du ansluter till en virtuell dator som kör Linux][Logon].
2. Därefter måste du hitta enhets-ID för datadisken kan initieras. Det finns två sätt att göra det:
   
    (a) Grep för SCSI-enheter i loggarna, till exempel i följande kommando:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    För de senaste distributioner som Ubuntu, kan du behöva använda `sudo grep SCSI /var/log/syslog` eftersom logga `/var/log/messages` kan vara inaktiverade som standard.
   
    Du hittar ID för senaste datadisken som lades till i de meddelanden som visas.
   
    ![Hämta disk-meddelanden](./media/attach-disk/scsidisklog.png)
   
    ELLER
   
    (b) Använd den `lsscsi` kommando för att ta reda på enhets-id. `lsscsi` kan installeras genom att antingen `yum install lsscsi` (Red Hat baserad på distributioner) eller `apt-get install lsscsi` (på Debian baserat distributioner). Du hittar den disk som du letar efter med dess *lun* eller **logiskt enhetsnummer**. Till exempel den *lun* för diskar som du har kopplat enkelt kan ses från `azure vm disk list <virtual-machine>` som:

    ```azurecli
    azure vm disk list myVM
    ```

    De utdata som genereras liknar följande:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Jämföra dessa data med utdata från `lsscsi` för samma prov på virtuell dator:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Det sista numret i tuppeln på varje rad är den *lun*. Se `man lsscsi` för mer information.
3. I Kommandotolken skriver du följande kommando för att skapa din enhet:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. När du uppmanas, skriver **n** att skapa en partition.

    ![Skapa enhet](./media/attach-disk/fdisknewpartition.png)

5. När du uppmanas, skriver **p** att partitionen den primära partitionen. Typ **1** så att den första partitionen och skriv sedan RETUR för att acceptera standardvärdet för cylindern. I vissa system kan det visa standardvärdena för första och sista sektorer, i stället för cylindern. Du kan välja att acceptera standardinställningarna.

    ![Skapa partition](./media/attach-disk/fdisknewpartdetails.png)


6. Typ **p** att se information om den disk som är är partitionerad.

    ![Diskinformation för listan](./media/attach-disk/fdiskpartitiondetails.png)


7. Typ **w** att skriva inställningarna för disken.

    ![Skriva diskändringar](./media/attach-disk/fdiskwritedisk.png)

8. Nu kan du skapa filsystemet på den nya partitionen. Lägg till partitionsnumret till enhets-ID (i följande exempel `/dev/sdc1`). I följande exempel skapas en ext4 partition på /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Skapa filsystem](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 system stöder bara läsbehörighet för ext4 filsystem. För de här systemen rekommenderas att formatera det nya filsystemet som ext3 i stället för ext4.

9. Skapa en katalog att montera filsystemet nya på följande sätt:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Slutligen kan du montera enheten enligt följande:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Datadisken är nu klar att använda som **/datadrive**.
   
    ![Skapa katalogen och montera disken](./media/attach-disk/mkdirandmount.png)

11. Lägg till den nya enheten i/etc/fstab:
   
    För att säkerställa att disken monteras automatiskt efter en omstart måste den läggas till i/etc/fstab-filen. Dessutom kan rekommenderar vi starkt att UUID (universellt Unik identifierare) används i/etc/fstab för att referera till enheten i stället för bara namnet på enheten (d.v.s. /dev/sdc1). Med hjälp av UUID undviker den felaktiga disken monteras på en viss plats om Operativsystemet upptäcker ett diskfel under start- och eventuella återstående datadiskar som sedan tilldelas dessa enhets-ID. Du kan använda för att hitta UUID för den nya enheten i **blkid** verktyget:
   
    ```bash
    sudo -i blkid
    ```
   
    Utdata ser ut ungefär så här:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Felaktigt redigerar den **/etc/fstab** filen kan resultera i ett system som inte kan startas. Om du är osäker, finns det distribution dokumentationen för information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av/etc/fstab-filen har skapats innan du redigerar.

    Därefter öppnar den **/etc/fstab** filen i en textredigerare:

    ```bash
    sudo vi /etc/fstab
    ```

    I det här exemplet använder vi UUID-värdet för den nya **/dev/sdc1** enheten som skapades i föregående steg och monteringspunkten **/datadrive**. Lägg till följande rad i slutet av den **/etc/fstab** fil:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Alternativt kan du behöva använda ett något annorlunda format på system som baseras på SuSE Linux:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > Den `nofail` alternativet ser du till att den virtuella datorn startar även om filsystemet är skadat eller disken finns inte vid start. Utan det här alternativet kan du stöta på beteende enligt beskrivningen i [kan SSH till Linux VM på grund av FSTAB-fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Nu kan du testa att filsystemet är monterad korrekt av demontering och ommontering filsystemet, dvs. med hjälp av exempel monteringspunkt `/datadrive` skapades i tidigare steg:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Om den `mount` kommandot genererar ett fel, kontrollera/etc/fstab-filen för rätt syntax. Om ytterligare enheter eller partitioner skapas, ange dem i/etc/fstab samt separat.

    Att enheten blir skrivbar med hjälp av det här kommandot:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Därefter ta bort en datadisk utan att redigera fstab leda till den virtuella datorn inte kan starta. Om detta är vanligt förekommande, de flesta distributioner måste ange den `nofail` och/eller `nobootwait` fstab-alternativ som gör att ett system att starta även om disken inte kan montera vid uppstart. Mer information om dessa parametrar finns i dokumentationen för din distribution.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP stöd för Linux i Azure
Vissa Linux-kernel stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som tagits bort sidor som inte längre är giltiga och kan ignoreras. Tar bort sidor kan minska kostnaderna om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i din Linux-VM. Som vanligt finns distributionen för den rekommenderade metoden:

* Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab ska köras regelbundet:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nästa steg
Du kan läsa mer om hur du använder din Linux-VM i följande artiklar:

* [Hur du ansluter till en virtuell dator som kör Linux][Logon]
* [Så här kopplar du bort en disk från en Linux-dator](detach-disk-classic.md)
* [Med Azure CLI med den klassiska distributionsmodellen](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Konfigurera RAID på en virtuell Linux-dator i Azure](../configure-raid.md)
* [Konfigurera LVM på en Linux-dator i Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
