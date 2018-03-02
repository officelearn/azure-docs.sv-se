---
title: "Säkerhetskopiera och återställa en server i Azure-databas för MySQL"
description: "Lär dig hur du säkerhetskopierar och återställer en server i Azure-databas för MySQL med hjälp av Azure CLI."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b954e26c9ecb1767b971117fc9102e8573beaaac
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-cli"></a>Säkerhetskopiera och återställa en server i Azure-databas för MySQL med hjälp av Azure CLI

Använd Azure-databas för MySQL för att återställa en server-databas till ett tidigare datum som sträcker sig från 7 till 35 dagar.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här instruktioner:
- En [Azure-databas för MySQL-server och databas](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Om du installerar och använder Azure CLI lokalt, kräver den här instruktioner att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta version Kommandotolken Azure CLI, ange `az --version`. Om du vill installera eller uppgradera, se [installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
När du använder Azure-databas för MySQL görs databastjänsten en säkerhetskopia av tjänsten var femte minut. 

För grundläggande nivån finns säkerhetskopieringar för 7 dagar. För standardnivån är säkerhetskopior tillgängliga för 35 dagar. Mer information finns i [Azure-databas för MySQL prisnivåer](concepts-pricing-tiers.md).

Med funktionen om automatisk säkerhetskopiering kan du återställa servern och databaserna till ett tidigare datum eller tidpunkt.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Återställa en databas till en tidigare tidpunkt med hjälp av Azure CLI
Använd Azure-databas för MySQL för att återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern är kvar. Till exempel kan en tabell av misstag utelämnas kl. tolv idag så, du återställa tiden innan på dagen. Du kan sedan hämta saknas tabellen och data från den återställda kopian av servern. 

Använd Azure CLI för att återställa servern [az mysql server återställning](/cli/azure/mysql/server#az_mysql_server_restore) kommando.

### <a name="run-the-restore-command"></a>Kör kommandot restore

Om du vill återställa servern Kommandotolken Azure CLI, anger du följande kommando:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name myserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Den `az mysql server restore` kommandot kräver följande parametrar:
| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resursgruppen. | myresourcegroup |  Resursgruppen där källservern finns.  |
| namn | myserver-restored | Namnet på den nya servern som skapas med kommandot restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Välj en punkt i tid att återställa till. Datumet och tiden måste vara inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd formatet ISO8601 datum och tid. Exempelvis kan du använda din egen lokala tidszon som `2017-04-13T05:59:00-08:00`. Du kan också använda Zulu UTC-format, till exempel `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Namnet eller ID på källservern för att återställa från. |

När du återställer en server till en tidigare tidpunkt, skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats och prisnivå nivåvärden för den återställda servern vara densamma som den ursprungliga servern. 

Den `az mysql server restore` kommandot är synkront. När servern har återställts kan använda du den för att upprepa processen för en annan tidpunkt. 

När återställningen är klar, leta upp den nya servern och kontrollera att data återställs som förväntat.

## <a name="next-steps"></a>Nästa steg
[Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md)
