---
title: Granska loggformat
description: Förstå hur GRANSKNINGSLOGGAR FÖR SQL Database är strukturerade.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722092"
---
# <a name="sql-database-audit-log-format"></a>Loggformat för SQL-databasgranskning

[Granskning av Azure SQL Database](sql-database-auditing.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto, eller skickar dem till Event Hub eller Log Analytics för nedströmsbearbetning och analys.

## <a name="naming-conventions"></a>Namngivning

### <a name="blob-audit"></a>Blob-granskning

Granskningsloggar som lagras i Blob-lagring lagras i en behållare som heter `sqldbauditlogs` i Azure Storage-kontot. Kataloghierarkin i behållaren är `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`av formuläret . Filnamnet Blob är `<CreationTime>_<FileNumberInSession>.xel`, `CreationTime` där är `hh_mm_ss_ms` i `FileNumberInSession` UTC-format, och är ett löpande index om sessionsloggar sträcker sig över flera Blob-filer.

För databasen `Database1` på `Server1` följande finns till exempel en möjlig giltig sökväg:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Skrivskyddade repliker](sql-database-read-scale-out.md) Granskningsloggar lagras i samma behållare. Kataloghierarkin i behållaren är `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`av formuläret . Filnamnet Blob delar samma format. Granskningsloggarna för skrivskyddade repliker lagras i samma behållare.


### <a name="event-hub"></a>Händelsehubb

