---
title: Skillnader mellan T-SQL-funktioner i Azure Synapse SQL
description: Lista över Transact-SQL-funktioner som är tillgängliga i Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: f2ee66522df3a149f9f5d4b8e2cd80cf956b84e3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197616"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-funktioner som stöds i Azure Synapse SQL

Azure Synapse SQL är en stor data analys tjänst som gör det möjligt att fråga och analysera dina data med hjälp av T-SQL-språket. Du kan använda ANSI-kompatibel dialekt av SQL-språk som används på SQL Server och Azure SQL Database för data analys. 

Transact-SQL-språket används i Synapse SQL Server lös och etablerad modell kan referera till olika objekt och har vissa skillnader i uppsättningen med funktioner som stöds. På den här sidan kan du hitta skillnader i Transact-SQL på hög nivå mellan förbruknings modeller av Synapse SQL.

## <a name="database-objects"></a>Databas objekt

Med förbruknings modeller i Synapse SQL kan du använda olika databas objekt. Jämförelsen av objekt typer som stöds visas i följande tabell:

|   | Etablerats | Utan server |
| --- | --- | --- |
| Tabeller | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej, Server lös modell kan bara fråga på externa data som placeras på [Azure Storage](#storage-options) |
| Vyer | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vyer kan använda [språkvariant-element](#query-language) som är tillgängliga i en etablerad modell. | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Vyer kan använda [frågespråket](#query-language) som är tillgängliga i en server lös modell. |
| Scheman | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Temporära tabeller | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nej |
| Procedurer | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| Functions | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| Utlösare | Nej | Nej |
| Externa tabeller | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Se [data format](#data-formats)som stöds. | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Se [data format](#data-formats)som stöds. |
| Cachelagra frågor | Ja, flera formulär (SSD-baserad cachelagring, minnes intern cachelagring, ResultSet). Dessutom stöds materialiserad vy | Nej |
| Table-variabler | [Nej](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Använd temporära tabeller | Nej |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabelldistribution)                 | Ja | Nej |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Tabellindex)                           | Ja | Nej |
| [Table-partitioner](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Nej |
| [Statistik](develop-tables-statistics.md)            | Ja | Ja |
| [Resurs klasser och samtidighet](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Nej |

## <a name="query-language"></a>Frågespråk

Frågespråket som används i Synapse SQL kan ha olika funktioner som stöds beroende på förbruknings modellen. Följande tabell beskriver de viktigaste skillnaderna i frågespråket i Transact-SQL-dialekter:

|   | Etablerats | Utan server |
| --- | --- | --- |
| SELECT-uttryck | Ja. Transact-SQL-frågeuttryck [för XML/för JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [match](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [predict](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) stöds inte. | Ja. Transact-SQL-frågeuttryck [för XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [match](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [predict](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-och frågetipset-tips stöds inte. [Offset/Fetch](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) och [PIVOT/unpivot](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kan bara användas för att fråga efter data i temporära tabeller (inte externa data). |
| Infoga instruktion | Ja | Nej |
| UPDATE-instruktion | Ja | Nej |
| TA bort instruktion | Ja | Nej |
| MERGE-instruktion | Ja | Nej |
| [Transaktioner](develop-transactions.md) | Ja | Nej |
| [Etiketter](develop-label.md) | Ja | Nej |
| Data inläsning | Ja. Det rekommenderade verktyget är [copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -instruktion, men systemet stöder både BCP (Mass inläsning) och [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för data inläsning. | Nej |
| Dataexport | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Ja. Använda [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Typer | Ja, alla Transact-SQL-typer förutom [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text och image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [spatial typer](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL- \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla Transact-SQL-typer förutom [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text och image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [spatial typer](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL \_ variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och tabell typ |
| Frågor över flera databaser | Nej | Ja, inklusive [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -instruktionen. |
| Inbyggda funktioner (analys) | Ja, alla [analytiska](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), konverteringar, [datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiska, [matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktioner i Transact-SQL, förutom [välja](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OOM](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [parsa](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla [analytiska](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)konverteringar, konvertering, [datum och tid](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiska, [matematiska](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktioner i Transact-SQL. |
| Inbyggda funktioner (text) | Ja. Alla funktioner för Transact-SQL- [sträng](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och-sortering, förutom [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [Översätt](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alla funktioner för Transact-SQL- [sträng](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och-sortering. |
| Inbyggda tabell värdes funktioner | Ja, [Transact-SQL rowset-funktioner](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), förutom [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, [Transact-SQL rowset-funktioner](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), förutom [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Samlingar |  Inbyggda Transact-SQL-mängder, förutom [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Inbyggda Transact-SQL-mängder utom String- [ \_ aggregering](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operatorer | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utom [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alla [Transact-SQL-operatorer](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Kontroll av flöde | Ja. Alla [Transact-SQL Control-of-Flow-uttryck](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utom [Fortsätt](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [gå](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)till, [returnera](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [använda](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)och [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alla [Transact-SQL Control-of-Flow-instruktioner](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) förutom [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -och Select-frågor i `WHILE (...)` villkor |
| DDL-instruktioner (skapa, ändra, släpp) | Ja. Alla Transact-SQL DDL-uttryck som gäller för de objekt typer som stöds | Ja. Alla Transact-SQL DDL-uttryck som gäller för de objekt typer som stöds |

## <a name="security"></a>Säkerhet

Synapse SQL gör att du kan använda inbyggda säkerhetsfunktioner för att skydda dina data och kontrol lera åtkomst. I följande tabell jämförs skillnader i hög nivå mellan Synapse-förbruknings modeller i SQL.

|   | Etablerats | Utan server |
| --- | --- | --- |
| Inloggningar | Ej tillämpligt (endast inneslutna användare stöds i databaser) | Ja |
| Användare |  Ej tillämpligt (endast inneslutna användare stöds i databaser) | Ja |
| [Inneslutna användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. **Obs:** endast en AAD-användare kan vara obegränsad administratör | Ja |
| Azure Active Directory autentisering (AAD) | Ja, AAD-användare | Ja, AAD-inloggningar och användare |
| AAD-autentisering genom att lagra | Ja | Ja |
| Autentisering av SAS-token för lagring | Nej | Ja, Använd [databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i den [externa data källan](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) eller på instans nivåns [autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Autentisering av lagrings åtkomst nyckel | Ja, Använd [databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i den [externa data källan](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| Autentisering med hanterad identitet för lagring | Ja, med [hanterad tjänstidentitet autentiseringsuppgift](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, med hjälp av `Managed Identity` autentiseringsuppgifter. |
| Autentisering av lagrings program identitet | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| Behörigheter-objekt nivå | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar på de system objekt som stöds |
| Behörigheter – schema nivå | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar i schemat | Ja, inklusive möjlighet att bevilja, neka och återkalla behörigheter för användare/inloggningar i schemat |
| Behörigheter – [databas nivå](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja | Ja |
| Behörigheter- [Server nivå](/sql/relational-databases/security/authentication-access/server-level-roles) | Nej | Ja, sysadmin och andra Server roller stöds |
| Roller/grupper | Ja (databas omfattning) | Ja (både server-och databas omfattning) |
| Funktioner för säkerhets &amp; identitet | Vissa säkerhets funktioner och operatorer för Transact-SQL:,,,,,,,, `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` , `EXECUTE AS` ,`OPEN/CLOSE MASTER KEY` | Vissa Transact-SQL-säkerhetsfunktioner och-operatorer:,,,,,,,,, `CURRENT_USER` `HAS_DBACCESS` `HAS_PERMS_BY_NAME` ,, `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` och `REVERT` . Säkerhets funktioner kan inte användas för att fråga externa data (lagra resultatet i variabeln som kan användas i frågan).  |
| DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER | Ja | Ja |

SQL-pool och SQL på begäran använder standard Transact-SQL-språk för att fråga data. För detaljerade skillnader, se [språk referens för Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Verktyg

Du kan använda olika verktyg för att ansluta till Synapse SQL för att fråga efter data.

|   | Etablerats | Utan server |
| --- | --- | --- |
| Synapse Studio | Ja, SQL-skript | Ja, SQL-skript |
| Power BI | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| Azure Analysis Service | Ja | Ja |
| Azure Data Studio | Ja | Ja, version 1.18.0 eller högre. SQL-skript och SQL-anteckningsböcker stöds. |
| SQL Server Management Studio | Ja | Ja, version 18,5 eller senare |

> [!NOTE]
> Du kan använda SSMS för att ansluta till SQL på begäran (för hands version) och en fråga. Den stöds delvis från och med version 18,5. du kan bara använda den för att ansluta och fråga.

De flesta av programmen använder standard Transact-SQL-språk och kan fråga både etablerade och serverbaserade förbruknings modeller i Synapse SQL.

## <a name="storage-options"></a>Lagringsalternativ

Data som analyseras kan lagras på olika lagrings typer. I följande tabell visas alla tillgängliga lagrings alternativ:

|   | Etablerats | Utan server |
| --- | --- | --- |
| Intern lagring | Ja | Nej |
| Azure Data Lake v2 | Ja | Ja |
| Azure Blob Storage | Ja | Ja |

## <a name="data-formats"></a>Data format

Data som analyseras kan lagras i olika lagrings format. I följande tabell visas alla tillgängliga data format som kan analyseras:

|   | Etablerats | Utan server |
| --- | --- | --- |
| Avgränsade | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-single-csv-file.md) |
| CSV | Ja (avgränsare för flera tecken stöds inte) | [Ja](query-single-csv-file.md) |
| Parquet | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-parquet-files.md), inklusive filer med [kapslade typer](query-parquet-nested-types.md) |
| Hive-ORC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| Hive RC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nej |
| JSON | Ja | [Ja](query-json-files.md) |
| [Delta – sjö](https://delta.io/) | Nej | Nej |
| [COMMON data service](https://docs.microsoft.com/common-data-model/) | Nej | Nej |

## <a name="next-steps"></a>Nästa steg
Mer information om metod tips för SQL-poolen och SQL på begäran finns i följande artiklar:

- [Metod tips för SQL-pool](best-practices-sql-pool.md)
- [Metod tips för SQL på begäran](best-practices-sql-on-demand.md)
