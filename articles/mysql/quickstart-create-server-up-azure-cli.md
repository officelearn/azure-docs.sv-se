---
title: Snabbstart – skapa en Azure Database for MySQL med ett enkelt Azure CLI-kommando - az mysql in (förhandsversion)
description: Snabbstartsguide för att skapa Azure Database for MySQL-server med Azure CLI (kommandoradsgränssnitt) in-kommando.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61231088"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snabbstart: Skapa en Azure Database for MySQL med ett enkelt Azure CLI-kommando - az mysql in (förhandsversion)

> [!IMPORTANT]
> Den [az mysql in](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI-kommando är en förhandsversion.

Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder den [az mysql in](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) kommando för att skapa en Azure Database for MySQL-server med Azure CLI. Förutom att skapa servern, den `az mysql up` kommandot skapar en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar brandväggsregler för klientdatorn. Detta hjälper till att påskynda utvecklingen.

## <a name="prerequisites"></a>Nödvändiga komponenter

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

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database for MySQL-server

Om du vill använda kommandona installera den [db upp](/cli/azure/ext/db-up) tillägget. Om ett fel returneras ska du kontrollera att du har installerat den senaste versionen av Azure CLI. Se [installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for MySQL-server med följande kommando:

```azurecli
az mysql up
```

Servern skapas med följande standardvärden (om du manuellt åsidosätta dem):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | Systemgenererad | Ett unikt namn för Azure Database för MySQL-server.
resource-group | Systemgenererad | En ny Azure resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en General Purpose Gen5 server med 2 virtuella kärnor. Se våra [prissättningssidan](https://azure.microsoft.com/pricing/details/mysql/) för mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar.
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | westus2 | Azure-platsen för servern.
ssl-enforcement | Disabled | Om ssl ska aktiveras eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 5.7 | Huvudversion för MySQL.
admin-user | Systemgenererad | Användarnamnet för administratörsinloggning.
admin-password | Systemgenererad | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om den `az mysql up` kommandot och dess ytterligare parametrar finns i den [Azure CLI-dokumentationen](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

När servern har skapats, medföljer följande inställningar:

- En brandväggsregel som kallas ”devbox” har skapats. Azure CLI försöker identifiera IP-adressen för datorn den `az mysql up` kommandot är köras från- och vitlistor IP-adress.
- ”Tillåt åtkomst till Azure-tjänster” är inställt på på. Den här inställningen konfigurerar serverns Brandvägg för att godkänna anslutningar från alla Azure-resurser, inklusive resurser inte i din prenumeration.
- Den `wait_timeout` parametern anges till 8 timmar
- En tom databas med namnet ”sampledb” har skapats
- En ny användare med namnet ”rot” med behörighet till ”sampledb” har skapats

> [!NOTE]
> Azure Database för MySQL kommunicerar via port 3306. När du ansluter från inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåtas av nätverkets brandvägg. Har din IT-avdelning öppnar port 3306 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

Efter den `az mysql up` kommandot har slutförts returneras en lista med anslutningssträngar för vanliga programmeringsspråk för dig. Dessa anslutningssträngar är förkonfigurerad med specifika attribut för din nyligen skapade Azure Database for MySQL-server.

Du kan använda den [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) kommando för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabbstarten med följande kommando. Det här kommandot tar bort Azure Database for MySQL-server och resursgrupp.

```azurecli
az mysql down --delete-group
```

Om du endast vill ta bort den nyligen skapade servern, kan du köra [az mysql ned](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) kommando.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
