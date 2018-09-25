---
title: Skapa en Oracle-databas i en Azure-dator | Microsoft Docs
description: Snabbt en Oracle Database 12c-databas för dig och kom igång med Azure-miljön.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 516552111cc21cacf87fd8179ef49b939e2820d9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985086"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Skapa en Oracle-databas i en Azure virtuell dator

Den här guiden beskriver hur man använder Azure CLI för att distribuera en virtuell dator från den [Oracle marketplace-galleriet bilden](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) för att skapa en Oracle 12 c-databas. När servern har distribuerats ansluter du via SSH för att kunna konfigurera Oracle-databasen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Du kan skapa en virtuell dator (VM) med den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. 

Följande exempel skapar en virtuell dator med namnet `myVM`. Det skapar också SSH-nycklar om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress`. Du kan använda den här adressen för att få åtkomst till den virtuella datorn.

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

Oracle-programvaran är redan installerad på Marketplace-avbildning. Skapa en exempeldatabas på följande sätt. 

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

3. Ange Oracle variabler

Innan du ansluter måste du ange två miljövariabler: *ORACLE_HOME* och *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Du kan också lägga till ORACLE_HOME och ORACLE_SID variabler i filen .bashrc. Detta skulle spara miljövariabler för framtida inloggningar. Bekräfta följande uttryck har lagts till i `~/.bashrc` -fil med valfri redigerare.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-anslutning

För ett GUI-hanteringsverktyg som du kan använda för att utforska databasen, ställa in Oracle EM Express. Om du vill ansluta till Oracle EM Express, måste du först ställa in porten i Oracle. 

1. Anslut till din databas med sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. När du är ansluten, ange porten 5502 för EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Öppna den behållaren PDB1 om den inte redan öppnad men först kontrollera status:

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

Du måste ange `quit` att avsluta sqlplus session och skriv `exit` logga ut för den oracle-användaren.

## <a name="automate-database-startup-and-shutdown"></a>Automatisera databasen start och avstängning

Oracle-databasen som standard startas inte automatiskt när du startar om den virtuella datorn. Om du vill konfigurera Oracle-databasen för att starta automatiskt, loggar in första gången som rot. Sedan kan du skapa och uppdatera vissa systemfiler.

1. Inloggning som rot
    ```bash
    sudo su -
    ```

2.  Med redigeringsprogram du föredrar, redigerar du filen `/etc/oratab` och ändra standard `N` till `Y`:

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

5.  Skapa symboliska länkar för start och avstängning på följande sätt:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Testa ändringarna genom att starta om den virtuella datorn:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Öppna portar för anslutning

Det sista steget är att konfigurera vissa externa slutpunkter. Avsluta SSH-sessionen på den virtuella datorn (bör ha varit går utanför SSH när omstart i föregående steg) om du vill konfigurera Azure Network Security Group, som skyddar den virtuella datorn. 

1.  Öppna den slutpunkt som du använder för att få fjärråtkomst till Oracle-databasen genom att skapa en regel för Nätverkssäkerhetsgrupp [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) på följande sätt: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Öppna den slutpunkt som du använder för att komma åt Oracle EM Express via fjärranslutning genom att skapa en regel för Nätverkssäkerhetsgrupp [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) på följande sätt:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Om det behövs hämtar den offentliga IP-adressen för den virtuella datorn igen med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) på följande sätt:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Anslut EM Express från din webbläsare. Kontrollera att din webbläsare är kompatibelt med EM (Flash installation krävs): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Du kan logga in med hjälp av den **SYS** konto och kontrollera den **som sysdba** kryssrutan. Använd lösenord **OraPasswd1** som du anger under installationen. 

![Skärmbild av inloggningssidan Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med att utforska din första Oracle-databas på Azure och den virtuella datorn inte längre behövs kan du använda den [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen, den virtuella datorn, och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om andra [Oracle-lösningar på Azure](oracle-considerations.md). 

Prova den [installera och konfigurera Oracle automatisk lagringshantering](configure-oracle-asm.md) självstudien.