---
title: Hur du konfigurerar serverparametrar i Azure-databas för MariaDB
description: Den här artikeln beskriver hur du konfigurerar MariaDB serverparametrar i Azure Database for MariaDB med Azure portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c618a4035e9ec9b1ca1986e898ea1060ac05712d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922590"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Hur du konfigurerar serverparametrar i Azure Database for MariaDB med hjälp av Azure portal

Azure Database for MariaDB stöder konfiguration av vissa serverparametrar. Den här artikeln beskriver hur du konfigurerar dessa parametrar med hjälp av Azure portal. Inte alla serverparametrar kan justeras.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen

1. Logga in på Azure portal och leta upp din Azure Database for MariaDB-server.
2. Under den **inställningar** klickar du på **serverparametrar** att öppna sidan parametrar för Azure Database for MariaDB-server.
![Sidan för Azure-portalservern parametrar](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Leta upp alla inställningar som du behöver justera. Granska den **beskrivning** kolumn att förstå syftet med och tillåtna värden.
![Räkna upp Släpp ned](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **spara** att spara dina ändringar.
![Spara eller ignorera ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna, kan du alltid återgå allt till standardvärden genom att välja **Återställ allt till standardinställningarna**.
![Återställ allt till standardinställningarna](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över stöds serverparametrar växer hela tiden. Använda fliken server parametrar i Azure portal för att hämta definitionen och konfigurera serverparametrar baserat på dina programkrav.

## <a name="non-configurable-server-parameters"></a>Inte går att konfigurera serverparametrar

InnoDB Buffertpoolen och högsta antal anslutningar är inte kan konfigureras och bundet till din [prisnivån](concepts-pricing-tiers.md).

|**Prisnivå**| **virtuella kärnor**|**InnoDB Buffertpoolen (MB)**| **Högsta antal anslutningar**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Generellt syfte| 2| 3584| 300|
|Generellt syfte| 4| 7680| 625|
|Generellt syfte| 8| 15360| 1250|
|Generellt syfte| 16| 31232| 2500|
|Generellt syfte| 32| 62976| 5000|
|Generellt syfte| 64| 125952| 10000|
|Minnesoptimerad| 2| 7168| 600|
|Minnesoptimerad| 4| 15360| 1250|
|Minnesoptimerad| 8| 30720| 2500|
|Minnesoptimerad| 16| 62464| 5000|
|Minnesoptimerad| 32| 125952| 10000|

Dessa ytterligare server-parametrar kan inte konfigureras i systemet:

|**Parametern**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|AV|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra serverparametrar som inte listas här är inställda på MariaDB out-of-box standardvärdena för [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

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

MariaDB-dokumentationen för [datum- och tidsfunktioner](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
