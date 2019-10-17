---
title: Gransknings loggar för Azure Database for MySQL
description: Beskriver de gransknings loggar som är tillgängliga i Azure Database for MySQL och de tillgängliga parametrarna för att aktivera loggnings nivåer.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434399"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Gransknings loggar i Azure Database for MySQL

I Azure Database for MySQL är gransknings loggen tillgänglig för användare. Gransknings loggen kan användas för att spåra aktivitet på databas nivå och används ofta för efterlevnad.

> [!IMPORTANT]
> Gransknings logg funktionen är för närvarande en för hands version.

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

Som standard är gransknings loggen inaktive rad. Om du vill aktivera det anger du `audit_log_enabled` till på.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedanstående tabell för vissa gransknings händelser.
- `audit_log_include_users`: MySQL-användare ska inkluderas för loggning. Standardvärdet för den här parametern är tomt, som innehåller alla användare som ska loggas. Detta har högre prioritet än `audit_log_exclude_users`. Parameterns max längd är 512 tecken.
> [!Note]
> `audit_log_include_users` har högre prioritet än `audit_log_exclude_users` till exempel om audit_log_include_users = `demouser` och audit_log_exclude_users = `demouser`, så granskar den loggarna eftersom `audit_log_include_users` har högre prioritet.
- `audit_log_exclude_users`: MySQL-användare undantas från loggning. Parameterns max längd är 512 tecken.

> [!Note]
> För `sql_text` trunkeras loggen om den innehåller fler än 2048 tecken.

| **Händelse** | **Beskrivning** |
|---|---|
| `CONNECTION` | -Anslutnings initiering (lyckades eller misslyckades) <br> – Användarautentisering med annan användare/lösen ord under sessionen <br> -Anslutnings terminering |
| `DML_SELECT`| Välj frågor |
| `DML_NONSELECT` | Infoga/ta bort/uppdatera frågor |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Frågor som "släpp databas" |
| `DCL` | Frågor som "beviljande behörighet" |
| `ADMIN` | Frågor som "Visa STATUS" |
| `GENERAL` | Alla i DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN |
| `TABLE_ACCESS` | – Endast tillgängligt för MySQL 5,7 <br> – Läs instruktioner för tabell, t. ex. SELECT eller INSERT INTO... SELECT <br> – Tabell borttagnings instruktioner, till exempel ta bort eller TRUNCATE TABLE <br> – Infoga uttryck för tabeller, till exempel infoga eller Ersätt <br> – Tabell uppdaterings instruktioner, till exempel uppdatering |

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Gransknings loggar är integrerade med Azure Monitor diagnostikloggar. När du har aktiverat gransknings loggar på MySQL-servern kan du generera dem till Azure Monitor loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure Portal finns i [artikeln Gransknings logg Portal](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnostiska loggar scheman

I följande avsnitt beskrivs vad som utdata av MySQLs gransknings loggar baserat på händelse typen. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Always `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Always `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (endast tillgängligt för MySQL 5,7) |
| `connection_id_d` | Unikt anslutnings-ID som genererats av MySQL |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MySQL |
| `user_s` | Namn på användaren som kör frågan |
| `db_s` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för händelse typerna GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Always `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Always `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (endast tillgängligt för MySQL 5,6) |
| `event_time` | Frågans start tid i UTC-tidsstämpel |
| `error_code_d` | Felkod om frågan misslyckades. `0` innebär inget fel |
| `thread_id_d` | ID för tråd som körde frågan |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MySQL |
| `user_s` | Namn på användaren som kör frågan |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

### <a name="table-access"></a>Tabell åtkomst

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Always `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Always `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` eller `DELETE` |
| `connection_id_d` | Unikt anslutnings-ID som genererats av MySQL |
| `db_s` | Namnet på databasen som används |
| `table_s` | Namnet på tabellen har öppnats |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du gransknings loggar i Azure Portal](howto-configure-audit-logs-portal.md)