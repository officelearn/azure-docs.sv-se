---
title: SQL Data Warehouse rekommendationer
description: Lär dig mer om SQL Data Warehouse rekommendationer och hur de genereras
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a93339b99c6591ee9ed615c032d0eac58a372fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827504"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse rekommendationer

I den här artikeln beskrivs rekommendationer som hanteras av SQL Data Warehouse via Azure Advisor.  

SQL Data Warehouse ger rekommendationer för att säkerställa att informations lagret är konsekvent optimerat för prestanda. Informations lager rekommendationerna är nära integrerade med [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) för att ge dig bästa praxis direkt i [Azure Portal](https://aka.ms/Azureadvisor). SQL Data Warehouse analyserar det aktuella läget för ditt informations lager, samlar in telemetri och underlags rekommendationer för din aktiva arbets belastning på en daglig takt. De scenarier som stöds av data lagrets rekommendationer beskrivs nedan tillsammans med hur du använder rekommenderade åtgärder.

Om du har några synpunkter på SQL Data Warehouse Advisor eller om du får problem kan du kontakta [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Klicka [här](https://aka.ms/Azureadvisor) för att kontrol lera dina rekommendationer idag! Den här funktionen gäller för närvarande endast för Gen2-informations lager. 

## <a name="data-skew"></a>Data skevning

Dataskevning kan orsaka ytterligare data flytt eller resurs Flask halsar när du kör arbets belastningen. I följande dokumentation beskrivs hur du identifierar data skevning och förhindrar att den kan utföras genom att välja en optimal distributions nyckel.

- [Identifiera och ta bort skevning](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Ingen eller inaktuell statistik

Om du har en underoptimerad statistik kan du kraftigt påverka frågeresultatet eftersom det kan orsaka att SQL Data Warehouse Query Optimering genererar underoptimala fråge planer. I följande dokumentation beskrivs de bästa metoderna för att skapa och uppdatera statistik:

- [Skapa och uppdatera tabell statistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Om du vill se en lista över berörda tabeller med dessa rekommendationer kör du följande [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor kör kontinuerligt samma T-SQL-skript för att skapa rekommendationerna.

## <a name="replicate-tables"></a>Replikera tabeller

För replikerade tabell rekommendationer identifierar Advisor tabell kandidater baserat på följande fysiska egenskaper:

- Replikerad tabell storlek
- Antal kolumner
- Tabell distributions typ
- Antal partitioner

Advisor utnyttjar kontinuerligt arbets belastnings heuristik, till exempel tabell åtkomst frekvens, rader som returneras i genomsnitt och tröskelvärden kring storlek och aktivitet för data lagret för att säkerställa att rekommendationerna skapas med hög kvalitet. 

Här följer en beskrivning av de arbets belastnings-baserade heuristik som du kan hitta i Azure Portal för varje replikerad tabell rekommendation:

- I genomsnitt används den genomsnittliga procent andelen av rader som returnerades från tabellen för varje tabell åtkomst under de senaste sju dagarna
- Frekvent läsning, ingen uppdatering – visar att tabellen inte har uppdaterats under de senaste sju dagarna vid visning av åtkomst aktivitet
- Läs-/uppdaterings kvot – förhållandet mellan hur ofta tabellen användes i relation till när den uppdaterades de senaste sju dagarna
- Aktivitet – mäter användningen baserat på åtkomst aktivitet. Detta jämför tabell åtkomst aktiviteten i förhållande till den genomsnittliga tabell åtkomst aktiviteten i data lagret under de senaste sju dagarna. 

För närvarande kan Advisor bara visa högst fyra replikerade tabell kandidater samtidigt med grupperade columnstore-index som prioriterar den högsta aktiviteten.

> [!IMPORTANT]
> Den replikerade tabell rekommendationen är inte full och tar inte hänsyn till data flyttnings åtgärder. Vi arbetar med att lägga till detta som en tumregel, men under tiden bör du alltid validera din arbets belastning när du har tillämpat rekommendationen. Kontakta sqldwadvisor@service.microsoft.com om du upptäcker replikerade tabell rekommendationer som leder till att din arbets belastning regress. Mer information om replikerade tabeller finns i följande [dokumentation](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
