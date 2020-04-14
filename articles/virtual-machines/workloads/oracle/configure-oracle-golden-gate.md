---
title: Implementera Oracle Golden Gate på en virtuell Azure Linux-dator | Microsoft-dokument
description: Snabbt få en Oracle Golden Gate igång i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: d7a52f09463d03fc6ecf0b57583d9eb7a5e65d45
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263156"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementera Oracle Golden Gate på en virtuell Azure Linux-dator 

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur du använder Azure CLI för att distribuera en Oracle 12c-databas från Azure Marketplace-galleriavbildningen. 

Det här dokumentet visar hur du skapar, installerar och konfigurerar Oracle Golden Gate på en virtuell Azure-dator. I den här självstudien konfigureras två virtuella datorer i en tillgänglighetsuppsättning i en enda region. Samma självstudiekurs kan användas för att konfigurera OracleGolden Gate för virtuella datorer i olika tillgänglighetszoner i en enda Azure-region eller för virtuella datorer i två olika regioner.

Kontrollera att Azure CLI har installerats innan du börjar. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Förbereda miljön

För att utföra Oracle Golden Gate-installationen måste du skapa två virtuella Azure-datorer på samma tillgänglighetsuppsättning. Marketplace-avbildningen som du använder för att skapa de virtuella datorerna är **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Du måste också vara bekant med Unix editor vi och har en grundläggande förståelse för x11 (X Windows).

Följande är en sammanfattning av miljökonfigurationen:
> 
> |  | **Primär plats** | **Replikera plats** |
> | --- | --- | --- |
> | **Oracle release** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Maskinens namn** |myVM1 (på väg tillbaka) |myVM2 (på väg mot andra) |
> | **Operativsystem** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Orakkel SID** |CDB1 (PÅ ANDRA) |CDB1 (PÅ ANDRA) |
> | **Replikeringsschema** |TEST|TEST |
> | **Golden Gate ägare / replikera** |C##GGADMIN |REPUSER (ANDRA) |
> | **Golden Gate-processen** |EXTORA |REPORA (AV ANDRA)|


### <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på din Azure-prenumeration med kommandot [az login.](/cli/azure/reference-index) Följ sedan anvisningarna på skärmen.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk behållare som Azure-resurser distribueras till och från vilken de kan hanteras. 

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Följande steg är valfritt men rekommenderas. Mer information finns i [Azure-guiden för tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm). 

I följande exempel skapas `myVM1` två `myVM2`virtuella datorer med namnet och . Skapa SSH-nycklar om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Skapa myVM1 (primär):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

När den virtuella datorn har skapats visar Azure CLI information som liknar följande exempel. (Notera `publicIpAddress`. Den här adressen används för att komma åt den virtuella datorn.)

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Skapa myVM2 (replikera):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Ta del `publicIpAddress` av också efter det att det har skapats.

### <a name="open-the-tcp-port-for-connectivity"></a>Öppna TCP-porten för anslutning

Nästa steg är att konfigurera externa slutpunkter, vilket gör att du kan komma åt Oracle-databasen på distans. Om du vill konfigurera de externa slutpunkterna kör du följande kommandon.

#### <a name="open-the-port-for-myvm1"></a>Öppna porten för myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Resultaten bör se ut ungefär som följande svar:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Öppna porten för myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt IP-adressen med `publicIpAddress` för den virtuella datorn.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Skapa databasen på myVM1 (primär)

Oracle-programvaran är redan installerad på Marketplace-avbildningen, så nästa steg är att installera databasen. 

Kör programvaran som "oraklet" superuser:

```bash
sudo su - oracle
```

Skapa databasen:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Utdata bör se ut ungefär som följande svar:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Ange variablerna ORACLE_SID och ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Du kan också lägga till ORACLE_HOME och ORACLE_SID i .bashrc-filen, så att dessa inställningar sparas för framtida inloggningar:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Starta Oracle-lyssnaren

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Skapa databasen på myVM2 (replikera)

```bash
sudo su - oracle
```

