---
title: Konfigurera parametrar för Server i Azure-databas för MySQL
description: Den här artikeln beskriver hur du konfigurerar MySQL serverparametrar i Azure-databas för MySQL med Azure-portalen.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 32414cb034bf6ee95a284021111759959054c00e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267313"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurera parametrar för server i Azure-databas för MySQL med hjälp av Azure portal

Azure-databas för MySQL stöder konfiguration av vissa serverparametrar. Den här artikeln beskriver hur du konfigurerar dessa parametrar med hjälp av Azure portal. Parametrar för server kan justeras. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen
1. Logga in på Azure-portalen och sedan leta upp din Azure-databas för MySQL-servern.
2. Under den **inställningar** klickar du på **serverparametrar** att öppna sidan parametrar för Azure-databas för MySQL.
![Sidan för Azure portalservern parametrar](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Hitta alla inställningar som du måste justera. Granska de **beskrivning** kolumnen att förstå syftet med och tillåtna värden. 
![Räkna upp Släpp ned](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **spara** att spara ändringarna.
![Spara eller ignorera ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna, kan du alltid återgå allt tillbaka till standardvärdena genom att välja **Återställ till standard**.
![Återställ till standard](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över parametrar stöds server växer ständigt. Använd fliken server parametrar i Azure-portalen för att hämta definitionen och konfigurera parametrar för server baserat på kraven för application. 

## <a name="nonconfigurable-server-parameters"></a>Nonconfigurable serverparametrar
InnoDB buffertpool och högsta antal anslutningar är inte kan konfigureras och bundet till din [prisnivån](concepts-service-tiers.md). 

|**Prisnivå**| **Compute-generering**|**vCore(s)**|**InnoDB buffertpool (MB)**| **Högsta antal anslutningar**|
|---|---|---|---|--|
|Basic| Generation 4| 1| 1024| 50|
|Basic| Generation 4| 2| 2560| 100|
|Basic| Generation 5| 1| 1024| 50|
|Basic| Generation 5| 2| 2560| 100|
|Generellt syfte| Generation 4| 2| 3584| 300|
|Generellt syfte| Generation 4| 4| 7680| 625|
|Generellt syfte| Generation 4| 8| 15360| 1250|
|Generellt syfte| Generation 4| 16| 31232| 2500|
|Generellt syfte| Generation 4| 32| 62976| 5000|
|Generellt syfte| Generation 5| 2| 3584| 300|
|Generellt syfte| Generation 5| 4| 7680| 625|
|Generellt syfte| Generation 5| 8| 15360| 1250|
|Generellt syfte| Generation 5| 16| 31232| 2500|
|Generellt syfte| Generation 5| 32| 62976| 5000|
|Minnesoptimerad| Generation 5| 2| 7168| 600|
|Minnesoptimerad| Generation 5| 4| 15360| 1250|
|Minnesoptimerad| Generation 5| 8| 30720| 2500|
|Minnesoptimerad| Generation 5| 16| 62464| 5000|

Dessa ytterligare server-parametrar kan inte konfigureras i systemet:

|**Parametern**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table i grundläggande nivån|AV|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra server som inte listas här har angetts till MySQL out-of-box standardvärdena för versioner [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Nästa steg
- [Anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md).
