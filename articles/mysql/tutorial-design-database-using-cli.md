---
title: 'Självstudie: Utforma Azure Database for MySQL med Azure CLI'
description: I den här självstudien beskrivs hur du skapar och hanterar en Azure Database for MySQL-server och en databas med Azure CLI 2.0 från kommandoraden.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 6f77b24092a80708ba82c6d42b947c00d6a69b6d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>Självstudie: Utforma Azure Database for MySQL med Azure CLI

Azure Database for MySQL är en relationsdatabastjänst i Microsoft-molnet som är baserad på databasmotorn MySQL Community Edition. I den här självstudien kommer du att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg till följande:

> [!div class="checklist"]
> * Skapa en Azure Database för MySQL-server
> * Konfigurera serverbrandväggen
> * Använda [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att skapa en databas
> * Läsa in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Du kan använda Azure Cloud Shell i webbläsaren eller [installera Azure CLI 2.0]( /cli/azure/install-azure-cli) lokalt när du ska köra kodblocken i den här självstudiekursen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med kommandot [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp.

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
Skapa en Azure Database for MySQL-server med kommandot az mysql server create. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en Azure Database för MySQL-server i `westus` i resursgruppen `myresourcegroup` med namnet `mydemoserver`. Servern har en administratörsinloggning med namnet `myadmin`. Det här är 4:e generationens server för generell användning med 2 virtuella kärnor. Ersätt `<server_admin_password>` med ditt eget värde.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.


## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en Azure Database för MySQL-brandväggsregel på servernivå med kommandot az mysql server firewall-rule create. En brandväggsregel på servernivå gör att externa program, som kommandoradsverktyget **mysql** eller MySQL Workbench, kan ansluta till servern via Azure MySQL-tjänstens brandvägg. 

I följande exempel skapas en brandväggsregel för ett fördefinierat adressintervall. Exemplet visar hela intervallet med möjliga IP-adresser.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
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
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att upprätta en anslutning till din Azure Database for MySQL-server. I det här exemplet är kommandot:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern ska du skapa en tom databas.
```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for MySQL-databasen kan du utföra några grundläggande uppgifter.

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data till tabellerna
Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande frågor för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du har tagit bort den här tabellen av misstag. Det här är något som inte är helt enkelt att ångra. Med Azure Database for MySQL kan du gå tillbaka till valfri tidpunkt under de senaste 35 dagarna och återställa tidpunkten på en ny server. Du kan använda den nya servern till att återställa dina data. Följande steg återställer exempelservern till en tidpunkt innan tabellen lades till.

Du behöver följande information vid återställningen:

- Återställningspunkt: Välj en tidpunkt innan servern ändrades. Måste vara senare än eller lika med källdatabasens äldsta säkerhetskopiering.
- Målserver: Ange ett nytt servernamn som du vill återställa till.
- Källserver: Ange namnet på den server du vill återställa från.
- Plats: Du kan inte välja region, som standard är det samma som källservern.

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Följande parametrar behövs för kommandot `az mysql server restore`:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| namn | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan använda din egen lokala tidszon som t.ex. `2017-04-13T05:59:00-08:00`, eller använda UTC Zulu-formatet `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidpunkt så skapas en ny server. Den kopieras som den ursprungliga servern vid den tidpunkt du anger. Plats- och prisnivåvärden för den återställda servern är samma som för källservern.

Kommandot är synkront och återgår när servern har återställts. När återställningen är klar letar du upp den nya server som skapades. Kontrollera att dina data har återställts som förväntat.

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du lärda dig att:
> [!div class="checklist"]
> * Skapa en Azure Database för MySQL-server
> * Konfigurera serverbrandväggen
> * Använda [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att skapa en databas
> * Läsa in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Azure Database for MySQL – Azure CLI-exempel](./sample-scripts-azure-cli.md)
