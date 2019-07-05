---
title: Granskningsloggar för Azure Database for MariaDB
description: Beskriver granskningsloggarna i Azure-databas för MariaDB, och de tillgängliga parametrarna för att aktivera loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 13ea60c62283db35ce4bf9fde6c3b36ba7f88013
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439206"
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

## <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

I följande avsnitt beskriver vad som är resultatet av MariaDB granskningsloggarna utifrån händelsetyp. De fält som ingår och i vilken ordning som de visas kan variera beroende på utdata-metod.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unikt anslutnings-ID som genereras av MariaDB |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienter som ansluter till MariaDB |
| `user_s` | Namnet på användaren som kör frågan |
| `db_s` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för Allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMINISTRATÖREN händelsetyper.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Fråga starta sekunder i UNIX-tidsstämpel |
| `error_code_d` | Felkod om frågan misslyckades. `0` innebär att inga fel |
| `thread_id_d` | ID för tråden som har kört frågan |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienter som ansluter till MariaDB |
| `user_s` | Namnet på användaren som kör frågan |
| `sql_text_s` | Fullständig frågetexten |
| `\_ResourceId` | Resurs-URI |

### <a name="table-access"></a>Tabellåtkomst

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`, eller `DELETE` |
| `connection_id_d` | Unikt anslutnings-ID som genereras av MariaDB |
| `db_s` | Namnet på databasen som används |
| `table_s` | Namnet på tabellen som nås |
| `sql_text_s` | Fullständig frågetexten |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du granskningsloggar i Azure portal](howto-configure-audit-logs-portal.md)