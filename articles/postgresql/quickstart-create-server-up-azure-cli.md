---
title: 'Snabbstart: Skapa server - az postgres up - Azure Database for PostgreSQL - Single Server'
description: Snabbstartsguide för att skapa Azure Database för PostgreSQL - Single Server med azure CLI (kommandoradsgränssnitt) upp kommando.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774840"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snabbstart: Använd ett Azure CLI-kommando, az postgres up (preview), för att skapa en Azure-databas för PostgreSQL - Single Server

> [!IMPORTANT]
> Kommandot [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI visas i förhandsversion.

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder kommandot [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) för att skapa en Azure-databas för PostgreSQL-server med Azure CLI. Förutom att skapa servern `az postgres up` skapar kommandot en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar standardbrandväggsregler för klientdatorn. Dessa standardvärden bidrar till att påskynda utvecklingsprocessen.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Observera **ID-egenskapen** från kommandoutdata för motsvarande prenumerationsnamn.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt **prenumerations-ID-egenskapen** från **az-inloggningsutgången** för din prenumeration med platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Om du vill använda kommandona installerar du [tillägget db-up.](/cli/azure/ext/db-up) Om ett fel returneras kontrollerar du att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure-databas för PostgreSQL-server med följande kommando:

```azurecli
az postgres up
```

Servern skapas med följande standardvärden (såvida du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | System som genereras | Ett unikt namn som identifierar Azure Database för PostgreSQL-servern.
resource-group | System som genereras | En ny Azure-resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardär en General Purpose Gen5-server med 2 virtuella kärnor. Se vår [prissida](https://azure.microsoft.com/pricing/details/postgresql/) för mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhetskopia behålls. Enheten är dagar.
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | westus2 | Azure-platsen för servern.
ssl-enforcement | Disabled | Om ssl ska aktiveras eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 10 | Huvudversion för PostgreSQL.
admin-user | System som genereras | Användarnamnet för administratören.
admin-password | System som genereras | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om `az postgres up` kommandot och dess ytterligare parametrar finns i [Azure CLI-dokumentationen](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

När servern har skapats levereras följande inställningar:

- En brandväggsregel som kallas "devbox" skapas. Azure CLI försöker identifiera IP-adressen för `az postgres up` den dator som kommandot körs från och vitlistar IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på ON. Den här inställningen konfigurerar serverns brandvägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte finns i din prenumeration.
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "root" med behörighet att "sampledb" skapas

> [!NOTE]
> Azure Database för PostgreSQL kommunicerar över port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Låt IT-avdelningen öppna port 5432 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När `az postgres up` kommandot är klart returneras en lista över anslutningssträngar för populära programmeringsspråk till dig. Dessa anslutningssträngar är förkonfigurerade med specifika attribut för din nyligen skapade Azure-databas för PostgreSQL-server.

Du kan använda kommandot [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du har skapat i snabbstarten med följande kommando. Det här kommandot tar bort Azure Database för PostgreSQL-server och resursgruppen.

```azurecli
az postgres down --delete-group
```

Om du bara vill ta bort den nyskapade servern kan du köra [kommandot az postgres down.](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