Skapa databasen:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Ange variablerna ORACLE_SID och ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Du kan också lägga till ORACLE_HOME och ORACLE_SID i .bashrc-filen, så att dessa inställningar sparas för framtida inloggningar.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Starta Oracle-lyssnaren

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurera Golden Gate 
Om du vill konfigurera Golden Gate gör du stegen i det här avsnittet.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Aktivera arkivloggläge på myVM1 (primärt)

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
```
Aktivera kraftloggning och kontrollera att det finns minst en loggfil.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Ladda ner Golden Gate programvara
Så här hämtar och förbereder du Programvaran Oracle Golden Gate:

1. Ladda ner **filen fbo_ggs_Linux_x64_shiphome.zip** från [Oracle Golden Gate nedladdningssida](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Under nedladdningstiteln **Oracle GoldenGate 12.x.x.x för Oracle Linux x86-64,** bör det finnas en uppsättning .zip-filer att ladda ner.

2. När du har hämtat ZIP-filerna till klientdatorn använder du SCP (Secure Copy Protocol) för att kopiera filerna till den virtuella datorn:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Flytta ZIP-filerna till mappen **/opt.** Ändra sedan ägaren av filerna enligt följande:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Packa upp filerna (installera Verktyget Linux packa upp om det inte redan är installerat):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Ändra behörighet:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Förbereda klienten och den virtuella datorn för att köra x11 (endast för Windows-klienter)
Detta är ett valfritt steg. Du kan hoppa över det här steget om du använder en Linux-klient eller redan har x11-konfiguration.

1. Ladda ner PuTTY och Xming till din Windows-dator:

   * [Ladda ner PuTTY](https://www.putty.org/)
   * [Ladda ner Xming](https://xming.en.softonic.com/)

2. När du har installerat PuTTY kör puttygen.exe (PuTTY-nyckelgeneratorn) i mappen PuTTY (till exempel C:\Program Files\PuTTY).

3. I PuTTY nyckel generera:

   - Om du vill generera en nyckel väljer du knappen **Generera.**
   - Kopiera innehållet i nyckeln (**Ctrl+C**).
   - Välj knappen **Spara privat nyckel.**
   - Ignorera varningen som visas och välj sedan **OK**.

   ![Skärmbild av PuTTY-nyckelgenererarsidan](./media/oracle-golden-gate/puttykeygen.png)

4. Kör dessa kommandon i den virtuella datorn:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Skapa en fil med namnet **authorized_keys**. Klistra in innehållet i nyckeln i den här filen och spara sedan filen.

   > [!NOTE]
   > Nyckeln måste innehålla `ssh-rsa`strängen . Dessutom måste innehållet i nyckeln vara en enda textrad.
   >  

6. Starta PuTTY. Välj **Anslutning** > **SSH** > **Auth**i **kategorifönstret** . Bläddra till nyckeln som du genererade tidigare i rutan **Privat nyckelfil för autentisering.**

   ![Skärmbild av sidan Ange privat nyckel](./media/oracle-golden-gate/setprivatekey.png)

7. Välj **Anslutning** > **SSH** > **X11**i **kategorifönstret** . Välj sedan rutan **Aktivera X11-vidarebefordran.**

   ![Skärmbild av sidan Aktivera X11](./media/oracle-golden-gate/enablex11.png)

8. Gå till **Session**i **kategorifönstret** . Ange värdinformationen och välj sedan **Öppna**.

   ![Skärmbild av sessionssidan](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Installera Golden Gate-programvara

Så här installerar du Oracle Golden Gate:

1. Logga in som orakel. (Du bör kunna logga in utan att bli tillfrågad om ett lösenord.) Kontrollera att Xming körs innan du påbörjar installationen.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Välj "Oracle GoldenGate för Oracle Database 12c". Välj sedan **Nästa** för att fortsätta.

   ![Skärmbild av sidan Välj installationsprogram](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Ändra programvaruplatsen. Välj sedan rutan **Starthanteraren** och ange databasplatsen. Välj **Nästa** för att fortsätta.

   ![Skärmbild av sidan Välj installation](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Ändra lagerkatalogen och välj sedan **Nästa** för att fortsätta.

   ![Skärmbild av sidan Välj installation](./media/oracle-golden-gate/golden_gate_install_03.png)

5. På skärmen **Sammanfattning** väljer du **Installera** för att fortsätta.

   ![Skärmbild av sidan Välj installationsprogram](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Du kan uppmanas att köra ett skript som "root". Om så är fallet, öppna en separat session, ssh till den virtuella datorn, sudo till rot, och sedan köra skriptet. Välj **OK** fortsätt.

   ![Skärmbild av sidan Välj installation](./media/oracle-golden-gate/golden_gate_install_05.png)

7. När installationen är klar väljer du **Stäng** för att slutföra processen.

   ![Skärmbild av sidan Välj installation](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Konfigurera tjänst på myVM1 (primär)

1. Skapa eller uppdatera filen tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Skapa Golden Gate-ägaren och användarkontona.

   > [!NOTE]
   > Ägarkontot måste ha C## prefix.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Skapa användarkontot för Golden Gate-testet:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Konfigurera parameterfilen för utdrag.

   Starta kommandoradsgränssnittet för Golden Gate (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. Lägg till följande i PARAMETERFILEN EXTRACT (med hjälp av vi-kommandon). Tryck på Esc, ':wq!' för att spara filen. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. Registrera utdrag- integrerat utdrag:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Ställ in extrahera kontrollpunkter och starta utdrag i realtid:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   I det här steget hittar du start-SCN, som kommer att användas senare, i ett annat avsnitt:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Konfigurera tjänst på myVM2 (replikerad)


1. Skapa eller uppdatera filen tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Skapa ett replikerat konto:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Skapa ett Golden Gate-testanvändarkonto:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. REPET-parameterfil för att replikera ändringar: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Innehållet i REPORA-parameterfilen:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Konfigurera en kontrollpunkt för replikerade:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Konfigurera replikeringen (myVM1 och myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Ställ in replikeringen på myVM2 (replikera)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Uppdatera filen med följande:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Starta sedan om tjänsten Chef:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Ställ in replikeringen på myVM1 (primär)

Starta den första belastningen och kontrollera om det finns fel:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Ställ in replikeringen på myVM2 (replikera)

Ändra SCN-numret med det nummer du fick före:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

Replikeringen har påbörjats och du kan testa den genom att infoga nya poster i TEST-tabeller.


### <a name="view-job-status-and-troubleshooting"></a>Visa jobbstatus och felsökning

#### <a name="view-reports"></a>Visa rapporter
Om du vill visa rapporter om myVM1 kör du följande kommandon:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Om du vill visa rapporter på myVM2 kör du följande kommandon:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Visa status och historik
Om du vill visa status och historik på myVM1 kör du följande kommandon:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Om du vill visa status och historik på myVM2 kör du följande kommandon:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Detta slutför installationen och konfigurationen av Golden Gate på Oracle linux.


## <a name="delete-the-virtual-machine"></a>Ta bort den virtuella datorn

När det inte längre behövs kan följande kommando användas för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: Skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)

[Utforska exempel på distribution av virtuella datorer med CLI](../../linux/cli-samples.md)
