---
title: Granskningsloggar för Azure Database for MariaDB
description: Beskriver granskningsloggarna i Azure-databas för MariaDB, och de tillgängliga parametrarna för att aktivera loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 765db8461465b74ac068782c1b91d3c68b73f7d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079527"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Granskningsloggar i Azure-databas för MariaDB

I Azure Database for MariaDB är granskningsloggen tillgängliga för användare. Granskningsloggen kan användas för att spåra aktiviteter på databasnivå och används ofta för kompatibilitet.

> [!IMPORTANT]
> Granska loggen funktionen finns för närvarande i förhandsversion.

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Granskningsloggen är inaktiverad som standard. Aktivera det genom att ange `audit_log_enabled` on.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedan tabellen för specifika granskningshändelser.
- `audit_log_exclude_users`: MariaDB-användare som ska undantas från att logga in. Tillåter högst fyra användare. Maxlängd för parametern är 256 tecken.

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

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Granskningsloggarna är integrerade med Azure Monitor-diagnostikloggar. När du har aktiverat granskningsloggar på MariaDB-servern, kan du generera dem till Azure Monitor-loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure-portalen finns i [Granska loggen portal artikeln](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="schemas"></a>Scheman

I följande avsnitt beskriver vad som är resultatet av MariaDB granskningsloggarna utifrån händelsetyp. De fält som ingår och i vilken ordning som de visas kan variera beroende på utdata-metod.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`, `DISCONNECT` |
| `connection_id` | Unikt anslutnings-ID som genereras av MariaDB |
| `host` | Tom |
| `ip` | IP-adressen för klienter som ansluter till MariaDB |
| `user` | Namnet på användaren som kör frågan |
| `db` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för Allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMINISTRATÖREN händelsetyper.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när tshe log registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Fråga starta sekunder i UNIX-tidsstämpel |
| `error_code` | Felkod om frågan misslyckades. `0` innebär att inga fel |
| `thread_id` | ID för tråden som har kört frågan |
| `host` | Tom |
| `ip` | IP-adressen för klienter som ansluter till MariaDB |
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
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`, `INSERT`, `UPDATE`, eller `DELETE` |
| `connection_id` | Unikt anslutnings-ID som genereras av MariaDB |
| `db` | Namnet på databasen som används |
| `table` | Namnet på tabellen som nås |
| `sql_text` | Fullständig frågetexten |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du granskningsloggar i Azure portal](howto-configure-audit-logs-portal.md)