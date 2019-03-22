---
title: Snabbstart – skapa en Azure Database for PostgreSQL med ett enkelt Azure CLI-kommando - az postgres upp (förhandsversion)
description: Snabbstartsguide för att skapa Azure Database for PostgreSQL-server med Azure CLI (kommandoradsgränssnitt) in-kommando.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.openlocfilehash: 0db49e2c370aee37cca4181cecbe4cf0b5585c51
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136452"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-a-simple-azure-cli-command---az-postgres-up-preview"></a>Snabbstart: Skapa en Azure Database for PostgreSQL med ett enkelt Azure CLI-kommando - az postgres upp (förhandsversion)

> [!IMPORTANT]
> Den [az postgres upp](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-kommando är en förhandsversion.

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder den [az postgres upp](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommando för att skapa en Azure Database for PostgreSQL-server med Azure CLI. Förutom att skapa servern, den `az postgres up` kommandot skapar en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar brandväggsregler för klientdatorn. Detta hjälper till att påskynda utvecklingen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med hjälp av den [az-inloggning](/cli/azure/authenticate-azure-cli?view=interactive-log-in) kommando. Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt den **prenumerations-id** egenskap från den **az-inloggning** utdata för din prenumeration i platshållaren för prenumerations-ID: t.

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
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar.
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | westus2 | Azure-platsen för servern.
ssl-enforcement | Disabled | Om ssl ska aktiveras eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 10 | Huvudversion för PostgreSQL.
admin-user | Systemgenererad | Användarnamnet för administratörsinloggning.
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
