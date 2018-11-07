---
title: SQL Data Warehouse-rekommendationer – begrepp | Microsoft Docs
description: Läs mer om rekommendationer för SQL Data Warehouse och hur de genereras
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 712eed36f3a68ee02668849207835e3c8bdb8238
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232162"
---
# <a name="sql-data-warehouse-recommendations"></a>Rekommendationer för SQL Data Warehouse

Den här artikeln beskriver de rekommendationer som hanteras av SQL Data Warehouse med Azure Advisor.  

SQL Data Warehouse ger rekommendationer för att se till att ditt informationslager konsekvent har optimerats för prestanda. Data warehouse rekommendationer är nära integrerad med [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) att förse dig med metodtips direkt inom den [Azure-portalen](https://aka.ms/Azureadvisor). SQL Data Warehouse analyserar det aktuella tillståndet för ditt informationslager, samlar in telemetri och ytor rekommendationer för din aktiva arbetsbelastning på en daglig takt. Stöds data warehouse rekommendation scenarier som beskrivs nedan tillsammans med hur du tillämpa rekommenderade åtgärder.

Om du har synpunkter på SQL Data Warehouse Advisor eller stöter på problem, nå ut till [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Klicka på [här](https://aka.ms/Azureadvisor) att kontrollera dina rekommendationer idag! Den här funktionen är för närvarande gäller för Gen2-informationslager. 

## <a name="data-skew"></a>Datasnedställning

Datasnedställning kan orsaka ytterligare data movement eller resurs flaskhalsar när du kör din arbetsbelastning. Följande dokumentation beskriver att identifiera datasnedställning och förhindra att det inträffar genom att välja en optimal distributionsnyckeln.

- [Identifiera och ta bort skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Inte eller är inaktuella statistik

Att använda icke-optimal statistik kan frågeprestanda kraftigt försämras eftersom detta kan medföra Frågeoptimeringen SQL Data Warehouse att skapa icke-optimal frågeplaner. Följande dokumentation innehåller metodtips kring skapar och uppdaterar statistik:

- [Skapa och uppdatera tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Om du vill se en lista över tabeller som påverkas av de här rekommendationerna, kör du följande [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor körs kontinuerligt samma T-SQL-skript för att generera rekommendationerna.

## <a name="replicate-tables"></a>Replikera tabeller

Advisor identifierar tabell kandidaterna utifrån följande fysiska egenskaper för replikerad tabell rekommendationer:

- Replikerad tabellstorlek
- Antal kolumner
- Tabelltyp för distribution
- Antalet partitioner

Advisor utnyttjar kontinuerligt arbetsbelastning-baserade heuristik, till exempel tabellen åtkomst frekvens, rader returneras i genomsnitt och tröskelvärden runt data warehouse-storlek och aktiviteten för att säkerställa hög kvalitet rekommendationer genereras. 

Nedan beskrivs arbetsbelastning-baserade heuristik kanske i Azure-portalen för varje rekommendation för replikerad tabell:

- Skanna avg-genomsnittliga procentandelen rader som returnerades från tabellen för varje tabellåtkomst under de senaste sju dagarna
- Frekventa Läs-, utan uppdatering - anger att tabellen inte har uppdaterats under de senaste sju dagarna samtidigt som visar åtkomstaktivitet
- Läs/uppdatera förhållande - förhållandet mellan hur ofta tabellen användes i förhållande till när den uppdateras under de senaste sju dagarna
- Aktivitet – mäter användningen baserat på åtkomstaktivitet. Detta jämför tabellen åtkomstaktivitet i förhållande till åtkomstaktivitet genomsnittliga tabell över datalagret under de senaste sju dagarna. 

För närvarande visar Advisor endast högst fyra replikerad tabell kandidater samtidigt med prioritera aktiviteten högsta grupperade columnstore-index.

> [!IMPORTANT]
> Replikerad tabell rekommendationen är inte fullständig bevis och tar inte hänsyn till kontot dataflyttningsåtgärder. Vi arbetar på att lägga till detta som en tumregel men under tiden kan du alltid bör verifiera din arbetsbelastning efter att ha tillämpat rekommendationen. Kontakta sqldwadvisor@service.microsoft.com om du upptäcker replikerad tabell rekommendationer som gör att arbetsbelastningen och affärsmöjlighetens. Läs mer om replikerade tabeller på följande [dokumentation](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
>
