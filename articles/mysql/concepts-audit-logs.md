---
title: Granskningsloggar för Azure Database for MySQL
description: Beskriver granskningsloggarna i Azure Database för MySQL och tillgängliga parametrar för att aktivera loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a82afe6f5299609fd6dd57a54f04f49fad5d2268
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357649"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Granskningsloggar i Azure Database for MySQL

Granskningsloggen är tillgänglig för användare i Azure Database för MySQL. Granskningsloggen kan användas för att spåra aktiviteter på databasnivå och används ofta för kompatibilitet.

> [!IMPORTANT]
> Granska loggen funktionen finns för närvarande i förhandsversion.

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Granskningsloggen är inaktiverad som standard. Aktivera det genom att ange `audit_log_enabled` on.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedan tabellen för specifika granskningshändelser.
- `audit_log_exclude_users`: MySQL-användare som ska undantas från att logga in. Tillåter högst fyra användare. Maxlängd för parametern är 256 tecken.

| **Händelse** | **Beskrivning** |
|---|---|
| `CONNECTION` | -Anslutningen öppnas (lyckade eller misslyckade) <br> -Användare omautentisering med olika användare/lösenord under session <br> -Connection-avslutning |
| `DML_SELECT`| SELECT-frågor |
| `DML_NONSELECT` | Infoga/Ta bort/uppdatera frågor |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Frågor som ”DROP DATABASE” |
| `DCL` | Frågor som ”ge behörighet” |
| `ADMIN` | Frågor som ”Visa STATUS” |
| `GENERAL` | Allt i DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN |
| `TABLE_ACCESS` | – Endast tillgängligt för MySQL 5.7 <br> -Tabell Läs instruktioner, till exempel väljer eller INSERT INTO... VÄLJ <br> -Table delete-instruktioner, till exempel DELETE eller TRUNCATE TABLE <br> -Table insert-satser, till exempel infoga eller ERSÄTTA <br> -Table update-instruktioner, till exempel uppdatering |

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Granskningsloggarna är integrerade med Azure Monitor-diagnostikloggar. När du har aktiverat granskningsloggar på MySQL-servern, kan du generera dem till Azure Monitor-loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure-portalen finns i [Granska loggen portal artikeln](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

I följande avsnitt beskriver vad som är resultatet av MySQL-granskningsloggarna utifrån händelsetyp. De fält som ingår och i vilken ordning som de visas kan variera beroende på utdata-metod.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (endast tillgängligt för MySQL 5.7) |
| `connection_id` | Unikt anslutnings-ID som genereras av MySQL |
| `host` | Tom |
| `ip` | IP-adressen för klienter som ansluter till MySQL |
| `user` | Namnet på användaren som kör frågan |
| `db` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för Allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMINISTRATÖREN händelsetyper.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` (endast tillgängligt för MySQL 5.6) |
| `event_time` | Fråga starta sekunder i UNIX-tidsstämpel |
| `error_code` | Felkod om frågan misslyckades. `0` innebär att inga fel |
| `thread_id` | ID för tråden som har kört frågan |
| `host` | Tom |
| `ip` | IP-adressen för klienter som ansluter till MySQL |
| `user` | Namnet på användaren som kör frågan |
| `sql_text` | Fullständig frågetexten |
| `\_ResourceId` | Resurs-URI |

### <a name="table-access"></a>Tabellåtkomst

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, eller `DELETE` |
| `connection_id` | Unikt anslutnings-ID som genereras av MySQL |
| `db` | Namnet på databasen som används |
| `table` | Namnet på tabellen som nås |
| `sql_text` | Fullständig frågetexten |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du granskningsloggar i Azure portal](howto-configure-audit-logs-portal.md)