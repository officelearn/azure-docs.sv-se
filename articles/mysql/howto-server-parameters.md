---
title: Hur du konfigurerar serverparametrar i Azure Database för MySQL
description: Den här artikeln beskriver hur du konfigurerar MySQL-serverparametrar i Azure Database för MySQL med Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 103e09a0e2b9dd409fa2ddaff1c5311ef9936d22
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538517"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Hur du konfigurerar serverparametrar i Azure Database för MySQL med hjälp av Azure portal

Azure Database för MySQL stöder konfiguration av vissa serverparametrar. Den här artikeln beskriver hur du konfigurerar dessa parametrar med hjälp av Azure portal. Inte alla serverparametrar kan justeras.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen

1. Logga in på Azure portal och leta upp din Azure Database for MySQL-server.
2. Under den **inställningar** klickar du på **serverparametrar** att öppna sidan parametrar för Azure Database for MySQL-server.
![Sidan för Azure-portalservern parametrar](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Leta upp alla inställningar som du behöver justera. Granska den **beskrivning** kolumn att förstå syftet med och tillåtna värden.
![Räkna upp Släpp ned](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **spara** att spara dina ändringar.
![Spara eller ignorera ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna, kan du alltid återgå allt till standardvärden genom att välja **Återställ allt till standardinställningarna**.
![Återställ allt till standardinställningarna](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över stöds serverparametrar växer hela tiden. Använda fliken server parametrar i Azure portal för att hämta definitionen och konfigurera serverparametrar baserat på dina programkrav.

## <a name="non-configurable-server-parameters"></a>Inte går att konfigurera serverparametrar

InnoDB Buffertpoolen och högsta antal anslutningar är inte kan konfigureras och bundet till din [prisnivån](concepts-service-tiers.md).

|**Prisnivå**| **Compute-generering**|**virtuella kärnor**|**InnoDB Buffertpoolen (MB)**| **Högsta antal anslutningar**|
|---|---|---|---|--|
|Basic| Generation 4| 1| 960| 50|
|Basic| Generation 4| 2| 2560| 100|
|Basic| Generation 5| 1| 960| 50|
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
|Generellt syfte| Generation 5| 64| 125952| 10000|
|Minnesoptimerad| Generation 5| 2| 7168| 600|
|Minnesoptimerad| Generation 5| 4| 15360| 1250|
|Minnesoptimerad| Generation 5| 8| 30720| 2500|
|Minnesoptimerad| Generation 5| 16| 62464| 5000|
|Minnesoptimerad| Generation 5| 32| 125952| 10000|

Dessa ytterligare server-parametrar kan inte konfigureras i systemet:

|**Parametern**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|AV|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra serverparametrar som inte listas här är inställda på MySQL out-of-box standardvärdena för versioner [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med parametern tidszon

### <a name="populating-the-time-zone-tables"></a>Fylla i tabellerna tidszon

Tidszon tabellerna på din server kan fyllas genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör den `az_load_timezone` kommandot från MySQL Workbench, du kan behöva inaktivera säker uppdateringsläget första med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Om du vill visa tillgängliga tidszon värden, kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Tidszonen global nivå

Den globala nivån tidszonen kan ställas in från den **serverparametrar** sidan på Azure portal. Den nedan anger globala tidszonen till värdet ”USA / Stillahavsområdet”.

![En parameter för tidszon](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Tidszonen session nivå

Sessionen kan ställas in på tidszonen genom att köra den `SET time_zone` från ett verktyg som MySQL-kommandoraden eller MySQL Workbench. Exemplet nedan anger tidszonen till den **USA / Stillahavsområdet** tidszon.

```sql
SET time_zone = 'US/Pacific';
```

MySQL-dokumentationen för [datum- och tidsfunktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutningsbibliotek för Azure Database for MySQL](concepts-connection-libraries.md).
