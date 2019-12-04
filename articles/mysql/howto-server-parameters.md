---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar MySQL-serverns parametrar i Azure Database for MySQL att använda Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f803b7cccf3520c309e6b33d99b5565cfc4fdd01
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764924"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MySQL med hjälp av Azure Portal

Azure Database for MySQL stöder konfiguration av vissa Server parametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure Portal. Det går inte att justera alla Server parametrar.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till Server parametrar på Azure Portal

1. Logga in på Azure Portal och leta sedan upp Azure Database for MySQL-servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MySQL-servern.
Sidan ![Azure Portal Server parametrar](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Leta upp de inställningar du behöver för att justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
![räkna upp List rutan](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** för att spara ändringarna.
![Spara eller ta bort ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara Server parametrar

Listan över Server parametrar som stöds växer ständigt. Använd fliken Server parametrar i Azure Portal för att hämta definitionen och konfigurera server parametrarna utifrån dina program krav.

## <a name="non-configurable-server-parameters"></a>Server parametrar som inte går att konfigurera

InnoDB buffer-storlek kan inte konfigureras och är knutet till din [pris nivå](concepts-service-tiers.md).

|**Prisnivå**|**vCore (s)**|**InnoDB buffertstorlek i MB <br>(servrar som stöder upp till 4 TB lagrings utrymme)**| **InnoDB buffertstorlek i MB <br>(servrar som stöder upp till 16 TB lagring)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Generellt syfte| 2| 3584| 7168|
|Generellt syfte| 4| 7680| 15360|
|Generellt syfte| 8| 15360| 30720|
|Generellt syfte| 16| 31232| 62464|
|Generellt syfte| 32| 62976| 125952|
|Generellt syfte| 64| 125952| 251904|
|Minnesoptimerad| 2| 7168| 14336|
|Minnesoptimerad| 4| 15360| 30720|
|Minnesoptimerad| 8| 30720| 61440|
|Minnesoptimerad| 16| 62464| 124928|
|Minnesoptimerad| 32| 125952| 251904|

Dessa ytterligare Server parametrar kan inte konfigureras i systemet:

|**ProfileServiceApplicationProxy**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|Rund|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Andra server parametrar som inte listas här anges som standardvärden för MySQL-standardvärden för versionerna [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) och [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Tids zons tabellerna på servern kan fyllas i genom att anropa den `az_load_timezone` lagrade proceduren från ett verktyg som MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör kommandot `az_load_timezone` från MySQL Workbench, kan du behöva inaktivera säkert uppdaterings läge först med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. I nedan anges den globala tids zonen till värdet "US/Pacific".

![Ange tids zons parameter](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan anges genom att köra kommandot `SET time_zone` från ett verktyg som MySQL kommando rad eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för Azure Database for MySQL](concepts-connection-libraries.md).
