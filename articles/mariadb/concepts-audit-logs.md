---
title: Granskningsloggar - Azure Database för MariaDB
description: Beskriver granskningsloggarna som är tillgängliga i Azure Database för MariaDB och de tillgängliga parametrarna för att aktivera loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063820"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Granskningsloggar i Azure Database för MariaDB

I Azure Database för MariaDB är granskningsloggen tillgänglig för användare. Granskningsloggen kan användas för att spåra aktivitet på databasnivå och används ofta för efterlevnad.

> [!IMPORTANT]
> Granskningsloggfunktionen är för närvarande i förhandsgranskning.

## <a name="configure-audit-logging"></a>Konfigurera granskningsloggning

Som standard är granskningsloggen inaktiverad. Om du vill `audit_log_enabled` aktivera den ställer du in på ON.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr de händelser som ska loggas. Se nedan för specifika granskningshändelser.
- `audit_log_include_users`: MariaDB-användare som ska inkluderas för loggning. Standardvärdet för den här parametern är tomt, vilket inkluderar alla användare för loggning. Detta har högre `audit_log_exclude_users`prioritet framför . Max längd på parametern är 512 tecken.
> [!Note]
> `audit_log_include_users`har högre `audit_log_exclude_users`prioritet framför . `audit_log_include_users`  =  `demouser` Om `audit_log_exclude_users`och  = kommer användaren till exempel att inkluderas `audit_log_include_users` i granskningsloggarna eftersom den har högre prioritet. `demouser`
- `audit_log_exclude_users`: MariaDB-användare ska uteslutas från loggning. Tillåter högst fyra användare. Den maximala längden på parametern är 256 tecken.

| **Händelse** | **Beskrivning** |
|---|---|
| `CONNECTION` | - Initiering av anslutning (lyckad eller misslyckad) <br> - Användarens omautentisering med olika användare/lösenord under sessionen <br> - Anslutningsavsärende |
| `DML_SELECT`| SELECT-frågor |
| `DML_NONSELECT` | INFOGA/TA BORT/UPPDATERA frågor |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Frågor som "DROP DATABASE" |
| `DCL` | Frågor som "BEVILJA TILLSTÅND" |
| `ADMIN` | Frågor som "VISA STATUS" |
| `GENERAL` | Allt i DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN |

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Granskningsloggar är integrerade med Azure Monitor Diagnostic Logs. När du har aktiverat granskningsloggar på din MariaDB-server kan du avge dem till Azure Monitor-loggar, eventhubbar eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure-portalen finns i [artikeln för granskningsloggportalen](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Scheman för diagnostikloggar

I följande avsnitt beskrivs vad som ska utdata från MariaDB-granskningsloggar baserat på händelsetypen. Beroende på utdatametoden kan de inkluderade fälten och i vilken ordning de visas variera.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidsstämpel när loggen spelades in i UTC |
| `Type` | Typ av logg. Alltid`AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Resursleverantörens namn. Alltid`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs URI |
| `Resource` | Serverns namn |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unikt anslutnings-ID som genereras av MariaDB |
| `host_s` | Blank |
| `ip_s` | IP-adress för klient som ansluter till MariaDB |
| `user_s` | Namn på användaren som kör frågan |
| `db_s` | Namn på databas som är ansluten till |
| `\_ResourceId` | Resurs URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för händelsetyper för ALLMÄNT, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidsstämpel när loggen spelades in i UTC |
| `Type` | Typ av logg. Alltid`AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Resursleverantörens namn. Alltid`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs URI |
| `Resource` | Serverns namn |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Serverns namn |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Fråga startsekunder i UNIX-tidsstämpel |
| `error_code_d` | Felkod om frågan misslyckades. `0`betyder inget fel |
| `thread_id_d` | ID för tråd som körde frågan |
| `host_s` | Blank |
| `ip_s` | IP-adress för klient som ansluter till MariaDB |
| `user_s` | Namn på användaren som kör frågan |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysera loggar i Azure Monitor Logs

När dina granskningsloggar har skickas till Azure Monitor Logs via diagnostikloggar kan du utföra ytterligare analyser av dina granskade händelser. Nedan följer några exempelfrågor som hjälper dig att komma igång. Se till att uppdatera nedanstående med ditt servernamn.

- Lista ALLMÄNNA händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Lista CONNECTION-händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Summera granskade händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Diagram över distributionen av granskningshändelsetyp på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Lista granskade händelser på alla MariaDB-servrar med diagnostikloggar aktiverade för granskningsloggar

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Nästa steg

- [Konfigurera granskningsloggar i Azure-portalen](howto-configure-audit-logs-portal.md)