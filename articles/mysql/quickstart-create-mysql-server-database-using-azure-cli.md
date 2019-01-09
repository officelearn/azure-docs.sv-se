---
title: 'Snabbstart: Skapa en Azure Database for MySQL-server – Azure CLI'
description: I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/01/2018
ms.custom: mvc
ms.openlocfilehash: 4c425e4da99774be5da8c060a8243bc34ec08f5a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547804"
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Skapa en Azure Database för MySQL-server med Azure CLI
I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp på ungefär fem minuter. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med kommandot [az group create](/cli/azure/group#az-group-create). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database for MySQL-server
Skapa en Azure Database för MySQL-server med kommandot **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)**. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
namn | mydemoserver | Välj ett unikt namn för Azure Database för MySQL-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name | GP_Gen4_2 | Namnet på SKU:n. Följer konventionen {prisnivå}_{beräkningsgenerering}_{vCores} i snabbformat. Se under tabellen för mer information om parametern sku-name.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervallet är 7–35. 
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte. Tillåtna värden: Aktiverad, inaktiverad.
location | westus | Azure-platsen för servern.
ssl-enforcement | Enabled | Om ssl ska aktiveras eller inte för den här servern. Tillåtna värden: Aktiverad, inaktiverad.
storage-size | 51200 | Serverns lagringskapacitet (enheten är megabyte). En giltig storage-size är minst 5 120 MB och ökar i steg om 1 024 MB. Se dokumentet med [prisnivåer](./concepts-pricing-tiers.md) för mer information om storleksgränser för lagring. 
version | 5.7 | Huvudversion för MySQL.
admin-user | myadmin | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
admin-password | *säkert lösenord* | Lösenordet för administratörsanvändaren. Det måste innehålla mellan 8 och 128 tecken. Ditt lösenord måste innehålla tecken från tre av följande kategorier: Engelska versaler, engelska gemener, siffror och icke-alfanumeriska tecken.


Parametervärdet för sku-namn följer namngivningskonventionen {prisnivå}\_{compute-generering}\_{vCores} som i exemplen nedan:
+ `--sku-name B_Gen4_4` mappar till Basic, Gen 4 och 4 vCores.
+ `--sku-name GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
+ `--sku-name MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 vCores.

Se dokumentationen om [prisnivåer](./concepts-pricing-tiers.md) för mer information om giltiga värden per region och nivå.

I följande exempel skapas en MySQL 5.7-server i USA, västra som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradministratörsinloggningen `myadmin`. Det här är **4:e generationens** server för **generell användning** med 2 **virtuella kärnor**. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```




## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en Azure Database för MySQL-brandväggsregel på servernivå med kommandot **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)**. En brandväggsregel på servernivå gör att externa program, som kommandoradsverktyget **mysql.exe** eller MySQL Workbench kan ansluta till servern via Azure MySQL-tjänstens brandvägg. 

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt en IP-adress eller omfång av IP-adresser som motsvarar platsen som du kommer att ansluta från. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 3306.
> 


## <a name="configure-ssl-settings"></a>Konfigurera SSL-inställningar
Som standard verkställs SSL-anslutningar mellan servern och dina klientprogram. Denna standardinställning garanterar säkerheten för data ”i rörelse” genom att dataströmmen över internet krypteras. Avaktivera SSL-anslutningar för din server för att förenkla den här snabbstarten. Avaktivering av SSL rekommenderas inte för produktionsservrar. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database för MySQL](./howto-configure-ssl.md) för mer information.

I följande exempel inaktiveras SSL på MySQL-servern.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Anslut till servern med kommandoradsverktyget mysql.exe
Anslut till servern med kommandoradverktyget **mysql.exe**. Du kan hämta MySQL [här](https://dev.mysql.com/downloads/) och installera programmet på din dator. 

Skriv nästa kommandon: 

1. Anslut till servern med kommandoradsverktyget **mysql**:
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. Visa status för servern:
```sql
 mysql> status
```
Om allt går väl kommer kommandoradverktyget returnera följande text:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> För fler kommandon, se [Referensmanual för MySQL 5.7 - kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Anslut till servern med verktyget MySQL Workbench GUI
1.  Starta programmet MySQL Workbench på klientdatorn. Du kan ladda ned och installera MySQL Workbench [här](https://dev.mysql.com/downloads/workbench/).

2.  I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

   ![konfigurera ny anslutning](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Inställning** | **Föreslaget värde** | **Beskrivning** |
|---|---|---|
|   Anslutningsnamn | Min anslutning | Ange ett namn på anslutningen (det kan vara vad som helst) |
| Anslutningsmetod | välj Standard (TCP/IP) | Använda TCP/IP-protokollet för att ansluta till Azure Database för MySQL > |
| Värdnamn | mydemoserver.mysql.database.azure.com | Servernamn som du antecknade tidigare. |
| Port | 3306 | Standardporten för MySQL används. |
| Användarnamn | myadmin@mydemoserver | Inloggning för serveradministratör som du antecknade tidigare. |
| Lösenord | **** | Ange lösenordet som du konfigurerade tidigare. |

Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade.
Nu kan du klicka på anslutningen för att ansluta till servern.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
