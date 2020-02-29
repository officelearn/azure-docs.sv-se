---
title: Rekommendationer för SQL Analytics
description: Lär dig mer om SQL Analytics-rekommendationer och hur de genereras
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5471236c09737eeef2d4cb7542c245d3087e726c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195965"
---
# <a name="sql-analytics-recommendations"></a>Rekommendationer för SQL Analytics

I den här artikeln beskrivs rekommendationer för SQL Analytics som hanteras via Azure Advisor.  

SQL Analytics ger rekommendationer för att säkerställa att arbets belastningen för data lagret är konsekvent optimerad för prestanda. Rekommendationerna är nära integrerade med [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) för att ge dig bästa praxis direkt i [Azure Portal](https://aka.ms/Azureadvisor). SQL Analytics samlar in telemetri-och underlag rekommendationer för din aktiva arbets belastning på en daglig takt. De rekommendations scenarier som stöds beskrivs nedan tillsammans med hur du använder rekommenderade åtgärder.

Du kan [kontrol lera dina rekommendationer](https://aka.ms/Azureadvisor) idag! Den här funktionen gäller för närvarande endast för Gen2-informations lager. 

## <a name="data-skew"></a>Data skevning

Dataskevning kan orsaka ytterligare data flytt eller resurs Flask halsar när du kör arbets belastningen. I följande dokumentation beskrivs hur du identifierar data skevning och förhindrar att den kan utföras genom att välja en optimal distributions nyckel.

- [Identifiera och ta bort skevning](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Ingen eller inaktuell statistik

Om du har en underoptimerad statistik kan du kraftigt påverka frågeresultatet eftersom det kan orsaka att SQL Query Optimering genererar underoptimala fråge planer. I följande dokumentation beskrivs de bästa metoderna för att skapa och uppdatera statistik:

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
