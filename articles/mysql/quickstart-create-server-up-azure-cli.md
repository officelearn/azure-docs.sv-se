---
title: 'Snabbstart: Skapa Azure-databas för MySQL med az mysql up'
description: Snabbstartsguide för att skapa Azure Database för MySQL-server med kommandot Azure CLI (kommandoradsgränssnitt).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067710"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snabbstart: Skapa en Azure-databas för MySQL med ett enkelt Azure CLI-kommando – az mysql up (preview)

> [!IMPORTANT]
> Kommandot [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI visas i förhandsversion.

Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller skript. Den här snabbstarten visar hur du använder kommandot [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) för att skapa en Azure Database för MySQL-server med Azure CLI. Förutom att skapa servern `az mysql up` skapar kommandot en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar standardbrandväggsregler för klientdatorn. Detta bidrar till att påskynda utvecklingsprocessen.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt **prenumerations-ID-egenskapen** från **az-inloggningsutgången** för din prenumeration med platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server

Om du vill använda kommandona installerar du [tillägget db-up.](/cli/azure/ext/db-up) Om ett fel returneras kontrollerar du att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure-databas för MySQL-server med följande kommando:

```azurecli
az mysql up
```

Servern skapas med följande standardvärden (såvida du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | System som genereras | Ett unikt namn för Azure Database för MySQL-server.
resource-group | System som genereras | En ny Azure-resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardär en General Purpose Gen5-server med 2 virtuella kärnor. Se vår [prissida](https://azure.microsoft.com/pricing/details/mysql/) för mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar.
geo-redundant-backup | Disabled | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | westus2 | Azure-platsen för servern.
ssl-enforcement | Disabled | Om SSL ska aktiveras eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 5.7 | Huvudversion för MySQL.
admin-user | System som genereras | Användarnamnet för administratörsinloggning.
admin-password | System som genereras | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om `az mysql up` kommandot och dess ytterligare parametrar finns i [Azure CLI-dokumentationen](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

När servern har skapats levereras följande inställningar:

- En brandväggsregel som kallas "devbox" skapas. Azure CLI försöker identifiera IP-adressen för `az mysql up` den dator som kommandot körs från och vitlistar IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på ON. Den här inställningen konfigurerar serverns brandvägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte finns i din prenumeration.
- Parametern `wait_timeout` är inställd på 8 timmar
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "root" med behörighet att "sampledb" skapas

> [!NOTE]
> Azure Database for MySQL kommunicerar via port 3306. När du ansluter från ett företagsnätverk kan det hända att utgående trafik via port 3306 inte tillåts av nätverkets brandvägg. Låt IT-avdelningen öppna port 3306 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När `az mysql up` kommandot är klart returneras en lista över anslutningssträngar för populära programmeringsspråk till dig. Dessa anslutningssträngar är förkonfigurerade med specifika attribut för din nyligen skapade Azure-databas för MySQL-server.

Du kan använda kommandot [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du har skapat i snabbstarten med följande kommando. Det här kommandot tar bort Azure Database för MySQL-servern och resursgruppen.

```azurecli
az mysql down --delete-group
```

Om du bara vill ta bort den nyskapade servern, kan du köra [az mysql ner](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) kommandot.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
