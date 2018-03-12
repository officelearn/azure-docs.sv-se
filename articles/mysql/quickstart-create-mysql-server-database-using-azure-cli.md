---
title: "Snabbstart: Skapa en Azure Database for MySQL-server – Azure CLI"
description: "I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 2cd867f09550f922479955b885f10ff329715c1c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Skapa en Azure Database för MySQL-server med Azure CLI
I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp på ungefär fem minuter. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med kommandot [az group create](/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="add-the-extension"></a>Lägga till tillägget
Lägg till det uppdaterade hanteringstillägget för Azure Database for MySQL med följande kommando:
```azurecli-interactive
az extension add --name rdbms
``` 

Kontrollera att du har rätt tilläggsversion installerad. 
```azurecli-interactive
az extension list
```

JSON-returfilen bör innehålla följande: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.3"
}
```

Om version 0.0.3 inte returneras kör du följande för att uppdatera tillägget: 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Skapa en Azure Database för MySQL-server med kommandot **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)**. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en server i USA, västra som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradministratörsinloggningen `myadmin`. Det här är **4:e generationens** server för **generell användning** med 2 **virtuella kärnor**. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en Azure Database för MySQL-brandväggsregel på servernivå med kommandot **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)**. En brandväggsregel på servernivå gör att externa program, som kommandoradsverktyget **mysql.exe** eller MySQL Workbench kan ansluta till servern via Azure MySQL-tjänstens brandvägg. 

I följande exempel skapas en brandväggsregel för ett fördefinierat adressintervall, som i det här exemplet är hela det möjliga intervallet med IP-adresser.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Det är inte säkert att tillåta alla IP-adresser. Det här exemplet tillhandahålls för enkelhetens skull, men du behöver känna till de exakta IP-adressintervallen om du vill lägga till program och användare i ett verkligt scenario. 

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
Anslut till servern med kommandoradverktyget **mysql.exe**. Du kan hämta MySQL [här](https://dev.mysql.com/downloads/) och installera programmet på din dator. Du kan även klicka på knappen **Prova** på kodexemplen eller på `>_`-knappen i det övre högra verktygsfältet i Azure-portalen och starta **Azure Cloud Shell**.

Skriv nästa kommandon: 

1. Anslut till servern med kommandoradsverktyget **mysql**:
```azurecli-interactive
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

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
