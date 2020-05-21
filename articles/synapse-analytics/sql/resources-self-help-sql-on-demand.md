---
title: För hands version av SQL på begäran) – självhjälp
description: Det här avsnittet innehåller information som kan hjälpa dig att felsöka problem med SQL på begäran (för hands version).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8b2a9b6c5324240d71a80cde904057757d6ef421
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658874"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Själv hjälp för SQL på begäran (för hands version)

Den här artikeln innehåller information om hur du felsöker vanliga problem med SQL på begäran (för hands version) i Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL på begäran är nedtonat i Synapse Studio

Om Synapse Studio inte kan upprätta en anslutning till SQL på begäran, ser du att SQL på begäran är nedtonat eller visar status "offline". Det här problemet uppstår vanligt vis när något av följande inträffar:

1) Nätverket förhindrar kommunikation till Azure Synapse-backend. Det vanligaste fallet är att port 1443 är blockerad. För att hämta SQL på begäran för att ta bort blockeringen av porten. Andra problem kan förhindra att SQL på begäran fungerar också, finns i [fullständig fel söknings guide för mer information](../troubleshoot/troubleshoot-synapse-studio.md).
2) Du har inte behörighet att logga in på SQL på begäran. För att få åtkomst måste en av Azure Synapse-arbetsytans administratörer lägga till dig i arbets ytans administratör eller rollen SQL-administratör. [Mer information finns i fullständig guide om åtkomst kontroll](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Frågan Miss lyckas eftersom det inte går att öppna filen

Om frågan Miss lyckas med felet "det går inte att öppna filen eftersom den inte finns eller om den används av en annan process och du är säker på att både filen finns och att den inte används av någon annan process betyder det att SQL på begäran inte kan komma åt filen. Det här problemet uppstår vanligt vis eftersom din Azure Active Directory identitet inte har behörighet att komma åt filen. Som standard försöker SQL på begäran att komma åt filen med din Azure Active Directory identitet. För att lösa det här problemet måste du ha rätt behörighet för att komma åt filen. Det enklaste sättet är att ge dig själv rollen ”Storage Blob Data Contributor” för det lagringskonto som du försöker köra frågan mot. [Mer information finns i den fullständiga guiden till åtkomstkontroll för lagring i Azure Active Directory](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Frågan kan inte utföras eftersom den inte kan köras på grund av aktuella resurs begränsningar 

Om frågan Miss lyckas med fel meddelandet "det går inte att köra den här frågan på grund av aktuella resurs begränsningar", innebär det att SQL på begäran inte kan köra den just nu på grund av resurs begränsningar: 

- Se till att du använder datatyper av rimlig storlek. Ange också schemat för Parquet-filerna för strängkolumner eftersom de är VARCHAR(8000) som standard. 

- Om din fråga är CSV-filer bör du överväga att [skapa statistik](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Gå till [metod tips för prestanda för SQL på begäran](best-practices-sql-on-demand.md) för att optimera frågan.  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE ' STATEMENT ' stöds inte i Master-databasen

Om frågan Miss lyckas med fel meddelandet:

> Det gick inte att köra frågan. Fel: det finns inte stöd för att skapa en extern tabell/DATA källa/DATABASens begränsade AUTENTISERINGSUPPGIFTER/fil FORMAT i Master-databasen. 

Det innebär att huvud databasen i SQL på begäran inte stöder generering av:
  - Externa tabeller
  - Externa data källor
  - Autentiseringsuppgifter för databasens omfång
  - Externa fil format

Lösning:

  1. Skapa en användar databas:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Kör Create-instruktionen i kontexten för <DATABASE_NAME> som misslyckades tidigare för Master-databasen. 
  
  Exempel på hur du skapar externt fil format:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer om hur du använder SQL på begäran:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)

- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)

- [Fråga efter vissa filer](query-specific-files.md)

- [Efterfråga Parquet-filer](query-parquet-files.md)

- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)

- [Efterfråga JSON-filer](query-json-files.md)

- [Skapa och använda vyer](create-use-views.md)

- [Skapa och använda externa tabeller](create-use-external-tables.md)

- [Spara frågeresultat till lagringsplats](create-external-table-as-select.md)
