---
title: Konfigurera serverparametrar - Azure-portal - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar MySQL-serverparametrar i Azure Database for MySQL med Hjälp av Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605162"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurera serverparametrar i Azure Database för MySQL med hjälp av Azure-portalen

Azure Database for MySQL stöder konfiguration av vissa serverparametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure-portalen. Alla serverparametrar kan inte justeras.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen

1. Logga in på Azure-portalen och leta sedan upp din Azure-databas för MySQL-server.
2. Klicka på **Serverparametrar** under avsnittet **INSTÄLLNINGAR** för att öppna sidan serverparametrar för Azure-databasen för MySQL-servern.
![Sidan Parametrar för Azure Portal-server](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Leta reda på alla inställningar som du behöver justera. Granska kolumnen **Beskrivning** för att förstå syftet och tillåtna värden.
![Räkna upp nedsläpp](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** om du vill spara ändringarna.
![Spara eller ignorera ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt till standardvärdena genom att välja **Återställ alla till standard**.
![Återställa alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över serverparametrar som stöds växer ständigt. Använd fliken serverparametrar i Azure-portalen för att hämta definitionen och konfigurera serverparametrar baserat på dina programkrav.

## <a name="non-configurable-server-parameters"></a>Serverparametrar som inte kan konfigureras

Storleken på Buffertpoolen innoDB kan inte konfigureras och är knuten till din [prisnivå.](concepts-service-tiers.md)

|**Prisnivå**|**vCore(er)**|**Storlek på InnoDB <br>buffertpool i MB (servrar som stöder upp till 4 TB lagring)**| **Storlek på InnoDB <br>buffertpool i MB (servrar med stöd för upp till 16 TB lagring)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Generell användning| 2| 3584| 7168|
|Generell användning| 4| 7680| 15360|
|Generell användning| 8| 15360| 30720|
|Generell användning| 16| 31232| 62464|
|Generell användning| 32| 62976| 125952|
|Generell användning| 64| 125952| 251904|
|Minnesoptimerad| 2| 7168| 14336|
|Minnesoptimerad| 4| 15360| 30720|
|Minnesoptimerad| 8| 30720| 61440|
|Minnesoptimerad| 16| 62464| 124928|
|Minnesoptimerad| 32| 125952| 251904|

Dessa ytterligare serverparametrar kan inte konfigureras i systemet:

|**Parametern**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Andra serverparametrar som inte visas här är inställda på deras MySQL-standardvärden för inbyggda i rutan för [versionerna 5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

Se MySQL-dokumentationen för [datum- och tidsfunktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutningsbibliotek för Azure Database för MySQL](concepts-connection-libraries.md).
