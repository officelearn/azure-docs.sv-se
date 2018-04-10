---
title: Snabbstart – Skapa en Azure Database for PostgreSQL med hjälp av Azure CLI
description: Snabbstartsguide för att skapa och hantera Azure Database for PostgreSQL-server med Azure CLI (kommandoradsgränssnittet).
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 9b92e00f7082921586e8b8d8461ddf718ca30aeb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Snabbstart: Skapa en Azure Database for PostgreSQL med hjälp av Azure CLI
Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server i en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med hjälp av Azure-CLI:n.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av kommandot [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#az_group_create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Lägga till tillägget
Lägg till det uppdaterade hanteringstillägget för Azure Database for PostgreSQL med följande kommando:
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
    "version": "0.0.5"
}
```

Om version 0.0.5 inte returneras, kör du följande för att uppdatera tillägget: 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en [Azure Database för PostgreSQL-server](overview.md) med kommandot [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). En server innehåller en grupp med databaser som hanteras som en grupp. 

I följande exempel skapas en server i USA, västra som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradministratörsinloggningen `myadmin`. Det här är **4:e generationens** server för **generell användning** med 2 **virtuella kärnor**. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. Följande exempel använder [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) för att skapa en brandväggsregel `AllowAllIps` för ett IP-adressintervall. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure PostgreSQL-server.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Följande kommando ansluter till exempel till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  När du är ansluten till servern, skapar du en blank databas i prompten.
```sql
CREATE DATABASE mypgsqldb;
```

3.  I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ansluta till PostgreSQL-servern med hjälp av pgAdmin

pgAdmin är ett verktyg med öppen källkod som används med PostgreSQL. Du kan installera pgAdmin från [pgAdmin-webbplatsen](http://www.pgadmin.org/). Den pgAdmin-version som du använder kan skilja sig från vad som används i den här snabbstarten. Läs dokumentationen för pgAdmin om du behöver ytterligare hjälp.

1. Öppna pgAdmin-programmet på klientdatorn.

2. Från verktygsfältet går du till **Objekt**, hovrar över **Skapa** och väljer **Server**.

3. I dialogrutan **Skapa – Server** på fliken **Allmänt** anger du ett unikt eget namn för servern, t.ex. **mydemoserver**.

   ![Fliken ”Allmänt”](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. Fyll i inställningstabellen i dialogrutan **Skapa – Server** på fliken **Anslutning**.

   ![Fliken ”Anslutning”](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    pgAdmin-parameter |Värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Vår exempelserver är **mydemoserver.postgres.database.azure.com.** Använd det fullständiga domännamnet (**\*.postgres.database.azure.com**) som i det här exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    Port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern. 
    Underhållsdatabas | *postgres* | Systemgenererat standardnamn för databasen.
    Användarnamn | Inloggningsnamn för serveradministratör | Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *username@servername*.
    Lösenord | Ditt administratörslösenord | Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | Lämna tomt | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | *Kräv* | Du kan ställa in SSL-läge på SSL-fliken i pgAdmin. Som standard skapas alla Azure Database for PostgreSQL-servrar med tvingande SSL aktiverat. Om du vill inaktivera tvingande SSL kan läsa informationen om [tvingande SSL](./concepts-ssl-connection-security.md).
    
5. Välj **Spara**.

6. I fönstret **Webbläsare** till vänster expanderar du noden **Servrar**. Välj din server, till exempel **mydemoserver**. Klicka för att ansluta till den.

7. Expandera servernoden och expandera sedan **Databaser** under den. Listan bör innehålla din befintliga *postgres*-databas och eventuella andra databaser som du har skapat. Du kan skapa flera databaser per server med Azure Database for PostgreSQL.

8. Högerklicka på **Databaser**, välj menyn **Skapa** och välj sedan **Databas**.

9. Ange valfritt databasnamn i fältet **Databas**, till exempel **mypgsqldb2**.

10. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj ditt inloggningsnamn som serveradministratör, förslagsvis som i vårt exempel: **my admin**.

   ![Skapa en databas i pgAdmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Välj **Spara** för att skapa en tom databas.

12. I fönstret **Webbläsare** ser du den databas som du skapade i listan med databaser under servernamnet.




## <a name="clean-up-resources"></a>Rensa resurser

Rensa upp alla resurser du skapade i snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande snabbstarter ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta, kan du använda följande steg för att ta bort alla resurser som skapades av den här snabbstarten i Azure-CLI:n.

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)

