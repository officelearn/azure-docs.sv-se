---
title: SQL Data Warehouse-rekommendationer – begrepp | Microsoft Docs
description: Läs mer om rekommendationer för SQL Data Warehouse och hur de genereras
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2cbd691c29039c65b98d8b0191e9e278d2440f09
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348621"
---
# <a name="sql-data-warehouse-recommendations"></a>Rekommendationer för SQL Data Warehouse

Den här artikeln beskriver de rekommendationer som hanteras av SQL Data Warehouse med Azure Advisor.  

SQL Data Warehouse ger rekommendationer för att se till att ditt informationslager konsekvent har optimerats för prestanda. Data warehouse rekommendationer är nära integrerad med [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) att förse dig med metodtips direkt inom den [Azure-portalen](https://aka.ms/Azureadvisor). SQL Data Warehouse analyserar det aktuella tillståndet för ditt informationslager, samlar in telemetri och ytor rekommendationer för din aktiva arbetsbelastning på en daglig takt. Stöds data warehouse rekommendation scenarier som beskrivs nedan tillsammans med hur du tillämpa rekommenderade åtgärder.

Om du har synpunkter på SQL Data Warehouse Advisor eller stöter på problem, kontakta [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Klicka på [här](https://aka.ms/Azureadvisor) att kontrollera dina rekommendationer idag! Den här funktionen är för närvarande gäller för Gen2-informationslager. 

## <a name="data-skew"></a>Datasnedställning

Datasnedställning kan orsaka ytterligare data movement eller resurs flaskhalsar när du kör din arbetsbelastning. Följande dokumentation beskriver att identifiera datasnedställning och förhindra att det inträffar genom att välja en optimal distributionsnyckeln.

- [Identifiera och ta bort skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Inte eller är inaktuella statistik

Att använda icke-optimal statistik kan frågeprestanda kraftigt försämras eftersom detta kan medföra Frågeoptimeringen SQL Data Warehouse att skapa icke-optimal frågeplaner. Följande dokumentation innehåller metodtips kring skapar och uppdaterar statistik:

- [Skapa och uppdatera tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

Dessa två rekommendationer körs kontinuerligt i advisor följande [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) att identifiera tabeller som påverkas av rekommendationer skeva och statistik.
