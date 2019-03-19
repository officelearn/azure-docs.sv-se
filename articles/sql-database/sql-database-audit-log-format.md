---
title: Loggformatet för SQL Database-granskning | Microsoft Docs
description: Förstå hur granskningsloggar för SQL-databas är strukturerade.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001684"
---
# <a name="sql-database-audit-log-format"></a>Loggformatet för SQL Database-granskning

[Azure SQL Database-granskning](sql-database-auditing.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure storage-konto och skickar dem till Event Hub eller Log Analytics för nedströms bearbetning och analys.

## <a name="naming-conventions"></a>Namngivningskonventioner

### <a name="blob-audit"></a>Blobgranskning

Granskningsloggar lagras i Blob storage lagras i en behållare med namnet `sqldbauditlogs` i Azure Storage-kontot. Directory-hierarkin i behållaren är i formatet `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. Blobbformatet filename är `<CreationTime>_<FileNumberInSession>.xel`, där `CreationTime` är i UTC `hh_mm_ss_ms` format, och `FileNumberInSession` är en löpande indexera om sessionen loggar finns i flera Blob-filer.

Till exempel för databasen `Database1` på `Server1` följande är en giltig sökväg som möjligt:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Händelsehubb

Granskningshändelser skrivs till namnområde och en händelsehubb som har definierats under granskning konfigurationen och samlas i brödtexten i [Apache Avro](https://avro.apache.org/) händelser och lagras med JSON formatering med UTF-8-kodning. Du kan använda för att läsa granskningsloggarna [Avro verktyg](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

### <a name="log-analytics"></a>Log Analytics

Granskningshändelser skrivs till Log Analytics-arbetsyta som definierats under granskning konfigurationen till den `AzureDiagnostics` tabell i kategorin `SQLSecurityAuditEvents`. Ytterligare användbar information om Log Analytics-frågespråket och kommandon finns i [Log Analytics Sök referens](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Granska loggfält

| Namn (Blob) | Namn (Event Hubs/Log Analytics) | Beskrivning | Blobtyp | Event Hubs/Log Analytics-typ |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID för åtgärden | varchar(4) | sträng |
| action_name | action_name_s | Namnet på åtgärden | Gäller inte | sträng |
| additional_information | additional_information_s | Ytterligare information om händelsen, lagras som XML | nvarchar(4000) | sträng |
| affected_rows | affected_rows_d | Antalet rader som påverkas av frågan | bigint | int |
| Programnamn | application_name_s| Namnet på klientprogram | nvarchar(128) | sträng |
| audit_schema_version | audit_schema_version_d | Alltid 1 | int | int |
| class_type | class_type_s | Typ av granskningsbara entitet som granskningen inträffar på | varchar(2) | sträng |
| class_type_desc | class_type_description_s | Beskrivning av granskningsbara entitet som granskningen körs på | Gäller inte | sträng |
| client_ip | client_ip_s | Käll-IP-Adressen för klientprogrammet | nvarchar(128) | sträng |
| connection_id | Gäller inte | ID för anslutningen i servern | GUID | Gäller inte |
| data_sensitivity_information | data_sensitivity_information_s | Typer av information och känslighet etiketter som returneras av granskad frågan, baserat på klassificerade kolumner i databasen. Läs mer om [identifiera data för Azure SQL Database och klassificering](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | sträng |
| databasnamn | database_name_s | Databasens kontext där åtgärden utfördes | sysname | sträng |
| database_principal_id | database_principal_id_d | ID för databasanvändarsammanhang som åtgärden utförs i | int | int |
| database_principal_name | database_principal_name_s | Namnet på databasanvändarsammanhang där åtgärden utförs | sysname | sträng |
| duration_milliseconds | duration_milliseconds_d | Varaktighet för körning av frågan i millisekunder | bigint | int |
| event_time | event_time_t | Datum och tid när instruktionen granskningsbara utlöses | datetime2 | datetime |
| host_name | Gäller inte | Värdnamn för klienten | sträng | Gäller inte |
| is_column_permission | is_column_permission_s | Flagga som anger om det här är en kolumn på behörighet. 1 = true, 0 = false | bitars | sträng |
| Gäller inte | is_server_level_audit_s | Flagga som anger om den här granskning på servernivå | Gäller inte | sträng |
| object_-id | object_id_d | ID för den entitet som då granskningen inträffade. Detta omfattar de: server-objekt, databaser, databasobjekt och schemaobjekt. 0 utförs om entiteten är själva servern eller om granskningen inte är på objektnivå | int | int |
| object_name | object_name_s | Namnet på entiteten då granskningen inträffade. Detta omfattar de: server-objekt, databaser, databasobjekt och schemaobjekt. 0 utförs om entiteten är själva servern eller om granskningen inte är på objektnivå | sysname | sträng |
| permission_bitmask | permission_bitmask_s | När så är tillämpligt, visar de behörigheter som beviljas nekas eller återkallats | varbinary(16) | sträng |
| response_rows | response_rows_d | Antalet rader som returneras i resultatet | bigint | int |
| schema_name | schema_name_s | Schemat kontexten där åtgärden utfördes. NULL för granskningar som uppstår utanför ett schema | sysname | sträng |
| Gäller inte | securable_class_type_s | Skyddsbara objekt som mappar till class_type granskas | Gäller inte | sträng |
| sequence_group_id | sequence_group_id_g | Unik identifierare | varbinary | GUID |
| sequence_number | sequence_number_d | Spårar sekvens med poster inom en enda granskningspost som var för stor för att få plats i skrivbuffert för granskningar | int | int |
| server_instance_name | server_instance_name_s | Namnet på den server-instansen där granskningen inträffade | sysname | sträng |
| server_principal_id | server_principal_id_d | ID för inloggningskontext där åtgärden utförs | int | int |
| server_principal_name | server_principal_name_s | Aktuella inloggningen | sysname | sträng |
| server_principal_sid | server_principal_sid_s | Aktuella inloggningen SID | varbinary | sträng |
| session_id | session_id_d | ID för den session där händelsen inträffade | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Tjänstens huvudnamn för session | sysname | sträng |
| Instruktionen | statement_s | T-SQL-instruktionen som har gjorts (i förekommande fall) | nvarchar(4000) | sträng |
| Lyckades | succeeded_s | Anger om åtgärden som utlöste händelsen har slutförts. För händelser än inloggning och batch rapporterar detta endast om Kontrollera behörighet har lyckats eller misslyckats, inte igen. 1 = lyckat, 0 = misslyckas | bitars | sträng |
| target_database_principal_id | target_database_principal_id_d | Databashuvudobjektet DENY-GRANT/REVOKE-åtgärden utförs på. 0 om ej tillämpligt | int | int |
| target_database_principal_name | target_database_principal_name_s | Målanvändaren för åtgärden. NULL om ej tillämpligt | sträng | sträng |
| target_server_principal_id | target_server_principal_id_d | Tjänstens huvudnamn som DENY-GRANT/REVOKE-åtgärden utförs på. Returnerar 0 om ej tillämpligt | int | int |
| target_server_principal_name | target_server_principal_name_s | Mål-inloggningen för åtgärden. NULL om ej tillämpligt | sysname | sträng |
| target_server_principal_sid | target_server_principal_sid_s | SID för mål-inloggning. NULL om ej tillämpligt | varbinary | sträng |
| transaction_id | transaction_id_d | Endast SQL Server (från och med 2016) - 0 för Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Användardefinierad händelse-id skickas som ett argument för sp_audit_write. NULL för systemhändelser (standard) och inte är noll för en användardefinierad händelse. Mer information finns i [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Användardefinierad information som skickas som ett argument för sp_audit_write. NULL för systemhändelser (standard) och inte är noll för en användardefinierad händelse. Mer information finns i [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | sträng |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure SQL Database-granskning](sql-database-auditing.md).