---
title: Säkerhetskopiera och återställa en Oracle Database 12C-databas på en virtuell Azure Linux-dator | Microsoft Docs
description: Lär dig hur du säkerhetskopierar och återställer en Oracle Database 12C-databas i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: c5f02117d3af7fb411c75d783df82f6008d8104e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687007"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Säkerhetskopiera och återställa en Oracle Database 12C-databas på en virtuell Azure Linux-dator

Du kan använda Azure CLI för att skapa och hantera Azure-resurser i en kommando tolk eller använda skript. I den här artikeln använder vi Azure CLI-skript för att distribuera en Oracle Database 12C-databas från en Azure Marketplace-Galleri avbildning.

Innan du börjar ska du kontrol lera att Azure CLI är installerat. Mer information finns i [installations guiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="step-1-prerequisites"></a>Steg 1: krav

*   Om du vill utföra säkerhets kopierings-och återställnings processen måste du först skapa en virtuell Linux-dator som har en installerad instans av Oracle Database 12C. Marketplace-avbildningen som du använder för att skapa den virtuella datorn heter *Oracle: Oracle-Database-EE: 12.1.0.2: senaste*.

    Information om hur du skapar en Oracle-databas finns i [snabb starten för Oracle Create Database](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Steg 2: Anslut till den virtuella datorn

*   Använd följande kommando om du vill skapa en SSH-session (Secure Shell) med den virtuella datorn. Ersätt IP-adressen och värd namns kombinationen med `publicIpAddress` värdet för den virtuella datorn.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Steg 3: Förbered databasen

1.  Det här steget förutsätter att du har en Oracle-instans (cdb1) som körs på en virtuell dator med namnet *myVM*.

    Kör katalogen för superanvändare i *Oracle* och initiera sedan lyssnaren:

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

2.  Valfritt Kontrol lera att databasen är i arkivet logg läge:

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

3.  Valfritt Skapa en tabell för att testa incheckningen:

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

4.  Verifiera eller ändra plats och storlek för säkerhets kopierings filen:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Använd Oracle-Recovery Manager (RMAN) för att säkerhetskopiera databasen:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Steg 4: programkonsekvent säkerhets kopiering för virtuella Linux-datorer

Programkonsekventa säkerhets kopieringar är en ny funktion i Azure Backup. Du kan skapa och välja skript som ska köras före och efter ögonblicks bilden av den virtuella datorn (för ögonblicks bilder och efter ögonblicks bilder).

1. Ladda ned JSON-filen.

    Hämta VMSnapshotScriptPluginConfig. JSON från https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Fil innehållet ser ut ungefär så här:

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

2. Skapa mappen/etc/Azure på den virtuella datorn:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Kopiera JSON-filen.

    Kopiera VMSnapshotScriptPluginConfig. JSON till mappen/etc/Azure.

4. Redigera JSON-filen.

    Redigera filen VMSnapshotScriptPluginConfig. JSON för att inkludera parametrarna `PreScriptLocation` och `PostScriptlocation` . Ett exempel:

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

5. Skapa skriptfiler för ögonblicks bild och efter ögonblicks bild.

    Här är ett exempel på skript för ögonblicks bilder och efter ögonblicks bilder för en "kall säkerhets kopiering" (säkerhets kopiering offline, med avstängning och omstart):

    För/etc/Azure/pre_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    För/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Här är ett exempel på skript före och efter ögonblicks bilder för en "snabb säkerhets kopiering" (en onlinesäkerhetskopiering):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    För/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    För/etc/Azure/pre_script. SQL ändrar du filens innehåll enligt dina krav:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    För/etc/Azure/post_script. SQL ändrar du filens innehåll enligt dina krav:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Ändra fil behörigheter:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testa skripten.

    Om du vill testa skripten måste du först logga in som rot. Kontrol lera sedan att det inte finns några fel:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Mer information finns i [programkonsekvent säkerhets kopiering för virtuella Linux-datorer](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Steg 5: Använd Azure Recovery Services-valv för att säkerhetskopiera den virtuella datorn

1.  Sök efter **Recovery Services-valv**i Azure Portal.

    ![Sidan Recovery Services valv](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Klicka på **Lägg**till på bladet **Recovery Services valv** för att lägga till ett nytt valv.

    ![Lägg till sida i Recovery Services valv](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Fortsätt genom att klicka på **valv**.

    ![Informations sida för Recovery Services valv](./media/oracle-backup-recovery/recovery_service_03.png)

4.  På bladet mina **valv** klickar du på **säkerhets kopiering**.

    ![Sidan för säkerhets kopiering av Recovery Services valv](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Använd standardvärdena för **Azure** och den **virtuella datorn**på bladet **säkerhets kopierings mål** . Klicka på **OK**.

    ![Informations sida för Recovery Services valv](./media/oracle-backup-recovery/recovery_service_05.png)

6.  För **säkerhets kopierings princip**använder du **DefaultPolicy**, eller så väljer du **Skapa ny princip**. Klicka på **OK**.

    ![Informations sida för säkerhets kopiering av Recovery Services valv](./media/oracle-backup-recovery/recovery_service_06.png)

7.  På bladet **Välj virtuella datorer** markerar du kryss rutan **myVM1** och klickar sedan på **OK**. Klicka på knappen **Aktivera säkerhets kopiering** .

    ![Recovery Services valv objekt på informations sidan för säkerhets kopiering](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > När du har klickat på **Aktivera säkerhets kopiering**startar inte säkerhets kopieringen förrän den schemalagda tiden har gått ut. Slutför nästa steg för att konfigurera en omedelbar säkerhets kopiering.

8.  På bladet för att **säkerhetskopiera objekt** under **antal säkerhets kopierings**objekt väljer du antalet säkerhets kopierings objekt.

    ![Informations sida för Recovery Services valv](./media/oracle-backup-recovery/recovery_service_08.png)

9.  På bladet **säkerhets kopierings objekt (virtuell Azure-dator)** klickar du på knappen med tre punkter (**...**) på höger sida av sidan och klickar sedan på **Säkerhetskopiera nu**.

    ![Kommandot Säkerhetskopiera nu Recovery Services valv](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klicka på knappen **säkerhetskopiera** . Vänta tills säkerhets kopierings processen har slutförts. Gå sedan till [steg 6: ta bort databasfilerna](#step-6-remove-the-database-files).

    Klicka på **jobb**om du vill visa säkerhets kopierings jobbets status.

    ![Jobb sida för Recovery Services valv](./media/oracle-backup-recovery/recovery_service_10.png)

    Säkerhets kopierings jobbets status visas i följande bild:

    ![Jobb sidan Recovery Services valv med status](./media/oracle-backup-recovery/recovery_service_11.png)

11. För en programkonsekvent säkerhets kopiering kan du åtgärda eventuella fel i logg filen. Logg filen finns på/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Steg 6: ta bort databasfilerna 
Senare i den här artikeln får du lära dig hur du testar återställnings processen. Innan du kan testa återställnings processen måste du ta bort databasfilerna.

1.  Ta bort tabell utrymmet och säkerhets kopian:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Valfritt Stäng av Oracle-instansen:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Återställa de borttagna filerna från Recovery Services-valv
Utför följande steg för att återställa de borttagna filerna:

1. I Azure Portal söker du efter objektet valv *Recovery Services valv* . I **översikts** bladet under **säkerhets kopierings objekt**väljer du antalet objekt.

    ![Säkerhets kopierings objekt för Recovery Services valv](./media/oracle-backup-recovery/recovery_service_12.png)

2. Under **antal säkerhets kopierings objekt**väljer du antalet objekt.

    ![Recovery Services valv antal säkerhets kopierings objekt för virtuella Azure-datorer](./media/oracle-backup-recovery/recovery_service_13.png)

3. På bladet **myvm1** klickar du på **fil återställning (för hands version)**.

    ![Skärm bild av sidan Recovery Services valv fil återställning](./media/oracle-backup-recovery/recovery_service_14.png)

4. I fönstret **fil återställning (för hands version)** klickar du på **Hämta skript**. Spara sedan filen Download (. sh) i en mapp på klient datorn.

    ![Hämta alternativ för att spara skript fil](./media/oracle-backup-recovery/recovery_service_15.png)

5. Kopiera. sh-filen till den virtuella datorn.

    I följande exempel visas hur du använder ett säkert kopierings kommando (SCP) för att flytta filen till den virtuella datorn. Du kan också kopiera innehållet till Urklipp och sedan klistra in innehållet i en ny fil som har kon figurer ATS på den virtuella datorn.

    > [!IMPORTANT]
    > I följande exempel ser du till att du uppdaterar IP-adress och mappegenskaper. Värdena måste mappas till den mapp där filen sparas.

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

    I följande exempel visas vad du bör se när du har kört föregående skript. När du uppmanas att fortsätta anger du **Y**.

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

7. Åtkomst till monterade volymer bekräftas.

    Avsluta genom att ange **q**och sedan söka efter de monterade volymerna. Om du vill skapa en lista över de tillagda volymerna skriver du **DF-k**i kommando tolken.

    ![Kommandot DF-k](./media/oracle-backup-recovery/recovery_service_16.png)

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

    I Azure Portal på bladet **fil återställning (för hands version)** klickar du på **demontera diskar**.

    ![Demontera diskar, kommando](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Återställa hela den virtuella datorn

I stället för att återställa de borttagna filerna från Recovery Services-valven kan du återställa hela den virtuella datorn.

### <a name="step-1-delete-myvm"></a>Steg 1: ta bort myVM

*   Gå till **myVM1** -valvet i Azure Portal och välj sedan **ta bort**.

    ![Valv borttagnings kommando](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Steg 2: återställa den virtuella datorn

1.  Gå till **Recovery Services valv**och välj sedan **valv**.

    ![post för valv](./media/oracle-backup-recovery/recover_vm_02.png)

2.  I **översikts** bladet under **säkerhets kopierings objekt**väljer du antalet objekt.

    ![säkerhetskopierar objekt](./media/oracle-backup-recovery/recover_vm_03.png)

3.  På bladet **säkerhets kopierings objekt (virtuell Azure-dator)** väljer du **myvm1**.

    ![Sidan Återställ virtuell dator](./media/oracle-backup-recovery/recover_vm_04.png)

4.  På bladet **myvm1** klickar du på knappen med tre punkter (**...**) och klickar sedan på **Återställ virtuell dator**.

    ![Återställ VM-kommando](./media/oracle-backup-recovery/recover_vm_05.png)

5.  På bladet **Välj återställnings punkt** väljer du det objekt som du vill återställa och klickar sedan på **OK**.

    ![Välj återställnings punkt](./media/oracle-backup-recovery/recover_vm_06.png)

    Om du har aktiverat programkonsekvent säkerhets kopiering visas ett lodrätt blått fält.

6.  På bladet **Återställ konfiguration** väljer du namnet på den virtuella datorn, väljer resurs gruppen och klickar sedan på **OK**.

    ![Återställ konfigurations värden](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Om du vill återställa den virtuella datorn klickar du på knappen **Återställ** .

8.  Visa status för återställnings processen genom att klicka på **jobb**och sedan på **säkerhets kopierings jobb**.

    ![Status kommando för säkerhets kopierings jobb](./media/oracle-backup-recovery/recover_vm_08.png)

    Följande bild visar status för återställnings processen:

    ![Status för återställnings processen](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Steg 3: Ange den offentliga IP-adressen
När den virtuella datorn har återställts konfigurerar du den offentliga IP-adressen.

1.  I rutan Sök anger du **offentlig IP-adress**.

    ![Lista över offentliga IP-adresser](./media/oracle-backup-recovery/create_ip_00.png)

2.  Klicka på **Lägg till**på bladet **offentliga IP-adresser** . På bladet **skapa offentlig IP-adress** väljer **du namnet på**den offentliga IP-adressen. För **resursgrupp**, väljer du **använd befintlig**. Klicka på **Skapa**.

    ![Skapa IP-adress](./media/oracle-backup-recovery/create_ip_01.png)

3.  Om du vill associera den offentliga IP-adressen med nätverks gränssnittet för den virtuella datorn söker du efter och väljer **myVMip**. Klicka sedan på **associera**.

    ![Associera IP-adress](./media/oracle-backup-recovery/create_ip_02.png)

4.  För **resurs typ**väljer du **nätverks gränssnitt**. Välj det nätverks gränssnitt som används av myVM-instansen och klicka sedan på **OK**.

    ![Välj resurs typ och NIC-värden](./media/oracle-backup-recovery/create_ip_03.png)

5.  Sök efter och öppna den instans av myVM som är portad från portalen. Den IP-adress som är associerad med den virtuella datorn visas på bladet myVM- **Översikt** .

    ![IP-adress värde](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Steg 4: Anslut till den virtuella datorn

*   Använd följande skript för att ansluta till den virtuella datorn:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Steg 5: testa om databasen är tillgänglig
*   Om du vill testa tillgänglighet använder du följande skript:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Om **Start** kommandot för databasen genererar ett fel, för att återställa-databasen, se [steg 6: använda rman för att återställa databasen](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Steg 6: (valfritt) Använd RMAN för att återställa databasen
*   Använd följande skript för att återställa databasen:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Säkerhets kopiering och återställning av Oracle Database 12C-databasen på en virtuell Azure Linux-dator har nu slutförts.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

När du inte längre behöver den virtuella datorn kan du använda följande kommando för att ta bort resurs gruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)

[Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)



