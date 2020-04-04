---
title: Konfigurera serverparametrar - Azure portal - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du konfigurerar MariaDB-serverparametrar i Azure Database för MariaDB med Hjälp av Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9d057a4be02d8d93d3ef02ee3153baebe9146ff1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632718"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Konfigurera serverparametrar i Azure Database för MariaDB med hjälp av Azure-portalen

Azure Database för MariaDB stöder konfiguration av vissa serverparametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure-portalen. Alla serverparametrar kan inte justeras.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen

1. Logga in på Azure-portalen och leta sedan upp din Azure-databas för MariaDB-server.
2. Klicka på **Serverparametrar** under avsnittet **INSTÄLLNINGAR** för att öppna sidan serverparametrar för Azure Database för MariaDB-servern.
![Sidan Parametrar för Azure Portal-server](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Leta reda på alla inställningar som du behöver justera. Granska kolumnen **Beskrivning** för att förstå syftet och tillåtna värden.
![Räkna upp nedsläpp](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** om du vill spara ändringarna.
![Spara eller ignorera ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt till standardvärdena genom att välja **Återställ alla till standard**.
![Återställa alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över serverparametrar som stöds växer ständigt. Använd fliken serverparametrar i Azure-portalen för att hämta definitionen och konfigurera serverparametrar baserat på dina programkrav.

## <a name="non-configurable-server-parameters"></a>Serverparametrar som inte kan konfigureras

InnoDB Buffer Pool och Max-anslutningar kan inte konfigureras och är knutna till din [prisnivå.](concepts-pricing-tiers.md)

|**Prisnivå**| **vCore(er)**|**Buffertpool (INnoDB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Generell användning| 2| 3584|
|Generell användning| 4| 7680|
|Generell användning| 8| 15360|
|Generell användning| 16| 31232|
|Generell användning| 32| 62976|
|Generell användning| 64| 125952|
|Minnesoptimerad| 2| 7168|
|Minnesoptimerad| 4| 15360|
|Minnesoptimerad| 8| 30720|
|Minnesoptimerad| 16| 62464|
|Minnesoptimerad| 32| 125952|

Dessa ytterligare serverparametrar kan inte konfigureras i systemet:

|**Parametern**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra serverparametrar som inte visas här är inställda på deras MariaDB-standardvärden för andra alternativ för [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tidszonsparametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tidszonstabellerna

Tidszonstabellerna på servern kan fyllas `mysql.az_load_timezone` i genom att anropa den lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva stänga `SET SQL_SAFE_UPDATES=0;`av felsäkert uppdateringsläge först med .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att säkerställa att tidszonstabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure-portalen](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).
Om du vill visa tillgängliga tidszonsvärden kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ställa in tidszonen på global nivå

Tidszonen på global nivå kan ställas in från sidan **Serverparametrar** i Azure-portalen. Nedan anges den globala tidszonen till värdet "USA/Stillahavsområdet".

![Ange tidszonsparameter](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ställa in tidszonen för sessionsnivå

Tidszonen för sessionsnivå kan ställas `SET time_zone` in genom att köra kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan anges tidszonen till tidszonen **USA/Stillahavsområdet.**

```sql
SET time_zone = 'US/Pacific';
```

Se MariaDB-dokumentationen för [datum- och tidsfunktioner](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
