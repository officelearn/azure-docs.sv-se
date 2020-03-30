---
title: Skapa en Oracle-databas i en virtuell Azure-dator | Microsoft-dokument
description: Snabbt få igång en Oracle Database 12c-databas i din Azure-miljö.
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
ms.openlocfilehash: 9f4b9d53aaa1cac17fbaae4b638e144654fad4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535637"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Skapa en Oracle-databas i en virtuell Azure-dator

Den här guiden innehåller information om hur du använder Azure CLI för att distribuera en virtuell Azure-dator från [Oracle marketplace-galleriavbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) för att skapa en Oracle 12c-databas. När servern har distribuerats kommer du att ansluta via SSH för att konfigurera Oracle-databasen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator (VM) använder du kommandot [az vm create.](/cli/azure/vm) 

Följande exempel skapar en virtuell dator med namnet `myVM`. Det skapar också SSH nycklar, om de inte redan finns i en standardnyckel plats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Notera värdet `publicIpAddress`för . Du använder den här adressen för att komma åt den virtuella datorn.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Om du vill skapa en SSH-session med den virtuella datorn använder du följande kommando. Ersätt IP-adressen `publicIpAddress` med värdet för den virtuella datorn.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Skapa databasen

Oracle-programvaran är redan installerad på Marketplace-avbildningen. Skapa en exempeldatabas enligt följande. 

1.  Växla till *oracle* superuser och sedan initiera lyssnaren för loggning:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    De utdata som genereras liknar följande:

    ```output
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

2.  Skapa databasen:

    ```bash
    dbca -silent \
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

    Det tar några minuter att skapa databasen.

3. Ange Oracle-variabler

Innan du ansluter måste du ställa in två miljövariabler: *ORACLE_HOME* och *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

Du kan också lägga till ORACLE_HOME och ORACLE_SID variabler i .bashrc-filen. Detta skulle spara miljövariabler för framtida inloggningar. Bekräfta att `~/.bashrc` följande satser har lagts till i filen med valfri redigerare.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-anslutning

Skapa Oracle EM Express för ett guidningshanteringsverktyg som du kan använda för att utforska databasen. Om du vill ansluta till Oracle EM Express måste du först konfigurera porten i Oracle. 

1. Anslut till databasen med sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. När du är ansluten ställer du in porten 5502 för EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Öppna behållaren PDB1 om den inte redan har öppnats, men kontrollera först status:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De utdata som genereras liknar följande:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Om OPEN_MODE för inte `PDB1` läs skrivs kör du följande kommandon för att öppna PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Du måste `quit` skriva för att avsluta `exit` sqlplus-sessionen och skriva till utloggning av oracle-användaren.

## <a name="automate-database-startup-and-shutdown"></a>Automatisera start och avstängning av databasen

Oracle-databasen startar som standard inte automatiskt när du startar om den virtuella datorn. Om du vill konfigurera Oracle-databasen så att den startar automatiskt loggar du först in som root. Skapa och uppdatera sedan vissa systemfiler.

1. Logga in som rot

    ```bash
    sudo su -
    ```

2.  Redigera filen `/etc/oratab` med din favoritredigerare och ändra standardinställningen `N` till: `Y`

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Skapa en `/etc/init.d/dbora` fil med namnet och klistra in följande innehåll:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Ändra behörigheter för filer med *chmod* enligt följande:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Skapa symboliska länkar för start och avstängning enligt följande:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Starta om den virtuella datorn om du vill testa ändringarna:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Öppna portar för anslutning

Den sista uppgiften är att konfigurera vissa externa slutpunkter. Om du vill konfigurera Azure Network Security Group som skyddar den virtuella datorn avslutar du först SSH-sessionen i den virtuella datorn (borde ha sparkats ut från SSH vid omstart i föregående steg). 

1.  Om du vill öppna slutpunkten som du använder för att fjärransluta till Oracle-databasen skapar du en regel för nätverkssäkerhetsgrupp med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule) enligt följande: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Om du vill öppna slutpunkten som du använder för att fjärransluta till Oracle EM Express skapar du en regel för nätverkssäkerhetsgrupp med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule) enligt följande:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Om det behövs, hämta den offentliga [IP-adressen](/cli/azure/network/public-ip) för din virtuella dator igen med az network public-ip show enligt följande:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Anslut EM Express från din webbläsare. Kontrollera att din webbläsare är kompatibel med EM Express (Flash-installation krävs): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Du kan logga in med hjälp av **SYS-kontot** och markera kryssrutan **som sysdba.** Använd lösenordet **OraPasswd1** som du ställer in under installationen. 

![Skärmbild av inloggningssidan för Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du har utforskat din första Oracle-databas på Azure och den virtuella datorn inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Oracle-lösningar på Azure](oracle-considerations.md). 

Prova [självstudien Installera och konfigurera Oracles automatiska lagringshantering.](configure-oracle-asm.md)
