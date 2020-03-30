---
title: Säkerhetskopiera och återställa en Oracle Database 12c-databas på en virtuell Azure Linux-dator | Microsoft-dokument
description: Lär dig hur du säkerhetskopierar och återställer en Oracle Database 12c-databas i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: bae7e53a316fa6ca3158639cc551a0a3de5cb952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536929"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Säkerhetskopiera och återställa en Oracle Database 12c-databas på en virtuell Azure Linux-dator

Du kan använda Azure CLI för att skapa och hantera Azure-resurser i en kommandotolk eller använda skript. I den här artikeln använder vi Azure CLI-skript för att distribuera en Oracle Database 12c-databas från en Azure Marketplace-galleriavbildning.

Innan du börjar kontrollerar du att Azure CLI är installerat. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="step-1-prerequisites"></a>Steg 1: Förutsättningar

*   Om du vill utföra säkerhetskopierings- och återställningsprocessen måste du först skapa en virtuell Linux-dator som har en installerad instans av Oracle Database 12c. Marketplace-avbildningen som du använder för att skapa den virtuella datorn heter *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Mer information om hur du skapar en Oracle-databas finns i [snabbstarten för Oracle create database](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Steg 2: Anslut till den virtuella datorn

*   Om du vill skapa en SSH-session (Secure Shell) med den virtuella datorn använder du följande kommando. Ersätt IP-adressen och värdnamnskombinationen `publicIpAddress` med värdet för den virtuella datorn.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Steg 3: Förbereda databasen

1.  Det här steget förutsätter att du har en Oracle-instans (cdb1) som körs på en virtuell dator med namnet *myVM*.

    Kör *oracle* superuser rot, och sedan initiera lyssnaren:

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

2.  (Valfritt) Kontrollera att databasen är i arkivloggläge:

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

3.  (Valfritt) Skapa en tabell för att testa commit:

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

4.  Verifiera eller ändra platsen för säkerhetskopian och storleken:

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

Programkonsekventa säkerhetskopior är en ny funktion i Azure Backup. Du kan skapa och välja skript som ska köras före och efter ögonblicksbilden av den virtuella datorn (ögonblicksbild före och efter ögonblicksbild).

1. Ladda ner JSON-filen.

    Ladda ner VMSnapshotScriptPluginConfig.json från https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Filinnehållet liknar följande:

    ```output
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

    Redigera filen VMSnapshotScriptPluginConfig.json för `PreScriptLocation` `PostScriptlocation` att inkludera och parametrarna. Ett exempel:

    ```output
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

5. Skapa skriptfilerna före ögonblicksbild och efter ögonblicksbild.

    Här är ett exempel på skript före ögonblicksbild och efter ögonblicksbild för en "kall säkerhetskopiering" (en offline-säkerhetskopiering, med avstängning och omstart):

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

    Här är ett exempel på skript före ögonblicksbild och efter ögonblicksbild för en "hot backup" (en online-säkerhetskopiering):

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

    För /etc/azure/pre_script.sql ändrar du innehållet i filen enligt dina krav:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    För /etc/azure/post_script.sql ändrar du innehållet i filen enligt dina krav:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Ändra filbehörigheter:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testa skripten.

    Om du vill testa skripten loggar du först in som root. Se sedan till att det inte finns några fel:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Mer information finns i [Programkonsekvent säkerhetskopiering för virtuella Linux-datorer](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Steg 5: Använd Azure Recovery Services-valv för att säkerhetskopiera den virtuella datorn

1.  Sök efter **Recovery Services-valv**i Azure-portalen .

    ![Sidan Recovery Services-valv](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Om du vill lägga till ett nytt valv på **valvbladet Återställningstjänster** klickar du på **Lägg till**.

    ![Sidan Lägger till valv för Återställningstjänster](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Om du vill fortsätta klickar du på **myVault**.

    ![Informationssida för Återställningstjänster](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Klicka på **Backup**på bladet **myVault** .

    ![Säkerhetskopieringssida för återställningstjänster](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Använd standardvärdena för **Azure** och Virtual Machine på bladet Mål för **säkerhetskopiering** . **Virtual machine** Klicka på **OK**.

    ![Informationssida för Återställningstjänster](./media/oracle-backup-recovery/recovery_service_05.png)

6.  För **principer för säkerhetskopiering**använder du **DefaultPolicy**eller väljer **Skapa ny princip**. Klicka på **OK**.

    ![Informationssida för säkerhetskopieringsprincip för återställningstjänster](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Markera kryssrutan **myVM1** på bladet **Markera virtuella datorer** och klicka sedan på **OK**. Klicka på knappen **Aktivera säkerhetskopiering.**

    ![Återställningstjänster valv objekt till backup detalj sidan](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > När du har klickat på **Aktivera säkerhetskopiering**startar säkerhetskopieringen inte förrän den schemalagda tiden går ut. Om du vill konfigurera en omedelbar säkerhetskopiering slutför du nästa steg.

8.  På bladet **myVault - Backup items,** under **ANTAL SÄKERHETSOBJEKT,** väljer du antalet säkerhetsobjekt.

    ![Sidan Recovery Services valv myVault detaljsida](./media/oracle-backup-recovery/recovery_service_08.png)

9.  På bladet **Säkerhetsobjekt (Azure Virtual Machine)** till höger på sidan klickar du på knappen ellips (**...**) och klickar sedan på **Säkerhetskopiering nu**.

    ![Recovery Services valv Säkerhetskopiering nu kommando](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klicka på knappen **Säkerhetskopiering.** Vänta tills säkerhetskopieringen är klar. Gå sedan till [steg 6: Ta bort databasfilerna](#step-6-remove-the-database-files).

    Om du vill visa status för säkerhetskopieringsjobbet klickar du på **Jobb**.

    ![Jobbsida för Återställningstjänster](./media/oracle-backup-recovery/recovery_service_10.png)

    Status för säkerhetskopieringsjobbet visas i följande bild:

    ![Jobbsida för Återställningstjänster valv med status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Åtgärda eventuella fel i loggfilen för en programkonsekvent säkerhetskopiering. Loggfilen finns på /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Steg 6: Ta bort databasfilerna 
Senare i den här artikeln får du lära dig att testa återställningsprocessen. Innan du kan testa återställningsprocessen måste du ta bort databasfilerna.

1.  Ta bort tabellutrymmes- och säkerhetskopior:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Valfritt) Stäng av Oracle-instansen:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Återställa de borttagna filerna från Recovery Services-valven
Så här återställer du de borttagna filerna:

1. Sök efter valvobjektet *myVault* Recovery Services-valv i Azure-portalen. Välj antalet objekt under **Säkerhetskopierade objekt**på bladet **Översikt.**

    ![Recovery Services valv myVault säkerhetskopieringsobjekt](./media/oracle-backup-recovery/recovery_service_12.png)

2. Välj antalet objekt under **ANTAL SÄKERHETSOBJEKT.**

    ![Återställningstjänster valv Azure Virtual Machine backup objekt antal](./media/oracle-backup-recovery/recovery_service_13.png)

3. Klicka på **Filåterställning (förhandsgranskning)** på **myvm1-bladet** .

    ![Skärmbild av sidan Återställningstjänster valv filåterställning](./media/oracle-backup-recovery/recovery_service_14.png)

4. Klicka på **Hämta skript**i fönstret **Filåterställning (förhandsgranskning).** Spara sedan hämtningsfilen (.sh) i en mapp på klientdatorn.

    ![Hämta alternativ för sparade skriptfil](./media/oracle-backup-recovery/recovery_service_15.png)

5. Kopiera .sh-filen till den virtuella datorn.

    I följande exempel visas hur du använder ett kommando för säker kopia (scp) för att flytta filen till den virtuella datorn. Du kan också kopiera innehållet till Urklipp och sedan klistra in innehållet i en ny fil som har konfigurerats på den virtuella datorn.

    > [!IMPORTANT]
    > I följande exempel kontrollerar du att du uppdaterar IP-adress- och mappvärdena. Värdena måste mappas till mappen där filen sparas.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Ändra filen så att den ägs av roten.

    I följande exempel ändrar du filen så att den ägs av roten. Ändra sedan behörigheter.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    I följande exempel visas vad du bör se när du har kört det föregående skriptet. När du uppmanas att fortsätta anger du **Y**.

    ```output
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

7. Åtkomsten till de monterade volymerna bekräftas.

    Om du vill avsluta anger du **q**och söker sedan efter de monterade volymerna. Om du vill skapa en lista över de tillagda volymerna anger du **df -k**i en kommandotolk .

    ![Kommandot df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Använd följande skript för att kopiera de saknade filerna tillbaka till mapparna:

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

9. I följande skript använder du RMAN för att återställa databasen:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

10. Avmontera disken.

    Klicka på **Avmontera diskar**i bladet **Filåterställning (förhandsversion)** i Azure-portalen.

    ![Kommandot Avmontera diskar](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Återställa hela den virtuella datorn

I stället för att återställa de borttagna filerna från Recovery Services-valven kan du återställa hela den virtuella datorn.

### <a name="step-1-delete-myvm"></a>Steg 1: Ta bort myVM

*   Gå till **myVM1-valvet** i Azure-portalen och välj sedan **Ta bort**.

    ![Kommandot Ta bort valv](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Steg 2: Återställa den virtuella datorn

1.  Gå till **Recovery Services-valv**och välj sedan **myVault**.

    ![myVault-post](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Välj antalet objekt under **Säkerhetskopierade objekt**på bladet **Översikt.**

    ![myVault säkerhetskopiera objekt](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Välj **myvm1**på bladet **Säkerhetskopior (Azure Virtual Machine).**

    ![Sidan Återställning virtuell dator](./media/oracle-backup-recovery/recover_vm_04.png)

4.  På **myvm1-bladet** klickar du på ellipsen (**...**) och klickar sedan på **Återställ virtuell dator**.

    ![Återställa vm-kommando](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Markera det objekt som du vill återställa på bladet **Välj återställningspunkt** och klicka sedan på **OK**.

    ![Markera återställningspunkten](./media/oracle-backup-recovery/recover_vm_06.png)

    Om du har aktiverat programkonsekvent säkerhetskopiering visas ett lodrätt blått fält.

6.  På **bladet Återställ konfiguration** väljer du namnet på den virtuella datorn, väljer resursgruppen och klickar sedan på **OK**.

    ![Återställa konfigurationsvärden](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Om du vill återställa den virtuella datorn klickar du på knappen **Återställ.**

8.  Om du vill visa status för återställningsprocessen klickar du på **Jobb**och sedan **på Säkerhetskopieringsjobb**.

    ![Statuskommando för säkerhetskopieringsjobb](./media/oracle-backup-recovery/recover_vm_08.png)

    Följande bild visar status för återställningsprocessen:

    ![Status för återställningsprocessen](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Steg 3: Ange den offentliga IP-adressen
När den virtuella datorn har återställts ställer du in den offentliga IP-adressen.

1.  Ange **offentlig IP-adress**i sökrutan .

    ![Lista över offentliga IP-adresser](./media/oracle-backup-recovery/create_ip_00.png)

2.  Klicka på **Lägg till**på bladet **Offentliga IP-adresser.** Välj det offentliga IP-namnet för **Namn**på bladet **Skapa offentlig IP-adress.** För **resursgrupp**, väljer du **använd befintlig**. Klicka på **Skapa**.

    ![Skapa IP-adress](./media/oracle-backup-recovery/create_ip_01.png)

3.  Om du vill associera den offentliga IP-adressen med nätverksgränssnittet för den virtuella datorn söker du efter och väljer **myVMip**. Klicka sedan på **Associera**.

    ![Associera IP-adress](./media/oracle-backup-recovery/create_ip_02.png)

4.  För **resurstyp**väljer du **Nätverksgränssnitt**. Välj det nätverksgränssnitt som används av myVM-instansen och klicka sedan på **OK**.

    ![Välj resurstyp och nätverkskortsvärden](./media/oracle-backup-recovery/create_ip_03.png)

5.  Sök efter och öppna instansen av myVM som är portad från portalen. IP-adressen som är associerad med den **Overview** virtuella datorn visas på myVM Overview-bladet.

    ![IP-adressvärde](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Steg 4: Anslut till den virtuella datorn

*   Om du vill ansluta till den virtuella datorn använder du följande skript:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Steg 5: Testa om databasen är tillgänglig
*   Om du vill testa hjälpmedel använder du följande skript:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Om **startkommandot** för databasen genererar ett fel läser du Steg [6: Använd RMAN för att återställa databasen](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Steg 6: (Valfritt) Använd RMAN för att återställa databasen
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

Säkerhetskopieringen och återställningen av Oracle Database 12c-databasen på en Virtuell Azure Linux är nu klar.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

När du inte längre behöver den virtuella datorn kan du använda följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Skapa virtuella datorer med hög tillgång](../../linux/create-cli-complete.md)

[Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)



