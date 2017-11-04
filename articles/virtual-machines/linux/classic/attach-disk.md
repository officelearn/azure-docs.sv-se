---
title: Ansluta en disk till en Linux-VM i Azure | Microsoft Docs
description: "Lär dig att koppla en datadisk till en Linux VM som använder den klassiska distributionsmodellen och initiera disken så att den är redo för användning"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 017ba7197e11c2b222082833d5acabb9e542b762
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Hur du kopplar en datadisk till en virtuell Linux-dator
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Se hur du [ansluta en datadisk med hjälp av Resource Manager-distributionsmodellen](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan koppla både tom och diskar som innehåller data till din virtuella Azure-datorer. Båda typer av diskar är VHD-filer som finns i ett Azure storage-konto. Som med att lägga till en disk till en Linux-dator måste när du ansluta disken du initiera och formatera den så att den är redo för användning. Den här artikeln information som kopplar både tom och diskar som redan innehåller data som dina virtuella datorer, samt hur du sedan initiera och formatera en ny disk.

> [!NOTE]
> Det är en bra idé att använda en eller flera olika diskar för att lagra data för en virtuell dator. När du skapar en virtuell Azure-dator har en operativsystemdisk och en tillfällig disk. **Använd inte tillfällig disken för att lagra beständiga data.** Som namnet antyder ger tillfällig lagring. Det finns ingen redundans eller säkerhetskopiering eftersom den inte finns i Azure-lagring.
> Tillfällig disken är vanligtvis hanteras av Azure Linux-agenten och automatiskt monteras **/mnt/resurs** (eller **/mnt** på Ubuntu avbildningar). Å andra sidan en datadisk kan kallas Linux kerneln ungefär `/dev/sdc`, och du behöver att partitionera, formatera och montera den här resursen. Finns det [användarhandboken för Azure Linux-agenten] [ Agent] mer information.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Initiera en ny datadisk i Linux
1. SSH till den virtuella datorn. Mer information finns i [så att logga in på en virtuell dator som kör Linux][Logon].
2. Därefter måste du hitta enhets-ID för datadisken som ska initieras. Det finns två sätt att göra det:
   
    en) Grep för SCSI-enheter i loggarna, exempelvis följande kommando:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    För senaste Ubuntu distributioner kan du behöva använda `sudo grep SCSI /var/log/syslog` eftersom loggar till `/var/log/messages` kan vara inaktiverade som standard.
   
    Du kan hitta identifieraren för den senaste datadisk som har lagts till i de meddelanden som visas.
   
    ![Hämta disk-meddelanden](./media/attach-disk/scsidisklog.png)
   
    ELLER
   
    b) Använd det `lsscsi` kommando för att ta reda på enhetens id. `lsscsi` kan installeras antingen `yum install lsscsi` (Red Hat baserad på distributioner) eller `apt-get install lsscsi` (Debian baserad på distributioner). Du hittar den disk som du letar efter av dess *lun* eller **logiskt enhetsnummer**. Till exempel den *lun* för de diskar som du har kopplat kan enkelt se `azure vm disk list <virtual-machine>` som:

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
   
    Jämföra dessa data med utdata från `lsscsi` för samma prov virtuell dator:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Det sista numret i tuppeln på varje rad i *lun*. Se `man lsscsi` för mer information.
3. I Kommandotolken skriver du följande kommando för att skapa din enhet:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. När du uppmanas ange  **n**  att skapa en partition.

    ![Skapa enhet](./media/attach-disk/fdisknewpartition.png)

5. När du uppmanas ange **p** så att den primära partitionen för partitionen. Typen **1** så att den första partitionen och skriv sedan RETUR för att acceptera standardvärdet för cylindern. På vissa datorer kan visas standardvärdena för först och sista sektorer, i stället för cylindern. Du kan välja att acceptera standardinställningarna.

    ![Skapa partition](./media/attach-disk/fdisknewpartdetails.png)


6. Typen **p** att se information om den disk som ska partitioneras.

    ![Diskinformation för listan](./media/attach-disk/fdiskpartitiondetails.png)


7. Typen **w** att spara inställningar för disken.

    ![Att skriva ändringar för disk](./media/attach-disk/fdiskwritedisk.png)

