---
title: Säkerhetskopiering och återställning - Azure CLI - Azure Database för MariaDB
description: Lär dig hur du säkerhetskopierar och återställer en server i Azure Database för MariaDB med hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 6faae80c78fe07d33579cc3fb7c76ce668969992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369274"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Säkerhetskopierar och återställer en server i Azure Database för MariaDB med Hjälp av Azure CLI

Azure Database för MariaDB-servrar säkerhetskopieras med jämna mellanrum för att aktivera återställningsfunktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt, på en ny server.

## <a name="prerequisites"></a>Krav

För att slutföra den här guiden behöver du:

- En [Azure-databas för MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhetskopiering

Du kan välja mellan att konfigurera servern för antingen lokalt redundanta säkerhetskopior eller geografiskt redundanta säkerhetskopior när servern skapas.

> [!NOTE]
> När en server har skapats kan den typ av redundans som den har, geografiskt redundant kontra lokalt redundant, inte växlas.
>

När du skapar `az mariadb server create` en server `--geo-redundant-backup` via kommandot bestämmer parametern alternativet För säkerhetskopieringsredundans. Om `Enabled`tas geo redundanta säkerhetskopior. Eller `Disabled` om lokalt redundanta säkerhetskopior tas.

Lagringsperioden för säkerhetskopiering ställs `--backup-retention`in av parametern .

Mer information om hur du anger dessa värden under skapa finns i [Azure Database for MariaDB server CLI Quickstart](quickstart-create-mariadb-server-database-using-azure-cli.md).

Lagringsperioden för säkerhetskopiering för en server kan ändras på följande sätt:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

I föregående exempel ändras lagringsperioden för säkerhetskopiering för mindemoserver till 10 dagar.

Lagringsperioden för säkerhetskopiering styr hur långt tillbaka i tiden en point-in-time-återställning kan hämtas, eftersom den baseras på tillgängliga säkerhetskopior. Tidsåterställning beskrivs ytterligare i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Återställning av serverpunkt i tid

Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern lämnas som den är. Om en tabell till exempel av misstag tas bort vid lunchtid i dag kan du återställa till tiden strax före klockan tolv. Sedan kan du hämta den saknade tabellen och data från den återställda kopian av servern.

Om du vill återställa servern använder du kommandot Azure CLI [az mariadb server restore.](/cli/azure/mariadb/server#az-mariadb-server-restore)

### <a name="run-the-restore-command"></a>Kör återställningskommandot

Om du vill återställa servern anger du följande kommando i kommandotolken i Azure CLI:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Kommandot `az mariadb server restore` kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Den resursgrupp där källservern finns.  |
| namn | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan till exempel använda en egen `2018-03-13T05:59:00-08:00`lokal tidszon, till exempel . Du kan också använda UTC Zulu-formatet, till exempel `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

WNär du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats- och prisnivåvärdena för den återställda servern förblir desamma som den ursprungliga servern. 

När återställningen är klar letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggningsnamn och lösenord för serveradministratörer som var giltigt för den befintliga servern när återställningen initierades. Lösenordet kan ändras från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte de slutpunkter för VNet-tjänsten som fanns på den ursprungliga servern. Dessa regler måste ställas in separat för den nya servern. Brandväggsregler från den ursprungliga servern återställs.

## <a name="geo-restore"></a>Geo återställa

Om du har konfigurerat servern för geografiskt redundanta säkerhetskopior kan en ny server skapas från säkerhetskopian av den befintliga servern. Den här nya servern kan skapas i alla regioner som Azure Database för MariaDB är tillgänglig.  

Om du vill skapa en server med `az mariadb server georestore` hjälp av en geo redundant säkerhetskopiering använder du kommandot Azure CLI.

> [!NOTE]
> När en server först skapas kanske den inte är omedelbart tillgänglig för geoåterställning. Det kan ta några timmar innan de nödvändiga metadata fylls i.
>

Om du vill återställa servern geo anger du följande kommando i Kommandotolken i Azure CLI:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Detta kommando skapar en ny server som kallas *mydemoserver-georestored* i östra USA som kommer att *tillhöra myresourcegroup*. Det är ett allmänt syfte, Gen 5 server med 8 vCores. Servern skapas från den geosertaundanta säkerhetskopian av *mindemoserver*, som också finns i resursgruppen *myresourcegroup*

Om du vill skapa den nya servern i en annan resursgrupp än den befintliga servern, kvalificerar du servernamnet som i följande exempel i `--source-server` parametern:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Kommandot `az mariadb server georestore` kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|resource-group| myresourcegroup | Namnet på den resursgrupp som den nya servern kommer att tillhöra.|
|namn | mydemoserver-georestored | Namnet på den nya servern. |
|source-server | mydemoserver | Namnet på den befintliga servern vars geo redundanta säkerhetskopior används. |
|location | eastus | Platsen för den nya servern. |
|sku-name| GP_Gen5_8 | Den här parametern anger prisnivå, beräkningsgenerering och antal virtuella kärnor för den nya servern. GP_Gen5_8 kartor till ett allmänt syfte, Gen 5 server med 8 vCores.|

När du skapar en ny server genom en geoåterställning ärver den samma lagringsstorlek och prisnivå som källservern. Dessa värden kan inte ändras när de skapas. När den nya servern har skapats kan lagringsstorleken skalas upp.

När återställningen är klar letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggningsnamn och lösenord för serveradministratörer som var giltigt för den befintliga servern när återställningen initierades. Lösenordet kan ändras från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte de slutpunkter för VNet-tjänsten som fanns på den ursprungliga servern. Dessa regler måste ställas in separat för den nya servern. Brandväggsregler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg

- Läs mer om tjänstens [säkerhetskopior](concepts-backup.md)
- Läs mer om [repliker](concepts-read-replicas.md)
- Läs mer om alternativ [för affärskontinuitet](concepts-business-continuity.md)
