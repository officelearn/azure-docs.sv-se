---
title: "Utforma din första Azure-databas för PostgreSQL med Azure CLI | Microsoft Docs"
description: "Den här kursen visar hur du utformar din första Azure-databas för PostgreSQL med Azure CLI."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/27/2017
ms.openlocfilehash: 97299ae904115d08c5d03be38be263203552b84b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Utforma din första Azure-databas för PostgreSQL med Azure CLI 
I kursen får du använder Azure CLI (command-line-interface) och andra verktyg att lära dig hur du:
> [!div class="checklist"]
> * Skapa en Azure Database för PostgreSQL-server
> * Konfigurera server-brandväggen
> * Använd [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) verktyg för att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Du kan använda Azure Cloud Shell i webbläsaren eller [installera Azure CLI 2.0]( /cli/azure/install-azure-cli) på din dator för att köra kommandon i den här självstudiekursen.

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

I följande exempel skapas en server med namnet `mypgserver-20170401` i resursgruppen `myresourcegroup` med inloggning för serveradministratör `mylogin`. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. I följande exempel används [az postgres-brandväggsregel skapa](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) att skapa en brandväggsregel `AllowAllIps` som tillåter anslutning från någon IP-adress. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

Om du vill begränsa åtkomsten till Azure PostgreSQL-server för att endast i nätverket, kan du ange att brandväggsregeln omfattar endast ditt företagsnätverk IP-adressintervall.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure SQL-databasserver.
>

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Resultatet är i JSON-format. Anteckna **administratorLogin** och **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Ansluta till Azure-databas för PostgreSQL-databas med hjälp av psql
Om klientdatorn har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), eller Azure Cloud-konsolen att ansluta till en Azure PostgreSQL-servern. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure Database för PostgreSQL-servern.

1. Kör följande kommando för psql att ansluta till en Azure-databas för PostgreSQL-databas:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Följande kommando till exempel, ansluter till standarddatabasen som heter **postgres** på din PostgreSQL-server **mypgserver-20170401.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  När du är ansluten till servern, kan du skapa en tom databas i Kommandotolken:
```sql
CREATE DATABASE mypgsqldb;
```

3.  I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure-databasen för PostgreSQL gå vi igenom hur du utför några grundläggande uppgifter.

Vi kan först skapa en tabell och läsa in den med vissa data. Nu ska vi skapa en tabell som spårar inventeringsinformation:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Du kan se den nyligen skapade tabellen i listan över tabeller nu genom att skriva:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Läs in data i tabellen
Nu när vi har en tabell kan vi infoga vissa data i den. Kör följande fråga för att infoga vissa rader med data i öppna en kommandotolk-fönster:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Du har nu lagt till exempeldata två rader i tabellen som du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Fråga efter och uppdatera data i tabeller
Kör följande fråga för att hämta information från tabellen lager: 
```sql
SELECT * FROM inventory;
```

Du kan också uppdatera data i tabellen lager:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Du kan se de uppdaterade värdena när du hämtar data:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du av misstag har tagit bort en tabell. Detta är något du lätt kan återställa från. Azure-databas för PostgreSQL kan du gå tillbaka till någon i tidpunkt (upp till 7 dagar i Basic) och 35 dagar i Standard och återställa den här i tidpunkt till en ny server. Du kan använda den här nya servern för att återställa dina data. 

Följande kommando återställer exempelserver till en innan tabellen har lagts till:
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

Den `az postgres server restore` kommandot måste följande parametrar:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| --resursgruppen. |  myResourceGroup |  Resursgruppens namn som källservern finns.  |
| --namn | mypgserver återställs | Namnet på den nya servern som skapas med kommandot restore. |
| Återställ punkt i tiden | 2017-04-13T13:59:00Z | Välj en punkt i tid att återställa till. Datumet och tiden måste vara inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd ISO8601-formatet för datum och tid. Exempelvis kan du använda din egen lokala tidszon som `2017-04-13T05:59:00-08:00`, eller använda UTC Zulu format `2017-04-13T13:59:00Z`. |
| --käll-servern | mypgserver 20170401 | Namnet eller ID på källservern för att återställa från. |

Återställa en server till en i tidpunkt skapar en ny server, kopieras som den ursprungliga servern från och med punkten tidpunkt du anger. Plats och prisnivå nivåvärden för den återställda servern är samma som källservern.

Kommandot är synkron och kommer tillbaka när servern har återställts. Leta upp den nya servern som skapades när återställningen är klar. Kontrollera att data har återställts som förväntat.


## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur du använder Azure CLI (command-line-interface) och andra verktyg för att:
> [!div class="checklist"]
> * Skapa en Azure Database för PostgreSQL-server
> * Konfigurera server-brandväggen
> * Använd [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) verktyg för att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Därefter beskrivs hur du använder Azure portal gör liknande uppgifter genom att granska den här självstudiekursen: [utforma din första Azure-databas för PostgreSQL med Azure-portalen](tutorial-design-database-using-azure-portal.md)