8. Du kan nu skapa filsystemet på den nya partitionen. Lägg till partitionsnumret enhets-ID (i följande exempel `/dev/sdc1`). I följande exempel skapar en partition ext4 på /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Skapa filsystem](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 system stöder bara läsbehörighet för ext4 filsystem. För de här systemen rekommenderas att formatera nytt filsystem som ext3 i stället för ext4.

9. Skapa en katalog att montera det nya filsystemet på följande sätt:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Slutligen kan du montera enheten enligt följande:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Datadisken är nu klar att använda som **/datadrive**.
   
    ![Skapa katalogen och montera disken](./media/attach-disk/mkdirandmount.png)

11. Lägg till den nya enheten /etc/fstab:
   
    För att se till att enheten återmonteras automatiskt efter en omstart måste den läggas till i filen/etc/fstab. Dessutom kan rekommenderar vi starkt att UUID (universellt Unique IDentifier) används i /etc/fstab för att referera till enheten i stället för bara namnet på en enhet (d.v.s. /dev/sdc1). Felaktig disk monteras på en viss plats om Operativsystemet upptäcker ett diskfel under start- och eventuella återstående hårddiskar sedan tilldelas dessa enhets-ID undviks med UUID. Du kan använda för att hitta UUID för den nya enheten i **blkid** verktyget:
   
    ```bash
    sudo -i blkid
    ```
   
    Utdata liknar följande exempel:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Felaktigt redigering av **/etc/fstab** filen kan resultera i ett system som inte kan startas. Om du är osäker, i den distribution dokumentationen finns information om hur du ska redigera den här filen. Vi rekommenderar också att en säkerhetskopia av /etc/fstab-filen har skapats innan du redigerar.

    Därefter öppnar den **/etc/fstab** i en textredigerare:

    ```bash
    sudo vi /etc/fstab
    ```

    I det här exemplet använder vi UUID-värdet för den nya **/dev/sdc1** enheten som skapades i föregående steg och monteringspunkt **/datadrive**. Lägg till följande rad i slutet av den **/etc/fstab** fil:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Eller, för baserat på SuSE Linux-datorer kan du behöva använda ett något annorlunda format:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > Den `nofail` alternativet ser du till att den virtuella datorn startar även om filsystemet är skadat eller disken finns inte vid start. Utan det här alternativet, du kan stöta på problem som beskrivs i [kan SSH för Linux VM på grund av fel FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Du kan nu testa att filsystemet är monterade korrekt genom demontering och ommontering filsystemet, dvs. med exempel monteringspunkt `/datadrive` skapas i tidigare steg:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Om den `mount` kommandot genererar ett fel, kontrollera/etc/fstab filen korrekt syntax. Om ytterligare dataenheter eller partitioner har skapats, anger du dem i/etc/fstab samt separat.

    Gör att enheten skrivbar med hjälp av det här kommandot:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Därefter tar du bort en datadisk utan att redigera fstab leda till den virtuella datorn inte startar. Om detta är vanligt förekommande de flesta distributioner ange antingen det `nofail` och/eller `nobootwait` fstab alternativ som gör att ett system för att starta även om disken inte montera vid start. Se dokumentationen för din distribution för mer information om dessa parametrar.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIMNING/UNMAP stöd för Linux i Azure
Vissa Linux kärnor stöd för TRIM/UNMAP åtgärder för att ta bort oanvända block på disken. Dessa åtgärder är främst användbart för standardlagring att meddela Azure som bort sidor som inte längre är giltig och kan tas bort. Ignorera sidor kan du spara kostnader om du skapar stora filer och ta bort dem.

Det finns två sätt att aktivera TRIMNING stöd i Linux-VM. Som vanligt, kontakta din distribution för den rekommenderade metoden:

* Använd den `discard` montera alternativet i `/etc/fstab`, till exempel:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* I vissa fall den `discard` alternativet kanske prestanda. Du kan också köra den `fstrim` kommandot manuellt från kommandoraden eller lägga till den i din crontab att köras regelbundet:
  
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
Du kan läsa mer om hur du använder din Linux VM i följande artiklar:

* [Logga in till en virtuell dator som kör Linux][Logon]
* [Hur du koppla bort en disk från en virtuell Linux-dator](detach-disk.md)
* [Med den klassiska distributionsmodellen Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Konfigurera RAID på en virtuell Linux-dator i Azure](../configure-raid.md)
* [Konfigurera LVM på en virtuell Linux-dator i Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../agent-user-guide.md
[Logon]:../mac-create-ssh-keys.md
