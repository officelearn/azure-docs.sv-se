---
title: Skillnader i T-SQL-funktioner i Azure Synapse SQL
description: Lista över Transact-SQL-funktioner som är tillgängliga i Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424897"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-funktioner som stöds i Azure Synapse SQL

Azure Synapse SQL är en stordata analytisk tjänst som gör att du kan fråga och analysera dina data med hjälp av T-SQL-språket. Du kan använda standard-ANSI-kompatibel dialekt av SQL-språk som används på SQL Server och Azure SQL Database för dataanalys. 

Transact-SQL-språk används i Synapse SQL serverless och etablerad modell kan referera till olika objekt och har vissa skillnader i uppsättningen funktioner som stöds. På den här sidan kan du hitta skillnader i transact-SQL-språk på hög nivå mellan förbrukningsmodeller av Synapse SQL.

## <a name="database-objects"></a>Databasobjekt

Förbrukningsmodeller i Synapse SQL gör att du kan använda olika databasobjekt. Jämförelsen av objekttyper som stöds visas i följande tabell:

|   | Avstämmt | Utan server |
| --- | --- | --- |
| Tabeller | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej, serverlös modell kan endast fråga externa data som placeras på [Azure Storage](#storage-options) |
| Vyer | [- Ja,](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)det är jag. Vyer kan använda [frågespråkelement](#query-language) som är tillgängliga i etablerad modell. | [- Ja,](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)det är jag. Vyer kan använda [frågespråkelement](#query-language) som är tillgängliga i serverlös modell. |
| Scheman | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Temporära tabeller | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Inga |
| Procedurer | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| Functions | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| Utlösare | Inga | Inga |
| Externa tabeller | [- Ja,](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)det är jag. Se [dataformat](#data-formats)som stöds . | [- Ja,](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)det är jag. Se [dataformat](#data-formats)som stöds . |
| Cachelagringsfrågor | Ja, flera formulär (SSD-baserad cachelagring, i minnet, resultatuppsättningscachelagring). Dessutom stöds materialiserad vy | Inga |
| Tabellvariabler | [Nej](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), använd temporära tabeller | Inga |

## <a name="query-language"></a>Frågespråk

Frågespråk som används i Synapse SQL kan ha olika funktioner som stöds beroende på förbrukningsmodell. I följande tabell beskrivs de viktigaste frågespråksskillnaderna i Transact-SQL-dialekter:

|   | Avstämmt | Utan server |
| --- | --- | --- |
| SELECT-sats | Ja. Transact-SQL-frågesatser [för XML/FOR JSON,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) stöds inte. | Ja. Transact-SQL-frågesatser [för XML,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och frågetips stöds inte. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) och [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kan endast användas för att fråga data i temporära tabeller (inte externa data). |
| INSERT-sats | Ja | Inga |
| UPDATE-sats | Ja | Inga |
| DELETE-sats | Ja | Inga |
| KOPPLA sats | Ja | Inga |
| Datainläsning | Ja. Prioriterat verktyg är [COPY-sats,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) men systemet stöder både BULK load (BCP) och [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för datainläsning. | Inga |
| Dataexport | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Typer | Ja, alla Transact-SQL-typer utom [markör](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarki](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text och bild](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatialtyper](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL-variant\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla Transact-SQL-typer utom [markör,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierarki](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text och bild](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatialtyper](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL-variant\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och Tabelltyp |
| Frågor över flera databaser | Inga | Ja, inklusive [USE-sats.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Inbyggda funktioner (analys) | Ja, alla Transact-SQL [Analytic,](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Konvertering, [Datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Logiska, [Matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktioner, utom [VÄLJ,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla transact-SQL [analytiska,](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)konvertering, [datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiska, [matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktioner. |
| Inbyggda funktioner (text) | Ja. Alla funktioner Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och Collation, utom [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alla funktioner i Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och Collation. |
| Inbyggda tabellvärdefunktioner | Ja, [Transact-SQL-raduppsättningsfunktioner](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), utom [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE,](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, [Transact-SQL-raduppsättningsfunktioner](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), utom [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Samlingar |  Transact-SQL inbyggda aggregat utom, utom [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Transact-SQL inbyggda aggregat utom [\_STRING AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operatorer | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utom [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Kontroll av flödet | Ja. Alla [Transact-SQL Control-of-flow-uttryck](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utom [CONTINUE,](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alla [Transact-SQL Control-of-flow-uttryck](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utom `WHILE (...)` [RETUR-](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och SELECT-fråga i villkor |
| DDL-satser (SKAPA, ALTER, DROP) | Ja. Alla Transact-SQL DDL-uttryck som gäller för objekttyperna som stöds | Ja. Alla Transact-SQL DDL-uttryck som gäller för objekttyperna som stöds |

## <a name="security"></a>Säkerhet

Med Synapse SQL kan du använda inbyggda säkerhetsfunktioner för att skydda dina data och kontrollera åtkomsten. I följande tabell jämförs skillnader på hög nivå mellan Synapse SQL-förbrukningsmodeller.

|   | Avstämmt | Utan server |
| --- | --- | --- |
| Inloggningar | Saknas (endast inneslutna användare stöds i databaser) | Ja |
| Användare |  Saknas (endast inneslutna användare stöds i databaser) | Ja |
| [Inneslutna användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. **Obs:** endast en AAD-användare kan vara obegränsad admin | Ja |
| AAD-autentisering (Azure Active Directory) | Ja, AAD-användare | Ja, AAD-inloggningar och användare |
| Lagring AAD-vidareströmsautentisering | Ja | Ja |
| Lagring SAS-tokenautentisering | Inga | Ja, med hjälp av [databasscopedautentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [extern datakälla](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) eller [autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)på instansnivå . |
| Autentisering av lagringsåtkomstnyckel | Ja, med hjälp av [databasscopedautentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [extern datakälla](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| Autentisering av hanterad lagringsidentitet | Ja, med hjälp av [autentiseringsuppgifter för hanterad tjänstidentitet](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, `Managed Identity` med hjälp av autentiseringsuppgifter. |
| Identitetsautentisering för lagringsprogram | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| Behörigheter - Objektnivå | Ja, inklusive möjligheten att bevilja, neka och återkalla behörigheter till användare | Ja, inklusive möjligheten att bevilja, neka och återkalla behörigheter till användare/inloggningar på systemobjekt som stöds |
| Behörigheter - Schemanivå | Ja, inklusive möjligheten att bevilja, neka och återkalla behörigheter till användare/inloggningar i schemat | Ja, inklusive möjligheten att bevilja, neka och återkalla behörigheter till användare/inloggningar i schemat |
| Behörigheter - [Databasnivå](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja | Ja |
| Behörigheter - [Server-nivå](/sql/relational-databases/security/authentication-access/server-level-roles) | Inga | Ja, sysadmin och andra serverroller stöds |
| Roller/grupper | Ja (databasomfång) | Ja (både server- och databasscope) |
| Funktioner &amp; för säkerhetsidentitet | Vissa transact-SQL-säkerhetsfunktioner och `CURRENT_USER` `HAS_DBACCESS`-operatörer: , `SUSER_SNAME` `SYSTEM_USER`, `USER` `IS_MEMBER` `USER_NAME`, `EXECUTE AS` `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, , , , , , , , , , , , , ,`OPEN/CLOSE MASTER KEY` | Vissa transact-SQL-säkerhetsfunktioner och `CURRENT_USER` `HAS_DBACCESS`-operatörer: , `SUSER_NAME` `SUSER_SNAME`, `SYSTEM_USER` `HAS_PERMS_BY_NAME` `USER`, `USER_NAME` `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, , , , , , `EXECUTE AS`, och `REVERT`. Säkerhetsfunktioner kan inte användas för att fråga externa data (lagra resultatet i variabeln som kan användas i frågan).  |
| DATABASSCOPED-AUTENTISERINGSUPPGIFTER | Ja | Ja |

SQL-pool och SQL on-demand använder standard Transact-SQL-språk för att fråga data. Detaljerade skillnader finns i [språkreferensen Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Verktyg

Du kan använda olika verktyg för att ansluta till Synapse SQL för att fråga data.

|   | Avstämmt | Utan server |
| --- | --- | --- |
| Synapse Studio | Ja, SQL-skript | Ja, SQL-skript |
| Power BI | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| Azure-analystjänst | Ja | Ja |
| Azure Data Studio | Ja | Ja, version 1.14 eller senare. SQL-skript och SQL-anteckningsböcker stöds. |
| SQL Server Management Studio | Ja | Ja, version 18.4 eller senare |

De flesta av programmen använder standard Transact-SQL-språk kan fråga både etablerade och serverlösa förbrukningsmodeller av Synapse SQL.

## <a name="storage-options"></a>Lagringsalternativ

Data som analyseras kan lagras på olika lagringstyper. I följande tabell visas alla tillgängliga lagringsalternativ:

|   | Avstämmt | Utan server |
| --- | --- | --- |
| Intern lagring | Ja | Inga |
| Azure Data Lake v2 | Ja | Ja |
| Azure Blob Storage | Ja | Ja |

## <a name="data-formats"></a>Dataformat

Data som analyseras kan lagras i olika lagringsformat. I följande tabell visas alla tillgängliga dataformat som kan analyseras:

|   | Avstämmt | Utan server |
| --- | --- | --- |
| Avgränsade | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-single-csv-file.md) |
| CSV | Ja (avgränsningstecken med flera tecken stöds inte) | [Ja](query-single-csv-file.md) |
| Parkett | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-parquet-files.md), inklusive filer med [kapslade typer](query-parquet-nested-types.md) |
| Hive ORC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| Hive RC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inga |
| JSON | Ja | [Ja](query-json-files.md) |
| [Delta-sjö](https://delta.io/) | Inga | Inga |
| [Cdm](https://docs.microsoft.com/common-data-model/) | Inga | Inga |

## <a name="next-steps"></a>Nästa steg
Ytterligare information om metodtips för SQL-pool och SQL på begäran finns i följande artiklar:

- [Metodtips för SQL-pool](best-practices-sql-pool.md)
- [Metodtips för SQL på begäran](best-practices-sql-on-demand.md)