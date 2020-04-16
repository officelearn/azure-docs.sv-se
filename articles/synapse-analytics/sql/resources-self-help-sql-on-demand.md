---
title: Självhjälp för SQL på begäran)
description: Det här avsnittet innehåller information som kan hjälpa dig att felsöka problem med SQL on-demand (preview).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424834"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Självhjälp för SQL på begäran (förhandsgranskning)

Den här artikeln innehåller information om hur du felsöker de vanligaste problemen med SQL on-demand (preview) i Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand är nedtonad i Synapse Studio

Om Synapse Studio inte kan upprätta anslutning till SQL på begäran, kommer du att märka att SQL on-demand är nedtonad eller visar status "Offline". Vanligtvis uppstår det här problemet när något av följande fall inträffar:

1) Nätverket förhindrar kommunikation till Azure Synapse-serverning. Vanligast är att port 1443 är blockerad. För att få SQL on-demand att arbeta låsa upp den här porten. Andra problem kan förhindra SQL on-demand att fungera också, [besök fullständig felsökningsguide för mer information](../troubleshoot/troubleshoot-synapse-studio.md).
2) Du har inte behörighet att logga in på SQL på begäran. För att få åtkomst bör en av Azure Synapse-arbetsyteadministratörerna lägga till dig i arbetsyteadministratören eller SQL-administratörsrollen. [Besök hela guiden om åtkomstkontroll för mer information](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Frågan misslyckas eftersom filen inte kan öppnas

Om frågan misslyckas med felet "Filen kan inte öppnas eftersom den inte finns eller den används av en annan process" och du är säker på att båda filen finns och den inte används av en annan process betyder det att SQL on-demand inte kan komma åt filen. Det här problemet uppstår vanligtvis eftersom din Azure Active Directory-identitet inte har behörighet att komma åt filen. Som standard försöker SQL on-demand komma åt filen med din Azure Active Directory-identitet. För att lösa problemet måste du ha rätt behörighet att komma åt filen. Det enklaste sättet är att ge dig själv rollen Storage Blob Data Contributor på det lagringskonto du försöker fråga. [Besök fullständig guide om Azure Active Directory-åtkomstkontroll för lagring för mer information](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Frågan misslyckas eftersom den inte kan köras på grund av aktuella resursbegränsningar 

Om frågan misslyckas med felmeddelandet "Den här frågan kan inte köras på grund av aktuella resursbegränsningar", betyder det att SQL OD inte kan köra den just nu på grund av resursbegränsningar: 

- Se till att datatyper av rimliga storlekar används. Ange också schema för parquet-filer för strängkolumner eftersom de som standard ska vara VARCHAR(8000). 

- Om frågan är inriktad på CSV-filer kan du [överväga att skapa statistik](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Gå till [metodtips för prestanda för SQL on-demand för](best-practices-sql-on-demand.md) att optimera frågan.  

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer om hur du använder SQL på begäran:

- [Fråga en enda CSV-fil](query-single-csv-file.md)

- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)

- [Fråga specifika filer](query-specific-files.md)

- [Fråga parquet-filer](query-parquet-files.md)

- [Frågor Parkett kapslade typer](query-parquet-nested-types.md)

- [Fråga JSON-filer](query-json-files.md)

- [Skapa och använda vyer](create-use-views.md)

- [Skapa och använda externa tabeller](create-use-external-tables.md)

- [Lagra frågeresultat till lagring](create-external-table-as-select.md)
