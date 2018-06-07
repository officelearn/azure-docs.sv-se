---
title: Skapa en Oracle-databas i en Azure VM | Microsoft Docs
description: Snabbt en Oracle-databas 12c-databas och körs i Azure-miljön.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8ff463b89b395947a66db6d067b6ba32281087ba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657906"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Skapa en Oracle-databas i en virtuell dator i Azure

Den här guiden information som använder Azure CLI för att distribuera en virtuell Azure-dator från den [Oracle marketplace-avbildning i galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) för att skapa en Oracle 12 c-databas. När servern har distribuerats, ansluter du via SSH för att kunna konfigurera Oracle-databasen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Du kan skapa en virtuell dator (VM) med den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. 

Följande exempel skapar en virtuell dator med namnet `myVM`. Det skapar också SSH-nycklar, om de inte redan finns på standardplatsen nyckel. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

När du har skapat den virtuella datorn, visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress`. Du kan använda den här adressen för att få åtkomst till den virtuella datorn.

```azurecli
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

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt IP-adressen med den `publicIpAddress` värde för den virtuella datorn.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Skapa en databas

Oracle-programvara är installerad på Marketplace-avbildning. Skapa en exempeldatabas på följande sätt. 

1.  Växla till den *oracle* superanvändare och sedan initiera lyssnaren för loggning:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    De utdata som genereras liknar följande:

    ```bash
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

2.  skapa databasen:

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

3. Ange variabler för Oracle

Innan du ansluter måste du ställa in två miljövariabler: *ORACLE_HOME* och *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Du kan också lägga till ORACLE_HOME och ORACLE_SID variabler i filen .bashrc. Detta skulle spara miljövariabler för framtida inloggningar. Bekräfta följande påståenden har lagts till i `~/.bashrc` fil med valfri redigerare.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM snabb anslutning

För ett GUI-hanteringsverktyg som du kan använda för att utforska databasen, ställa in Oracle EM Express. Om du vill ansluta till Oracle EM Express, måste du först ställa in port i Oracle. 

1. Anslut till databasen med sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. När du är ansluten, anger du porten 5502 för EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Öppna den behållaren PDB1 om den inte redan öppnad men första Kontrollera status:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De utdata som genereras liknar följande:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Om OPEN_MODE för `PDB1` är inte läsa skriva, kör du följande kommandon för att öppna PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Du måste ange `quit` att avsluta sessionen sqlplus och typen `exit` logga ut för oracle-användare.

## <a name="automate-database-startup-and-shutdown"></a>Automatisera databasen start och stopp

Oracle-databasen som standard startas inte automatiskt när du startar om den virtuella datorn. Om du vill ställa in Oracle-databasen för att starta automatiskt, först logga in som rot. Sedan skapa och uppdatera vissa systemfiler.

1. Logga in som rot
    ```bash
    sudo su -
    ```

2.  Med din favorit redigerare, redigerar du filen `/etc/oratab` och ändra standard `N` till `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Skapa en fil med namnet `/etc/init.d/dbora` och klistra in följande innehåll:

    ```
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

4.  Ändra behörigheter för filer med *chmod* på följande sätt:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Skapa symboliska länkar för start och stopp enligt följande:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Testa dina ändringar genom att starta om den virtuella datorn:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Öppna portar för anslutning

Det sista steget är att konfigurera vissa externa slutpunkter. Avsluta SSH-sessionen på den virtuella datorn (bör ha har dessutom out-of-SSH när omstart i föregående steg) för att ställa in Azure Nätverkssäkerhetsgruppen som skyddar den virtuella datorn. 

1.  Om du vill öppna den slutpunkt som du använder för att få fjärråtkomst till Oracle-databasen, skapar du en Nätverkssäkerhetsgrupp regel med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) på följande sätt: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Om du vill öppna den slutpunkt som du använder för att fjärransluta till Oracle EM Express, skapar du en Nätverkssäkerhetsgrupp regel med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) på följande sätt:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Om det behövs kan hämta den offentliga IP-adressen på den virtuella datorn igen med [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show) på följande sätt:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Ansluta EM Express från din webbläsare. Kontrollera att din webbläsare är kompatibelt med EM Express (Flash installation krävs): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Du kan logga in med hjälp av den **SYS** konto och kontrollera den **som sysdba** kryssrutan. Använd lösenord **OraPasswd1** som du anger under installationen. 

![Skärmbild av inloggningssidan Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med att utforska din första Oracle-databas på Azure och den virtuella datorn inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#az_group_delete) kommandot för att ta bort resursgruppen VM, och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Oracle-lösningar på Azure](oracle-considerations.md). 

Försök i [installera och konfigurera Oracle Automated lagringshantering](configure-oracle-asm.md) kursen.