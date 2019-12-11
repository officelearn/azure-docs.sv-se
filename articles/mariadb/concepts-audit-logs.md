---
title: Gransknings loggar – Azure Database for MariaDB
description: Beskriver de gransknings loggar som är tillgängliga i Azure Database for MariaDB och de tillgängliga parametrarna för att aktivera loggnings nivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 9c5f6aa2900570aa00ddbc50ec8be4dbb0d16a34
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978057"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Gransknings loggar i Azure Database for MariaDB

I Azure Database for MariaDB är gransknings loggen tillgänglig för användare. Gransknings loggen kan användas för att spåra aktivitet på databas nivå och används ofta för efterlevnad.

> [!IMPORTANT]
> Gransknings logg funktionen är för närvarande en för hands version.

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

Som standard är gransknings loggen inaktive rad. Ange `audit_log_enabled` till på för att aktivera det.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedanstående tabell för vissa gransknings händelser.
- `audit_log_include_users`: MariaDB-användare ska inkluderas för loggning. Standardvärdet för den här parametern är tomt, som innehåller alla användare som ska loggas. Detta har högre prioritet jämfört med `audit_log_exclude_users`. Parameterns max längd är 512 tecken.
> [!Note]
> `audit_log_include_users` har högre prioritet än `audit_log_exclude_users`. Om till exempel `audit_log_include_users` = `demouser` och `audit_log_exclude_users` = `demouser`, tas användaren med i gransknings loggarna eftersom `audit_log_include_users` har högre prioritet.
- `audit_log_exclude_users`: MariaDB-användare undantas från loggning. Tillåter för högst fyra användare. Parameterns max längd är 256 tecken.

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

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Gransknings loggar är integrerade med Azure Monitor diagnostikloggar. När du har aktiverat gransknings loggar på MariaDB-servern kan du generera dem till Azure Monitor loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure Portal finns i [artikeln Gransknings logg Portal](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnostiska loggar scheman

I följande avsnitt beskrivs vad som är utdata från MariaDB gransknings loggar baserat på händelse typ. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unikt anslutnings-ID genererat av MariaDB |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MariaDB |
| `user_s` | Namn på användaren som kör frågan |
| `db_s` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för händelse typerna allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Frågans start-sekunder i UNIX-tidsstämpel |
| `error_code_d` | Felkod om frågan misslyckades. `0` innebär inget fel |
| `thread_id_d` | ID för tråd som körde frågan |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MariaDB |
| `user_s` | Namn på användaren som kör frågan |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

### <a name="table-access"></a>Tabell åtkomst

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` eller `DELETE` |
| `connection_id_d` | Unikt anslutnings-ID genererat av MariaDB |
| `db_s` | Namnet på databasen som används |
| `table_s` | Namnet på tabellen har öppnats |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du gransknings loggar i Azure Portal](howto-configure-audit-logs-portal.md)