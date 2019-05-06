---
title: 'Snabbstart: Skapa en Azure Database för PostgreSQL - Server med CLI-kommandot az postgres upp'
description: Snabbstartsguide för att skapa Azure Database för PostgreSQL – enskild Server med Azure CLI (kommandoradsgränssnittet) in-kommando.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 49f71c199a2832d763bb3c19d878fade47dfb8e4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069079"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snabbstart: Använda ett Azure CLI-kommando, az postgres upp (förhandsversion) för att skapa en Azure Database för PostgreSQL – enskild Server

> [!IMPORTANT]
> Den [az postgres upp](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-kommando är en förhandsversion.

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder den [az postgres upp](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommando för att skapa en Azure Database for PostgreSQL-server med Azure CLI. Förutom att skapa servern, den `az postgres up` kommandot skapar en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar brandväggsregler för klientdatorn. Dessa standardinställningar hjälpa dig för att påskynda utvecklingen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med hjälp av den [az-inloggning](/cli/azure/authenticate-azure-cli?view=interactive-log-in) kommando. Obs den **ID** egenskap från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt den **prenumerations-ID** egenskap från den **az-inloggning** utdata för din prenumeration i platshållaren för prenumerations-ID: T.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Om du vill använda kommandona installera den [db upp](/cli/azure/ext/db-up) tillägget. Om ett fel returneras ska du kontrollera att du har installerat den senaste versionen av Azure CLI. Se [installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for PostgreSQL-server med följande kommando:

```azurecli
az postgres up
```

Servern skapas med följande standardvärden (om du manuellt åsidosätta dem):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | Systemgenererad | Ett unikt namn som identifierar Azure Database för PostgreSQL-servern.
resource-group | Systemgenererad | En ny Azure resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en General Purpose Gen5 server med 2 virtuella kärnor. Se våra [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/) för mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhetskopia bevaras. Enheten är dagar.
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | westus2 | Azure-platsen för servern.
ssl-enforcement | Disabled | Om ssl ska aktiveras eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 10 | Huvudversion för PostgreSQL.
admin-user | Systemgenererad | Användarnamn för administratören.
admin-password | Systemgenererad | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om den `az postgres up` kommandot och dess ytterligare parametrar finns i den [Azure CLI-dokumentationen](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

När servern har skapats, medföljer följande inställningar:

- En brandväggsregel som kallas ”devbox” har skapats. Azure CLI försöker identifiera IP-adressen för datorn den `az postgres up` kommandot är köras från- och vitlistor IP-adress.
- ”Tillåt åtkomst till Azure-tjänster” är inställt på på. Den här inställningen konfigurerar serverns Brandvägg för att godkänna anslutningar från alla Azure-resurser, inklusive resurser inte i din prenumeration.
- En tom databas med namnet ”sampledb” har skapats
- En ny användare med namnet ”rot” med behörighet till ”sampledb” har skapats

> [!NOTE]
> Azure Database för PostgreSQL kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Har din IT-avdelning öppnar port 5432 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

Efter den `az postgres up` kommandot har slutförts returneras en lista med anslutningssträngar för vanliga programmeringsspråk för dig. Dessa anslutningssträngar är förkonfigurerad med specifika attribut för din nyligen skapade Azure Database for PostgreSQL-server.

Du kan använda den [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) kommando för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabbstarten med följande kommando. Det här kommandot tar bort Azure Database for PostgreSQL-server och resursgrupp.

```azurecli
az postgres down --delete-group
```

Om du endast vill ta bort den nyligen skapade servern, kan du köra [az postgres ned](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) kommando.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
