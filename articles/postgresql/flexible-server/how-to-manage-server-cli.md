---
title: Hantera Server – Azure CLI – Azure Database for PostgreSQL-flexibel Server
description: Lär dig hur du hanterar en Azure Database for PostgreSQL-flexibel Server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8e24dd6cb8a1fa90f1a6caf9117ab3c344c00b12
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913882"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Hantera en Azure Database for PostgreSQL-flexibel server med Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln visar hur du hanterar din flexibla server som distribueras i Azure. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Om du inte redan har skapat en flexibel Server ännu kan du skapa en för att komma igång med den här guiden.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan skala upp beräknings nivån, virtuella kärnor och lagringen på ett enkelt sätt med hjälp av följande kommando. Du kan se alla Server åtgärder som du kan köra [AZ postgres-Server (flexibel Server) Översikt](https://docs.microsoft.com/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Här följer information om argument ovan:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för servern. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|Standard_D4ds_v3|Ange namnet på beräknings nivån och storleken. Följ regel Standard_ {VM-storlek} i korthet. Mer information finns på [pris nivåerna](../concepts-pricing-tiers.md) .
storage-size | 6144 | Serverns lagringskapacitet (enheten är megabyte). Lägsta 5120 och ökar i steg om 1024.

> [!IMPORTANT]
> Det går inte att skala upp lagrings utrymmet. 

## <a name="manage-postgresql-databases-on-a-server"></a>Hantera PostgreSQL-databaser på en server

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Följande kommando ansluter exempelvis till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   När du har anslutit visar psql-verktyget en postgres-kommandotolk där du skriver sql-kommandon. Vid den första anslutningen kanske en varning visas eftersom det psql-verktyg du använder kan vara en annan version än Azure Database for PostgreSQL-serverversionen.

   Exempel på psql-utdata:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Om brandväggen inte är konfigurerad att tillåta IP-adressen för din klient uppstår följande fel:
   >
   > "psql: allvarligt: ingen pg_hba. conf-post för värden `<IP address>` , användare" mina administratörer ", databas" postgres ", SSL på oåterkallelig: SSL-anslutning krävs. Specify SSL options and retry.
   >
   > Kontrol lera att klientens IP-adress tillåts i steg ovan för brand Väggs regler.

2. Skapa en tom databas med namnet "postgresdb" i kommando tolken genom att skriva följande kommando:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. I kommando tolken kör du följande kommando för att växla anslutning till den nyligen skapade databasen **postgresdb** :

    ```bash
    \c postgresdb
    ```

4. Ange `\q` och tryck på retur för att avsluta psql.

Du anslöt till Azure Database for PostgreSQL-servern via psql, och skapade en tom användardatabas.

## <a name="reset-admin-password"></a>Återställ administratörs lösen ord
Du kan ändra administratörs rollens lösen ord med det här kommandot
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Kontrol lera att lösen ordet är minst 8 tecken och högst 128 tecken.
> Lösen ordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

## <a name="delete-a-server"></a>Ta bort en server

Om du bara vill ta bort den PostgreSQL flexibla servern kan du köra kommandot [AZ postgres böjlig-Server Delete](https://docs.microsoft.com/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

- [Förstå säkerhets kopierings-och återställnings begrepp](concepts-backup-restore.md)
- [Justera och övervaka servern](concepts-monitoring.md)