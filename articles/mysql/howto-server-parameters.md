---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar MySQL-serverns parametrar i Azure Database for MySQL att använda Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: c6e4ff494ee79428f7d9e6a55d184b877c0d58e4
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554945"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MySQL med hjälp av Azure Portal

Azure Database for MySQL stöder konfiguration av vissa Server parametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure Portal. Det går inte att justera alla Server parametrar.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till Server parametrar på Azure Portal

1. Logga in på Azure Portal och leta sedan upp Azure Database for MySQL-servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MySQL-servern.
![Sidan Azure Portal Server parametrar](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Leta upp de inställningar du behöver för att justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
![Räkna upp listruta](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** för att spara ändringarna.
![Spara eller ta bort ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva inaktivera läget för säker uppdatering först med `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. I nedan anges den globala tids zonen till värdet "US/Pacific".

![Ange tids zons parameter](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för Azure Database for MySQL](concepts-connection-libraries.md).
