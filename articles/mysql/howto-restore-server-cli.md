---
title: Säkerhetskopiera och återställa en server i Azure-databas för MySQL
description: Lär dig hur du säkerhetskopierar och återställer en server i Azure-databas för MySQL med hjälp av Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5325f23a13a181d912bbc8b26042de72855dc41e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319096"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Säkerhetskopiera och återställa en server i Azure-databas för MySQL med hjälp av Azure CLI

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
Azure-databas för MySQL-servrar säkerhetskopieras regelbundet för att aktivera funktioner för återställning. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare i tidpunkt, på en ny server.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här instruktioner:
- En [Azure-databas för MySQL-server och databas](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Den här instruktioner kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta version Kommandotolken Azure CLI, ange `az --version`. Om du vill installera eller uppgradera, se [installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Ange konfigurationen för säkerhetskopiering

Du välja mellan att konfigurera servern för lokalt redundant säkerhetskopiering eller geografiskt redundant säkerhetskopiering på server har skapats. 

> [!NOTE]
> När en server har skapats kan typ av redundans som finns, kan inte växlas geografiskt redundant jämfört med lokalt redundant.
>

När du skapar en server via den `az mysql server create` kommando, den `--geo-redundant-backup` parametern beslutar alternativ för redundans en säkerhetskopia. Om `Enabled`, geo-redundant säkerhetskopieringar vidtas. Eller om `Disabled` lokalt redundant säkerhetskopieringar vidtas. 

Säkerhetskopiering loggperioden anges av parametern `--backup-retention`. 

Mer information om hur du anger dessa värden under skapa finns i [Azure-databas för MySQL server CLI Quickstart](quickstart-create-mysql-server-database-using-azure-cli.md).

Säkerhetskopiering kvarhållningsperiod på en server kan ändras på följande sätt:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Föregående exempel ändrar säkerhetskopiering kvarhållningsperioden för mydemoserver på 10 dagar.

Säkerhetskopiering loggperioden styr hur långt tillbaka i tiden en point-in-time-återställning kan hämtas, eftersom den är baserad på säkerhetskopior som är tillgängliga. Point-in-time-återställning är ytterligare beskrivs i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Servern point-in-time-återställning
Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern är kvar. Till exempel kan en tabell av misstag utelämnas kl. tolv idag så, du återställa tiden innan på dagen. Du kan sedan hämta saknas tabellen och data från den återställda kopian av servern. 

Använd Azure CLI för att återställa servern [az mysql server återställning](/cli/azure/mysql/server#az_mysql_server_restore) kommando.

### <a name="run-the-restore-command"></a>Kör kommandot restore

Om du vill återställa servern Kommandotolken Azure CLI, anger du följande kommando:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Den `az mysql server restore` kommandot kräver följande parametrar:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| namn | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Välj en punkt i tid att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd formatet ISO8601 datum och tid. Exempelvis kan du använda din egen lokala tidszon som `2018-03-13T05:59:00-08:00`. Du kan också använda Zulu UTC-format, till exempel `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidigare tidpunkt, skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats och prisnivå nivåvärden för den återställda servern vara densamma som den ursprungliga servern. 

När återställningen är klar, leta upp den nya servern och kontrollera att data återställs som förväntat.

## <a name="geo-restore"></a>GEO-återställning
Om du har konfigurerat din server för geografiskt redundant säkerhetskopiering till kan en ny server skapas från en säkerhetskopia av den befintliga servern. Den här nya servern kan skapas i en region som Azure-databasen för MySQL är tillgänglig.  

Använd Azure CLI för att skapa en server med en geo-redundant säkerhetskopia `az mysql server georestore` kommando.

> [!NOTE]
> När en server först skapas kanske det inte omedelbart tillgängliga för geo-återställning. Det kan ta några timmar för nödvändiga metadata fyllas.
>

Ange följande kommando för att återställa geo server Kommandotolken Azure CLI:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Det här kommandot skapar en ny server som kallas *mydemoserver georestored* i östra USA som hör till *myresourcegroup*. Det är en generell Gen 4 server med 8 vCores. Servern har skapats från en geo-redundant säkerhetskopia av *mydemoserver*, vilket är också i resursgruppen *myresourcegroup*

Om du vill skapa den nya servern i en annan resursgrupp än den befintliga servern sedan i den `--source-server` parametern väljer du namnet på server som i följande exempel:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Den `az mysql server georestore` kommandot requies följande parametrar:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|resource-group| myresourcegroup | Namnet på resursgruppen den nya servern ska tillhöra.|
|namn | mydemoserver georestored | Namnet på den nya servern. |
|source-server | mydemoserver | Namnet på den befintliga servern vars geo-redundant säkerhetskopieringar används. |
|location | usaöstra | Platsen för den nya servern. |
|SKU-namn| GP_Gen4_8 | Den här parametern anger prisnivå nivå, beräkning generation och antalet vCores på den nya servern. GP_Gen4_8 mappar till en generell Gen 4 server med 8 vCores.|


>[!Important]
>När du skapar en ny server med geo-återställning, ärver samma lagringsutrymme och prisnivå som källservern. Dessa värden kan inte ändras när du skapar. När du har skapat den nya servern skalas dess lagringsstorlek upp.

När återställningen är klar, leta upp den nya servern och kontrollera att data återställs som förväntat.

## <a name="next-steps"></a>Nästa steg
- Mer information om tjänstens [säkerhetskopieringar](concepts-backup.md).
- Lär dig mer om [företagskontinuitet](concepts-business-continuity.md) alternativ.
