---
title: Azure SQL Database - automatisk justering | Microsoft Docs
description: "Azure SQL Database analyserar SQL-fråga och anpassas automatiskt till användare arbetsbelastning."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Azure SQL Database är en helt hanterad datatjänst som övervakar de frågor som körs på databasen och automatiskt förbättrar prestanda för arbetsbelastningen som databas. Azure SQL-databas har en inbyggd [automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) intelligence mekanism som automatiskt kan justera och förbättra prestanda för dina frågor genom att dynamiskt anpassa databasen till din arbetsbelastning. Automatisk justering i Azure SQL Database kan vara något av de viktigaste funktionerna att du kan aktivera på Azure SQL Database för att optimera prestanda för dina frågor.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

[Automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) alternativen i Azure SQL Database är:
 1. **CREATE INDEX** som identifierar de index som kan förbättra prestandan för din arbetsbelastning, skapar index och verifierar de förbättra prestanda för frågor.
 2. **DROP INDEX** som identifierar redundant och dubbla index och index som inte använts under en längre tidsperiod.
 3. **Planera REGRESSION korrigering** som identifierar SQL-frågor som använder åtgärdsplan som är långsammare än tidigare bra plan och använder den senaste kända bra planen i stället för regressed planen.

Azure SQL-databas identifierar **CREATE INDEX**, **DROP INDEX**, och **planera REGRESSION korrigering** rekommendationer som kan optimera din databas och visar dem i Azure portalen. Mer information om identifiering av index som ska ändras på [hitta index-rekommendationer i Azure-portalen](sql-database-advisor-portal.md). Du kan antingen tillämpa rekommendationer med hjälp av portalen manuellt eller du kan låta Azure SQL Database för att automatiskt tillämpa rekommendationer övervaka arbetsbelastning när ändringen och verifiera att rekommendationen bättre prestanda för din arbetsbelastning.

Automatisk justering alternativ kan vara oberoende aktiverat eller inaktiverat per databas eller de kan konfigureras på logisk server och tillämpas på alla databaser som ärver inställningarna från servern. Konfigurera [automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) alternativ på servern och arv inställningar på databaserna på servern är rekommenderad metod för att konfigurera automatisk justering eftersom det förenklar hanteringen av alternativ för automatisk justering på en stort antal databaser.

Finns den här artikeln stegen för att [aktivera automatisk justering](sql-database-automatic-tuning-enable.md) med Azure-portalen.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och låta automatisk justering funktionen för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill använda manuell inställning, kan du granska [justera rekommendationerna i Azure-portalen](sql-database-advisor-portal.md) och tillämpa manuellt med de som förbättrar prestandan för dina frågor.
- Läs mer om inbyggd intelligens som justerar den [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Läs mer om [automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) i Azure SQL Database och SQL Server 2017.
