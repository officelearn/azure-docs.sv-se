---
title: Granska loggformat
description: Förstå hur SQL Database gransknings loggar struktureras.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 389404f59b5a0cba1acd7aa097ddd3dd929d8082
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821998"
---
# <a name="sql-database-audit-log-format"></a>SQL Database Gransknings logg format

[Azure SQL Database granskning](sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg på ditt Azure Storage-konto eller skickar dem till händelsehubben eller Log Analytics för efterföljande bearbetning och analys.

## <a name="naming-conventions"></a>Namngivnings konventioner

### <a name="blob-audit"></a>BLOB Audit

Gransknings loggar lagrade i Blob Storage lagras i en behållare med namnet `sqldbauditlogs` i Azure Storage-kontot. Katalogpartitionen i behållaren har formatet `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. BLOB filename-formatet är `<CreationTime>_<FileNumberInSession>.xel`, där `CreationTime` är i UTC-`hh_mm_ss_ms`-format och `FileNumberInSession` är ett index som körs i fall där loggar loggar över flera BLOB-filer.

Exempel: för databas `Database1` på `Server1` följande är en möjlig giltig sökväg:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Händelsehubb

Gransknings händelser skrivs till namn området och händelsehubben som definierades vid gransknings konfigurationen och samlas in i bröd texten i [Apache Avro](https://avro.apache.org/) -händelser och lagras med JSON-FORMATERING med UTF-8-kodning. Om du vill läsa gransknings loggarna kan du använda [Avro-verktyg](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

### <a name="log-analytics"></a>Log Analytics

Gransknings händelser skrivs till Log Analytics arbets yta som definierats under gransknings konfigurationen till tabellen `AzureDiagnostics` med kategorin `SQLSecurityAuditEvents`. Mer värdefull information om Log Analytics sökspråk och-kommandon finns i [Log Analytics Sök referens](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Gransknings logg fält

| Namn (BLOB) | Namn (Event Hubs/Log Analytics) | Beskrivning | Blob-typ | Event Hubs/Log Analytics typ |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID för åtgärden | varchar (4) | sträng |
| action_name | action_name_s | Åtgärdens namn | Saknas | sträng |
| additional_information | additional_information_s | Eventuell ytterligare information om händelsen, lagrad som XML | nvarchar (4000) | sträng |
| affected_rows | affected_rows_d | Antalet rader som påverkas av frågan | bigint | int |
| application_name | application_name_s| Namn på klient program | nvarchar (128) | sträng |
| audit_schema_version | audit_schema_version_d | Alltid 1 | int | int |
| class_type | class_type_s | Typ av gransknings bar entitet som granskningen sker på | varchar (2) | sträng |
| class_type_desc | class_type_description_s | Beskrivning av gransknings bar entitet som granskningen sker på | Saknas | sträng |
| client_ip | client_ip_s | Käll-IP för klient programmet | nvarchar (128) | sträng |
| connection_id | Saknas | ID för anslutningen på servern | LED | Saknas |
| data_sensitivity_information | data_sensitivity_information_s | Informations typer och känslighets etiketter som returneras av den granskade frågan, baserat på de klassificerade kolumnerna i databasen. Läs mer om att [Azure SQL Database data identifiering och klassificering](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | sträng |
| database_name | database_name_s | Databas kontexten där åtgärden utfördes | sysname | sträng |
| database_principal_id | database_principal_id_d | ID för den databas användar kontext som åtgärden utförs i | int | int |
| database_principal_name | database_principal_name_s | Namnet på databasens användar kontext där åtgärden utförs | sysname | sträng |
| duration_milliseconds | duration_milliseconds_d | Varaktighet för körning av fråga i millisekunder | bigint | int |
| event_time | event_time_t | Datum och tid då den gransknings bara åtgärden utlöses | datetime2 | datetime |
| host_name | Saknas | Klient värd namn | sträng | Saknas |
| is_column_permission | is_column_permission_s | Flagga som anger om detta är en kolumn nivå behörighet. 1 = sant, 0 = falskt | bitmask | sträng |
| Saknas | is_server_level_audit_s | Flagga som anger om den här granskningen finns på server nivå | Saknas | sträng |
| object_-ID | object_id_d | ID för den entitet som granskningen utfördes på. Detta omfattar: Server objekt, databaser, databas objekt och schema objekt. 0 om entiteten är själva servern eller om granskningen inte utförs på en objekt nivå | int | int |
| object_name | object_name_s | Namnet på den entitet som granskningen utfördes på. Detta omfattar: Server objekt, databaser, databas objekt och schema objekt. 0 om entiteten är själva servern eller om granskningen inte utförs på en objekt nivå | sysname | sträng |
| permission_bitmask | permission_bitmask_s | När det är tillämpligt visar de behörigheter som beviljats, nekats eller återkallats | varbinary (16) | sträng |
| response_rows | response_rows_d | Antal rader som returneras i resultat uppsättningen | bigint | int |
| schema_name | schema_name_s | Schema kontexten där åtgärden utfördes. NULL för granskningar som inträffar utanför ett schema | sysname | sträng |
| Saknas | securable_class_type_s | Skydds bara objekt som mappar till class_type som granskas | Saknas | sträng |
| sequence_group_id | sequence_group_id_g | Unik identifierare | varbinary | LED |
| sequence_number | sequence_number_d | Spårar sekvensen av poster i en enskild gransknings post som var för stor för att få plats i insamlingsbufferten för granskningar | int | int |
| server_instance_name | server_instance_name_s | Namnet på Server instansen där granskningen utfördes | sysname | sträng |
| server_principal_id | server_principal_id_d | ID för inloggnings kontexten som åtgärden utförs i | int | int |
| server_principal_name | server_principal_name_s | Aktuell inloggning | sysname | sträng |
| server_principal_sid | server_principal_sid_s | Aktuellt inloggnings-SID | varbinary | sträng |
| session_id | session_id_d | ID för den session där händelsen inträffade | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Server huvud namn för session | sysname | sträng |
| Sekretesspolicy | statement_s | T-SQL-instruktion som kördes (om det finns) | nvarchar (4000) | sträng |
| brutit | succeeded_s | Anger om den åtgärd som utlöste händelsen lyckades. För andra händelser än inloggning och batch rapporteras endast om behörighets kontrollen lyckades eller misslyckades, inte åtgärden. 1 = lyckades, 0 = misslyckades | bitmask | sträng |
| target_database_principal_id | target_database_principal_id_d | Databasens huvud konto som åtgärden bevilja/neka/återkalla utförs på. 0 om inte tillämpligt | int | int |
| target_database_principal_name | target_database_principal_name_s | Mål användare av åtgärd. NULL om ej tillämpligt | sträng | sträng |
| target_server_principal_id | target_server_principal_id_d | Server huvud konto som åtgärden bevilja/neka/återkalla utförs på. Returnerar 0 om det inte är tillämpligt | int | int |
| target_server_principal_name | target_server_principal_name_s | Mål inloggning för åtgärd. NULL om ej tillämpligt | sysname | sträng |
| target_server_principal_sid | target_server_principal_sid_s | SID för mål inloggning. NULL om ej tillämpligt | varbinary | sträng |
| transaction_id | transaction_id_d | Endast SQL Server (från och med 2016) – 0 för Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Ett användardefinierat händelse-ID skickades som ett argument till sp_audit_write. NULL för system händelser (standard) och icke-noll för användardefinierad händelse. Mer information finns i [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Användardefinierad information som skickas som ett argument till sp_audit_write. NULL för system händelser (standard) och icke-noll för användardefinierad händelse. Mer information finns i [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | sträng |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure SQL Database granskning](sql-database-auditing.md).