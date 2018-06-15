---
title: 'Självstudie: Utforma Azure Database for PostgreSQL med Azure CLI'
description: I den här självstudien visar vi hur du skapar, konfigurerar och frågar din första Azure Database for PostgreSQL-server med Azure CLI.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.openlocfilehash: acba480631ba69a81da3029aadfb9cb51797549a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31417773"
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-azure-cli"></a>Självstudie: Utforma Azure Database for PostgreSQL med Azure CLI 
I den här självstudien kommer du att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg till följande:
> [!div class="checklist"]
> * Skapa en Azure Database för PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget för att skapa en databas
> * Läsa in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Du kan använda Azure Cloud Shell i webbläsaren eller [installera Azure CLI 2.0]( /cli/azure/install-azure-cli) lokalt när du ska köra kommandon i den här självstudiekursen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server
Skapa en [Azure Database för PostgreSQL-server](overview.md) med kommandot [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). En server innehåller en grupp med databaser som hanteras som en grupp. 

I följande exempel skapas en server som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradmin-inloggningen `myadmin`. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde. Det här är 4:e generationens server för generell användning med 2 virtuella kärnor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. I följande exempel används [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) för att skapa en brandväggsregel `AllowAllIps` som tillåter anslutning från valfri IP-adress. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

Om du vill begränsa åtkomsten till Azure PostgreSQL-servern så att endast ditt nätverk får åtkomst kan du ställa in brandväggsregeln så att den endast omfattar ditt företagsnätverks IP-adressintervall.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure SQL-databasserver.
>

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **administratorLogin** och **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Ansluta till Azure Database for PostgreSQL-databas med psql
Om din klientdator har PostgreSQL installerat kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) eller Azure Cloud Console för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure Database för PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database for PostgreSQL-databas:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Följande kommando ansluter till exempel till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  När du är ansluten till servern skapar du en tom databas:
```sql
CREATE DATABASE mypgsqldb;
```

3.  I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for PostgreSQL-databasen, kan du utföra några grundläggande uppgifter:

Skapa först en tabell och läs in lite data till den. Skapa till exempel en tabell som spårar lagerinformation:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Du kan se den nyligen skapade tabellen i listan med tabeller genom att skriva:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Läsa in data i tabellen
Nu när du har skapat en tabell infogar du lite data i den. Kör följande fråga i den öppna kommandotolken för att infoga några datarader:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du lagt till två rader exempeldata i tabellen som du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande fråga för att hämta information från inventeringstabellen: 
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i inventeringstabellen:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Du ser de uppdaterade värdena när du hämtar data:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du av misstag har tagit bort en tabell. Det här är något som inte är helt enkelt att ångra. Med Azure Database for PostgreSQL kan du gå tillbaka till valfri tidpunkt från vilken du har serversäkerhetskopior (bestäms utifrån kvarhållningsperioden för säkerhetskopior som du konfigurerar) och återställa tidpunkten på en ny server. Du kan använda den nya servern till att återställa dina data. 

Följande kommandon återställer exempelservern till en tidpunkt innan tabellen lades till:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Följande parametrar behövs för kommandot `az postgres server restore`:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| namn | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan använda din egen lokala tidszon som t.ex. `2017-04-13T05:59:00-08:00`, eller använda UTC Zulu-formatet `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidpunkt så skapas en ny server. Den kopieras som den ursprungliga servern vid den tidpunkt du anger. Plats- och prisnivåvärden för den återställda servern är samma som för källservern.

Kommandot är synkront och återgår när servern har återställts. När återställningen är klar letar du upp den nya server som skapades. Kontrollera att dina data har återställts som förväntat.


## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg för att:
> [!div class="checklist"]
> * Skapa en Azure Database för PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget för att skapa en databas
> * Läsa in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Nu ska du lära dig hur du använder Azure Portal för att utföra liknande aktiviteter. Gå till följande självstudie: [Utforma din första Azure Database for PostgreSQL med Azure Portal](tutorial-design-database-using-azure-portal.md)
