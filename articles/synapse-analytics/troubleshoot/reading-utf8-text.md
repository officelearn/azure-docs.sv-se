---
title: 'Fel sökning: läsa av UTF-8-text från CSV-eller PARQUET-filer med Server lös SQL-pool'
description: Läsning av UTF-8-text från CSV-eller PARQUET-filer med Server lös SQL-pool i Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466939"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Felsöka läsning av UTF-8-text från CSV-eller Parquet-filer med Server lös SQL-pool i Azure Synapse Analytics

Den här artikeln innehåller fel söknings steg för att läsa UTF-8-text från CSV-eller Parquet-filer med Server lös SQL-pool i Azure Synapse Analytics.

När UTF-8-text läses från en CSV-eller PARQUET-fil med en server lös SQL-pool, konverteras vissa specialtecken som ü och ö felaktigt om frågan returnerar VARCHAR-kolumner med icke-UTF8-sorteringar. Detta är ett känt problem i SQL Server och Azure SQL. Icke-UTF8-sortering är standard i Synapse SQL, så kund frågor kommer att påverkas. Kunder som använder vanliga engelska tecken och vissa delmängd av utökade latinska tecken kanske inte märker konverterings felen. Den felaktiga konverteringen beskrivs i detalj i [Använd alltid UTF-8-sorteringar för att läsa UTF-8-text i SQL-poolen utan server](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Lösning

Lösningen på det här problemet är att alltid använda UTF-8-sortering vid läsning av UTF-8-text från CSV-eller PARQUET-filer.

-   I många fall behöver du bara ange UTF8-sortering för databasen (metadata-åtgärd).
-   Om du inte angav UTF8-sortering på externa tabeller som läser UTF8-data måste du återskapa påverkade externa tabeller och ställa in UTF8-sortering i VARCHAR-kolumner (metadata-åtgärd).


## <a name="next-steps"></a>Nästa steg

* [CETAS med Synapse SQL](../sql/develop-tables-cetas.md)
* [Snabb start: Använd Server lös SQL-pool](../quickstart-sql-on-demand.md)
