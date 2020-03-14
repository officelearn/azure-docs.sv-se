---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MySQL
description: Den här artikeln beskriver hur du konfigurerar MySQL-serverparametrar i Azure Database för MySQL med Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245725"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Hur du konfigurerar serverparametrar i Azure Database för MySQL med hjälp av Azure portal

Azure Database för MySQL stöder konfiguration av vissa serverparametrar. Den här artikeln beskriver hur du konfigurerar dessa parametrar med hjälp av Azure portal. Inte alla serverparametrar kan justeras.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till serverparametrar på Azure-portalen

1. Logga in på Azure portal och leta upp din Azure Database for MySQL-server.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MySQL-servern.
Sidan ![Azure Portal Server parametrar](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Leta upp alla inställningar som du behöver justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
![räkna upp List rutan](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** för att spara ändringarna.
![Spara eller ta bort ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara serverparametrar

Listan över stöds serverparametrar växer hela tiden. Använda fliken server parametrar i Azure portal för att hämta definitionen och konfigurera serverparametrar baserat på dina programkrav.

## <a name="non-configurable-server-parameters"></a>Inte går att konfigurera serverparametrar

InnoDB buffer-storlek kan inte konfigureras och är knutet till din [pris nivå](concepts-service-tiers.md).

|**Prisnivå**|**vCore (s)**|**InnoDB buffertstorlek i MB <br>(servrar som stöder upp till 4 TB lagrings utrymme)**| **InnoDB buffertstorlek i MB <br>(servrar som stöder upp till 16 TB lagring)**|
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

Dessa ytterligare server-parametrar kan inte konfigureras i systemet:

|**ProfileServiceApplicationProxy**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra server parametrar som inte listas här anges som standardvärden för MySQL-standardvärden för versionerna [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med parametern tidszon

### <a name="populating-the-time-zone-tables"></a>Fylla i tabellerna tidszon

Tids zons tabellerna på servern kan fyllas i genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör kommandot `az_load_timezone` från MySQL Workbench, kan du behöva inaktivera säkert uppdaterings läge först med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Om du vill visa tillgängliga tidszon värden, kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Tidszonen global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. Den nedan anger globala tidszonen till värdet ”USA / Stillahavsområdet”.

![En parameter för tidszon](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Tidszonen session nivå

Tids zonen för tids zonen kan anges genom att köra kommandot `SET time_zone` från ett verktyg som MySQL kommando rad eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för Azure Database for MySQL](concepts-connection-libraries.md).
