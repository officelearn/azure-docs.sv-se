---
title: "Säkerhetskopiera och återställa en Oracle-databas 12c-databas på en virtuell Azure Linux-dator | Microsoft Docs"
description: "Lär dig mer om att säkerhetskopiera och återställa en databas med Oracle-databas 12c i Azure-miljön."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: 9a2293f13b90e9a4cb11b4169fad969dd622a9a6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Säkerhetskopiera och återställa en Oracle-databas 12c-databas på en virtuell Azure Linux-dator

Du kan använda Azure CLI för att skapa och hantera Azure-resurser i en kommandotolk eller använda skript. Vi använder Azure CLI-skript för att distribuera en Oracle-databas 12c-databas från en avbildning för Azure Marketplace-galleriet i den här artikeln.

Innan du börjar bör du kontrollera att Azure CLI är installerad. Mer information finns i [Azure CLI installationsguiden](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Förbered miljön

### <a name="step-1-prerequisites"></a>Steg 1: förutsättningar

*   Du måste skapa en Linux VM som har en installerad instans av Oracle-databas 12c om du vill utföra processen för säkerhetskopiering och återställning. Marketplace-avbildning som du använder för att skapa den virtuella datorn har namnet *Oracle: Oracle-databasen-Ee:12.1.0.2:latest*.

    Information om hur du skapar en Oracle-databas finns i [Oracle skapa database Snabbstart](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Steg 2: Anslut till den virtuella datorn

*   Om du vill skapa en SSH (Secure Shell)-session med den virtuella datorn, använder du följande kommando. Ersätt IP-adress och värdnamn med den `publicIpAddress` värde för den virtuella datorn.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Steg 3: Förbereda databasen

1.  Det här steget förutsätter att du har en Oracle-instans (cdb1) som körs på en virtuell dator med namnet *myVM*.

    Kör den *oracle* superanvändare rot och sedan initiera lyssnaren:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Valfritt) Kontrollera att databasen är i läget för archive log:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (Valfritt) Skapa en tabell om du vill testa genomförandet:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Kontrollera eller ändra storlek och Säkerhetskopians plats:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Använd Oracle Recovery Manager (RMAN) för att säkerhetskopiera databasen:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Steg 4: Programkonsekvent säkerhetskopiering för virtuella Linux-datorer

Programkonsekvent säkerhetskopiering är en ny funktion i Azure Backup. Du kan skapa och välj skript körs före och efter VM-ögonblicksbild (före ögonblicksbild och efter ögonblicksbild).

1. Hämta JSON-filen.

    Hämta VMSnapshotScriptPluginConfig.json från https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Filinnehållet se ut ungefär så här:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Skapa mappen /etc/azure på den virtuella datorn:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Kopiera JSON-filen.

    Kopiera VMSnapshotScriptPluginConfig.json till mappen /etc/azure.

4. Redigera JSON-filen.

    Redigera filen VMSnapshotScriptPluginConfig.json att inkludera den `PreScriptLocation` och `PostScriptlocation` parametrar. Exempel:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Skapa inför ögonblicksbilden och efter ögonblickbild skriptfilerna.

    Här är ett exempel på skript inför ögonblicksbilden och efter ögonblicksbild för en ”kalla” säkerhetskopiering (en offlinesäkerhetskopiering, med avstängning och omstart):

    För /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    För /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Här är ett exempel på inför ögonblicksbilden och efter ögonblickbild skript för ”varm säkerhetskopiering” (en onlinesäkerhetskopiering):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    För /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /Etc/azure/pre_script.sql, ändra i innehållet i filen enligt dina krav:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /Etc/azure/post_script.sql, ändra i innehållet i filen enligt dina krav:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Ändra behörigheter för filer:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testa skripten.

    Om du vill testa skripten först logga in som rot. Kontrollera sedan att det inte finns några fel:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Mer information finns i [programkonsekvent säkerhetskopiering för virtuella Linux-datorer](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Steg 5: Använd Azure Recovery Services-valv om du vill säkerhetskopiera den virtuella datorn

1.  I Azure-portalen, söka efter **Recovery Services-valv**.

    ![Sidan för Recovery Services-valv](./media/oracle-backup-recovery/recovery_service_01.png)

2.  På den **Recovery Services-valv** klickar du på bladet för att lägga till ett nytt valv **Lägg till**.

    ![Recovery Services-valv lägger du till sidan](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Om du vill fortsätta klickar du på **myVault**.

    ![Sidan innehåller information om Recovery Services-valv](./media/oracle-backup-recovery/recovery_service_03.png)

4.  På den **myVault** bladet, klickar du på **säkerhetskopiering**.

    ![Recovery Services-valv säkerhetskopiera sida](./media/oracle-backup-recovery/recovery_service_04.png)

5.  På den **säkerhetskopiering målet** bladet använder standardvärden för **Azure** och **virtuella**. Klicka på **OK**.

    ![Sidan innehåller information om Recovery Services-valv](./media/oracle-backup-recovery/recovery_service_05.png)

6.  För **säkerhetskopiera princip**, använda **DefaultPolicy**, eller välj **Skapa ny princip**. Klicka på **OK**.

    ![Recovery Services-valv säkerhetskopiera princip detaljsida](./media/oracle-backup-recovery/recovery_service_06.png)

7.  På den **Välj virtuella datorer** bladet Välj den **myVM1** kryssrutan och klicka sedan på **OK**. Klicka på den **Aktivera säkerhetskopiering** knappen.

    ![Återställningsobjekt Services valv till säkerhetskopiering detaljsida](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > När du klickar på **Aktivera säkerhetskopiering**, säkerhetskopieringen startas inte förrän den schemalagda tiden går ut. Slutför nästa steg om du vill konfigurera en omedelbar säkerhetskopia.

8.  På den **myVault - objekt för säkerhetskopiering** bladet under **säkerhetskopiering OBJEKTANTAL**, Välj antalet säkerhetskopiering objekt.

    ![Recovery Services valv myVault detaljsida](./media/oracle-backup-recovery/recovery_service_08.png)

9.  På den **säkerhetskopiering objekt (Azure virtuell dator)** bladet till höger på sidan, klicka på ellipsknappen (**...** ) knappen och klicka sedan på **Säkerhetskopiera nu**.

    ![Recovery Services-valv säkerhetskopiering nu kommando](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klicka på den **säkerhetskopiering** knappen. Vänta tills säkerhetskopieringen ska slutföras. Gå sedan till [steg 6: ta bort databasfilerna](#step-6-remove-the-database-files).

    Om du vill visa status för jobbet, klickar du på **jobb**.

    ![Recovery Services-valv jobbet sida](./media/oracle-backup-recovery/recovery_service_10.png)

    Status för jobbet visas i följande bild:

    ![Recovery Services-valv jobbet sida med status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Åtgärda eventuella fel i loggfilen för en programkonsekvent säkerhetskopiering. Loggfilen finns i /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Steg 6: Ta bort databasfilerna 
Senare i den här artikeln lär du dig hur du testar återställningsprocessen. Innan du kan testa återställningsprocessen måste du ta bort databasfilerna.

1.  Ta bort filer tabellutrymmet och säkerhetskopiering:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Valfritt) Stänga av Oracle-instansen:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Återställa borttagna filer från Recovery Services-valv
Om du vill återställa borttagna filer, gör du följande:

1. I Azure-portalen, söka efter den *myVault* Recovery Services-valv objektet. På den **översikt** bladet under **Säkerhetskopiera objekt**, Välj antalet objekt.

    ![Recovery Services valv myVault Säkerhetskopiera objekt](./media/oracle-backup-recovery/recovery_service_12.png)

2. Under **säkerhetskopiering OBJEKTANTAL**, Välj antalet objekt.

    ![Recovery Services-valv antal för virtuell dator i Azure-säkerhetskopiering objekt](./media/oracle-backup-recovery/recovery_service_13.png)

3. På den **myvm1** bladet, klickar du på **filåterställning (förhandsgranskning)**.

    ![Skärmbild av Recovery Services-valv återställningssidan för filen](./media/oracle-backup-recovery/recovery_service_14.png)

4. På den **filåterställning (förhandsgranskning)** rutan klickar du på **hämta skriptet**. Spara sedan filen download (.sh) till en mapp på klientdatorn.

    ![Hämta filen sparar alternativ](./media/oracle-backup-recovery/recovery_service_15.png)

5. Kopiera filen .sh till den virtuella datorn.

    I följande exempel visas hur du kan använda en säker kopia (scp) kommandot för att flytta filen till den virtuella datorn. Du kan också kopiera innehållet i Urklipp och klistra in innehållet i en ny fil som har ställts in på den virtuella datorn.

    > [!IMPORTANT]
    > Se till att du uppdaterar IP-adress och mappen värdena i exemplet nedan. Värdena måste mappas till mappen där filen sparas.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Ändra filen, så att det ägs av roten.

    I följande exempel visas att ändra filen så att det ägs av roten. Ändra behörigheter.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    I följande exempel visar vad som ska visas när du har kört skriptet. När du uppmanas att fortsätta ange **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Åtkomst till de monterade volymerna bekräftas.

    Om du vill avsluta, ange **q**, och sök sedan efter de monterade volymerna. Om du vill skapa en lista över volymer som lagts till i en kommandotolk, ange **df -k**.

    ![Kommandot df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Använd följande skript om du vill kopiera de saknade filerna till mapparna:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. Använd RMAN ska kunna återställa databasen i följande skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Demontera disken.

    I Azure-portalen på den **filåterställning (förhandsgranskning)** bladet, klickar du på **demontera diskar**.

    ![Demontera diskar kommando](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Återställa hela den virtuella datorn

Du kan återställa hela den virtuella datorn i stället för att återställa borttagna filer från Recovery Services-valv.

### <a name="step-1-delete-myvm"></a>Steg 1: Ta bort myVM

*   I Azure-portalen går du till den **myVM1** valvet och välj sedan **ta bort**.

    ![Valvet delete-kommandot](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Steg 2: Återställa den virtuella datorn

1.  Gå till **Recovery Services-valv**, och välj sedan **myVault**.

    ![myVault post](./media/oracle-backup-recovery/recover_vm_02.png)

2.  På den **översikt** bladet under **Säkerhetskopiera objekt**, Välj antalet objekt.

    ![myVault Säkerhetskopiera objekt](./media/oracle-backup-recovery/recover_vm_03.png)

3.  På den **säkerhetskopiering objekt (Azure virtuell dator)** bladet väljer **myvm1**.

    ![Återställningssidan för VM](./media/oracle-backup-recovery/recover_vm_04.png)

4.  På den **myvm1** bladet Klicka på ellipsknappen (**...** ) knappen och klicka sedan på **återställa VM**.

    ![Restore-kommandots VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  På den **Välj återställningspunkt** bladet, Välj det objekt som du vill återställa och klicka sedan på **OK**.

    ![Välj återställningspunkten](./media/oracle-backup-recovery/recover_vm_06.png)

    Om du har aktiverat programkonsekvent säkerhetskopiering, visas en blå vertikalstreck.

6.  På den **Återställ konfiguration** bladet välj namnet på virtuella datorn, välja en resursgrupp och klicka sedan på **OK**.

    ![Återställa konfigurationsvärden](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Om du vill återställa den virtuella datorn, klickar du på den **återställa** knappen.

8.  Om du vill visa status för återställningen klickar du på **jobb**, och klicka sedan på **säkerhetskopieringsjobb**.

    ![Säkerhetskopieringsjobb status-kommandot](./media/oracle-backup-recovery/recover_vm_08.png)

    Följande bild visar status för återställningen:

    ![Status för återställningen](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Steg 3: Ange den offentliga IP-adressen
När den virtuella datorn har återställts kan ställa in den offentliga IP-adressen.

1.  I sökrutan anger **offentliga IP-adressen**.

    ![Lista över offentliga IP-adresser](./media/oracle-backup-recovery/create_ip_00.png)

2.  På den **offentliga IP-adresser** bladet, klickar du på **Lägg till**. På den **skapa offentlig IP-adress** bladet för **namn**, Välj offentlig IP-namnet. För **resursgrupp**, väljer du **använd befintlig**. Klicka på **Skapa**.

    ![Skapa IP-adress](./media/oracle-backup-recovery/create_ip_01.png)

3.  Om du vill associera den offentliga IP-adressen till nätverksgränssnittet för den virtuella datorn, leta upp och markera **myVMip**. Klicka på **associera**.

    ![Associera IP-adress](./media/oracle-backup-recovery/create_ip_02.png)

4.  För **resurstypen**väljer **nätverksgränssnittet**. Välj det nätverksgränssnitt som används av myVM-instansen och klicka sedan på **OK**.

    ![Välj resurstyp och NIC-värden](./media/oracle-backup-recovery/create_ip_03.png)

5.  Söka efter och öppna instansen av myVM är portar från portalen. IP-adressen som är associerad med den virtuella datorn visas på myVM **översikt** bladet.

    ![Värdet för IP-adress](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Steg 4: Anslut till den virtuella datorn

*   Använd följande skript för att ansluta till den virtuella datorn:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Steg 5: Testa om databasen är tillgänglig
*   Testa hjälpmedel genom att använda följande skript:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Om databasen **Start** kommandot genererar ett fel, om du vill återställa databasen finns [steg 6: Använd RMAN ska kunna återställa databasen](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Steg 6: (Valfritt) använda RMAN ska kunna återställa databasen
*   Om du vill återställa databasen använder du följande skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Säkerhetskopiering och återställning av databasen på 12c Oracle-databas på en Azure Linux-VM är nu klar.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Du kan använda följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när du inte längre behöver den virtuella datorn:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudier: Skapa högtillgängliga virtuella datorer](../../linux/create-cli-complete.md)

[Utforska VM distribution Azure CLI-exempel](../../linux/cli-samples.md)