Granskningshändelser skrivs till namnområdet och händelsehubben som definierades under granskningskonfigurationen och fångas in i brödtexten i [Apache Avro-händelser](https://avro.apache.org/) och lagras med JSON-formatering med UTF-8-kodning. Om du vill läsa granskningsloggarna kan du använda [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

### <a name="log-analytics"></a>Log Analytics

Granskningshändelser skrivs till Log Analytics arbetsyta som `AzureDiagnostics` definierats `SQLSecurityAuditEvents`under granskningskonfigurationen, till tabellen med kategorin . Mer användbar information om logganalyssökspråk och kommandon finns i [Sökreferens för Logganalys](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Fält för granskningslogg

| Namn (Blob) | Namn (händelsehubbar/logganalys) | Beskrivning | Blob-typ | Händelsehubbar/logganalystyp |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID för åtgärden | varchar(4) | sträng |
| action_name | action_name_s | Åtgärdens namn | Ej tillämpligt | sträng |
| additional_information | additional_information_s | Ytterligare information om händelsen, som lagras som XML | nvarchar(4000) | sträng |
| affected_rows | affected_rows_d | Antal rader som påverkas av frågan | bigint | int |
| application_name | application_name_s| Namn på klientprogram | nvarchar(128) | sträng |
| audit_schema_version | audit_schema_version_d | Alltid 1 | int | int |
| class_type | class_type_s | Typ av granskningsbar enhet som revisionen sker på | varchar(2) | sträng |
| class_type_desc | class_type_description_s | Beskrivning av den granskningsbara enhet som revisionen sker på | Ej tillämpligt | sträng |
| client_ip | client_ip_s | Käll-IP för klientprogrammet | nvarchar(128) | sträng |
| connection_id | Ej tillämpligt | ID för anslutningen på servern | GUID | Ej tillämpligt |
| data_sensitivity_information | data_sensitivity_information_s | Informationstyper och känslighetsetiketter som returneras av den granskade frågan, baserat på de klassificerade kolumnerna i databasen. Läs mer om [identifiering och klassificering av Azure SQL Database-data](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | sträng |
| database_name | database_name_s | Databaskontexten där åtgärden inträffade | sysname (olika) | sträng |
| database_principal_id | database_principal_id_d | ID för databasanvändarkontexten som åtgärden utförs i | int | int |
| database_principal_name | database_principal_name_s | Namn på databasanvändarkontexten där åtgärden utförs | sysname (olika) | sträng |
| duration_milliseconds | duration_milliseconds_d | Varaktighet för frågekörning i millisekunder | bigint | int |
| event_time | event_time_t | Datum och tid då den granskningsbara åtgärden utlöses | datetime2 | datetime |
| host_name | Ej tillämpligt | Klientvärdens namn | sträng | Ej tillämpligt |
| is_column_permission | is_column_permission_s | Flagga som anger om detta är en behörighet på kolumnnivå. 1 = sant, 0 = falskt | bit | sträng |
| Ej tillämpligt | is_server_level_audit_s | Flagga som anger om granskningen är på servernivå | Ej tillämpligt | sträng |
| object_ id | object_id_d | ID:et för den enhet som revisionen ägde rum på. Detta inkluderar : serverobjekt, databaser, databasobjekt och schemaobjekt. 0 om entiteten är själva servern eller om granskningen inte utförs på objektnivå | int | int |
| object_name | object_name_s | Namnet på den enhet där revisionen inträffade. Detta inkluderar : serverobjekt, databaser, databasobjekt och schemaobjekt. 0 om entiteten är själva servern eller om granskningen inte utförs på objektnivå | sysname (olika) | sträng |
| permission_bitmask | permission_bitmask_s | I förekommande fall visar de behörigheter som beviljats, nekats eller återkallats | varbinary(16) | sträng |
| response_rows | response_rows_d | Antal rader som returneras i resultatuppsättningen | bigint | int |
| schema_name | schema_name_s | Schemakontexten där åtgärden inträffade. NULL för granskningar som inträffar utanför ett schema | sysname (olika) | sträng |
| Ej tillämpligt | securable_class_type_s | Säkerhetsobjekt som mappar till class_type som granskas | Ej tillämpligt | sträng |
| sequence_group_id | sequence_group_id_g | Unik identifierare | varbinary | GUID |
| sequence_number | sequence_number_d | Spårar sekvensen av poster i en enskild granskningspost som var för stor för att få plats i skrivbufferten för granskningar | int | int |
| server_instance_name | server_instance_name_s | Namn på den serverinstans där granskningen inträffade | sysname (olika) | sträng |
| server_principal_id | server_principal_id_d | ID för inloggningskontexten där åtgärden utförs | int | int |
| server_principal_name | server_principal_name_s | Aktuell inloggning | sysname (olika) | sträng |
| server_principal_sid | server_principal_sid_s | Aktuell inloggning SID | varbinary | sträng |
| session_id | session_id_d | ID för den session där händelsen inträffade | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Serverns huvudnamn för session | sysname (olika) | sträng |
| Uttalande | statement_s | T-SQL-uttryck som utfördes (om något) | nvarchar(4000) | sträng |
| lyckades | succeeded_s | Anger om åtgärden som utlöste händelsen lyckades. För andra händelser än inloggning och batch rapporterar detta bara om behörighetskontrollen lyckades eller misslyckades, inte åtgärden. 1 = framgång, 0 = misslyckas | bit | sträng |
| target_database_principal_id | target_database_principal_id_d | Databasens huvudnamn som åtgärden GRANT/DENY/REVOKE utförs på. 0 om det inte är tillämpligt | int | int |
| target_database_principal_name | target_database_principal_name_s | Rikta in dig på åtgärden. NULL om det inte är tillämpligt | sträng | sträng |
| target_server_principal_id | target_server_principal_id_d | Serverhuvudnamn som åtgärden GRANT/DENY/REVOKE utförs på. Returnerar 0 om det inte är tillämpligt | int | int |
| target_server_principal_name | target_server_principal_name_s | Mål inloggning av åtgärder. NULL om det inte är tillämpligt | sysname (olika) | sträng |
| target_server_principal_sid | target_server_principal_sid_s | SID för målinloggning. NULL om det inte är tillämpligt | varbinary | sträng |
| transaction_id | transaction_id_d | Endast SQL Server (från och med 2016) – 0 för Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Användardefinierat händelse-ID skickades som ett argument för att sp_audit_write. NULL för systemhändelser (standard) och icke-noll för användardefinierad händelse. Mer information finns [i sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Användardefinierad information skickas som ett argument för att sp_audit_write. NULL för systemhändelser (standard) och icke-noll för användardefinierad händelse. Mer information finns [i sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | sträng |

## <a name="next-steps"></a>Efterföljande moment

Läs mer om [Granskning av Azure SQL Database](sql-database-auditing.md).
