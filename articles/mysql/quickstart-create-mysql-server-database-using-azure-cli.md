---
title: "Snabbstart: Skapa en Azure Database för MySQL-server – Azure CLI | Microsoft Docs"
description: "I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 574299dd64120d75a1a36cb2ded0fdd269292570
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Skapa en Azure Database för MySQL-server med Azure CLI
I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp på ungefär fem minuter. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript.

Kontrollera att du har installerat den senaste versionen av [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) innan du går igenom den här snabbstarten. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```
Följ kommandoradsanvisningarna och öppna https://aka.ms/devicelog i webbläsaren och ange sedan den kod som genereras i **kommandotolken**.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) med kommandot [az group create](https://docs.microsoft.com/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `mycliresource` på platsen `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Skapa en Azure Database för MySQL-server med kommandot **az sql server create**. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en Azure Database för MySQL-server i `westus` i resursgruppen `mycliresource` med namnet `mycliserver`. Servern har en administratörsinloggning med namnet `myadmin` och lösenordet `Password01!`. Servern skapas med prestandanivån **Basic** och **50** beräkningsenheter som delas mellan alla databaser på servern. Du kan skala beräkning och lagring uppåt eller nedåt beroende på behoven i dina appar.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Skapa en Azure Database för MySQL-server med Azure CLI](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en Azure Database för MySQL-brandväggsregel på servernivå med kommandot **az mysql server firewall-rule create**. En brandväggsregel på servernivå gör att externa program, som kommandoradsverktyget **mysql.exe** eller MySQL Workbench kan ansluta till servern via Azure MySQL-tjänstens brandvägg. 

I följande exempel skapas en brandväggsregel för ett fördefinierat adressintervall, som i det här exemplet är hela det möjliga intervallet med IP-adresser.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Konfigurera SSL-inställningar
Som standard verkställs SSL-anslutningar mellan servern och dina klientprogram.  Detta garanterar säkerheten för data ”i rörelse” genom att dataströmmen över internet krypteras.  Vi gör den här snabbstarten lite enklare genom att inaktivera SSL-anslutningar för servern.  Det här rekommenderas inte för produktionsservrar.  Mer information finns i [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL).

I följande exempel inaktiveras SSL på MySQL-servern.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Anslut till servern med kommandoradsverktyget mysql.exe
När du ska ansluta till servern med kommandoradsverktyget **mysql.exe** ska du se till att MySQL är installerat i datorn.  Du kan hämta MySQL [här](https://dev.mysql.com/downloads/).

Öppna kommandotolken och ange följande: 

1. Anslut till servern med kommandoradsverktyget **mysql**:
```dos
 mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

2. Visa status för servern:
```dos
 mysql> status
```
Om allt går bra bör du se följande resultat i kommandoradsverktyget:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
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
Connection:             mycliserver.database.windows.net via TCP/IP
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
> Fler kommandon finns i [referenshandboken för MySQL 5.6 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Anslut till servern med verktyget MySQL Workbench GUI
1.    Starta programmet MySQL Workbench på klientdatorn. Du kan ladda ned och installera MySQL Workbench [här](https://dev.mysql.com/downloads/workbench/).

2.    I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

| **Parametrar** | **Beskrivning** |
|----------------|-----------------|
|    *Anslutningsnamn* | ange ett namn på anslutningen (det kan vara vad som helst) |
| *Anslutningsmetod* | välj Standard (TCP/IP) |
| *Värdnamn* | mycliserver.database.windows.net (SERVERNAMNET du antecknade tidigare) |
| *Port* | 3306 |
| *Användarnamn* | myadmin@mycliserver (INLOGGNING FÖR SERVERADMINISTRATÖR som du antecknade tidigare) |
| *Lösenord* | du kan lagra lösenordet för administratörskontot i valvet |

![konfigurera ny anslutning](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

3.    Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade.

4.    Nu kan du klicka på anslutningen du just skapade för att ansluta till servern.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att göra följande: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md).

