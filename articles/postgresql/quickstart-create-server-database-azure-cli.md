---
title: "Skapa en Azure Database för PostgreSQL med hjälp av Azure-CLI:n | Microsoft Docs"
description: "Snabbstartsguide för att skapa och hantera Azure Database för PostgreSQL-server med Azure-CLI (kommandoradsgränssnitt)."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 06/13/2017
ms.openlocfilehash: d78243abc140c7b3f0b99bdf56821b7920568550
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Skapa en Azure Database för PostgreSQL med hjälp av Azure-CLI:n
Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server i en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med hjälp av Azure-CLI:n.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer, välj lämplig prenumeration som resursen ska debiteras till. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en [Azure Database för PostgreSQL-server](overview.md) med kommandot [az postgres server create](/cli/azure/postgres/server#create). En server innehåller en grupp med databaser som hanteras som en grupp. 

Följande exempel skapar en server som heter `mypgserver-20170401` i din resursgrupp `myresourcegroup` med serveradmin-inloggningen `mylogin`. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. Följande exempel använder [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) för att skapa en brandväggsregel `AllowAllIps` för ett IP-adressintervall. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure SQL-databasserver.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Följande kommando till exempel, ansluter till standarddatabasen som heter **postgres** på din PostgreSQL-server **mypgserver-20170401.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  När du är ansluten till servern, skapar du en blank databas i prompten.
```sql
CREATE DATABASE mypgsqldb;
```

3.  I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Anslut till PostgreSQL-databasen med pgAdmin

Om du vill ansluta till Azure PostgreSQL-servern med GUI-verktyget _pgAdmin_
1.  Starta _pgAdmin_-programmet på din klientdator. Du kan installera _pgAdmin_ från http://www.pgadmin.org/.
2.  Välj **Lägg till ny Server** från menyn **snabblänkar**.
3.  I dialogrutan **skapa – server** i fliken **allmänt**, anger du ett unikt eget namn för servern. Låt säga **Azure PostgreSQL Server**.
 ![pgAdmin-verktyget – skapa – server](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  I dialogrutan **skapa – server**, i fliken **anslutning**:
    - Ange det fullständigt kvalificerade servernamnet (till exempel **mypgserver-20170401.postgres.database.azure.com**) i rutan **värddatornamn / adress**. 
    - Ange port 5432 i **Port**-rutan. 
    - Ange den **serveradmin-inloggning (user@mypgserver)** som du fick tidigare i den här snabbstarten och lösenordet du angav när du skapade servern i **användarnamn**- respektive **lösenords**-rutorna.
    - Välj **SSL-läge** som **kräv**. Som standard skapas alla Azure PostgreSQL-servrar med SSL tvingande aktiverat. Om du vill inaktivera SSL tvingande, se informationen i [tvingande SSL](./concepts-ssl-connection-security.md).

    ![pgAdmin – skapa – server](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Klicka på **Spara**.
6.  I webbläsarens vänstra fönster, expanderar du **servergrupper**. Välj din server **Azure PostgreSQL-server**.
7.  Välj den **server** du anslöt till och välj sedan **databaser** under den. 
8.  Högerklicka på **databaser** för att skapa en databas.
9.  Välj ett databasnamn **mypgsqldb** och ägaren för den som inloggning för serveradministratören **mylogin**.
10. Klicka på **spara** för att skapa en tom databas.
11. I **webbläsaren**, expanderar du **servrar**-gruppen. Expandera servern som du skapade och se databasen **mypgsqldb** under den.
 ![pgAdmin – skapa – databas](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Rensa resurser

Rensa upp alla resurser du skapade i snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta, kan du använda följande steg för att ta bort alla resurser som skapades av den här snabbstarten i Azure-CLI:n.

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill radera den nyskapade servern kan du köra kommandot [az postgres server delete](/cli/azure/postgres/server#delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
