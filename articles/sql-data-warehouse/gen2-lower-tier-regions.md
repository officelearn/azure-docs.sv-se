---
title: Azure SQL Data Warehouse Gen2 stöder lägre beräkningsnivåer | Microsoft Docs
description: Azure SQL Data Warehouse Gen2 har nu stöd för lägre beräkningsnivåer
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: bd0ec4d334b581d86451e5e534e80c1fde1e33ac
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445250"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Support för Azure SQL Data Warehouse Gen2 för lägre beräkningsnivåer

Microsoft hjälper sänka på ingångsnivå kostnaden för att köra ett data warehouse kan hantera krävande frågor genom att lägga till lägre beräkningsnivåer för den blixtsnabb snabb Azure SQL Data Warehouse Gen2. Kunder kan uppleva Azure SQL Data Warehouse ledande prestanda, flexibilitet och säkerhet funktioner från och med 100 cDWU (Informationslagerenheter) och skala till 30 000 cDWU på några minuter. Kunder kan dra nytta av Gen2 prestanda och flexibilitet med lägre beräkningsnivåer. 

Genom att släppa startpunkt för nästa generation av informationslager, öppnas Microsoft dörrar till värdet-drivna kunder som vill utvärdera alla fördelar med en säker, högpresterande data warehouse utan att gissa vilka utvärderingsmiljö är bäst för dessa.  Kunder kommer att kunna starta så lågt som 100 cDWU ned från den aktuella 500 cDWU startpunkten.  SQL Data Warehouse Gen2 fortsätter att stödja pausa och återuppta drift- och går utöver bara flexibilitet vid beräkning.  Gen2 också har stöd för obegränsad columnstore lagringskapacitet tillsammans med 2,5 gånger mer minne per fråga, upp till 128 samtidiga frågor och Adaptiv cachelagring funktioner att både upplevelser i genomsnitt 5 gånger så mycket prestanda jämfört med samma Data Warehouse enhet på Gen1 till samma pris.  GEO-redundanta säkerhetskopieringar är standard för Gen2 med inbyggda garanterad dataskydd. Azure SQL Data Warehouse Gen2 är redo att skala när du är.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Komma igång med Azure SQL Data Warehouse Gen2 

Kunder kan välja att distribuera en ny Gen2-instans eller uppgradera en befintlig Gen1 data warehouse-instans för att uppleva flexibiliteten och prestandan för nästa generations data informationslager. 

Försök [Azure SQL Data Warehouse Beräkningsnivån optimerad Gen2.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Uppgradera [Azure SQL Data Warehouse Compute Optimized Gen1 till Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) titta på Azure SQL Data Warehouse Gen2 i praktiken i den här [videon om Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Regioner som stöds för lägre beräkningsnivåer

[Tabellen tillgänglighet för region som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="next-steps"></a>Nästa steg

- [Läs mer](upgrade-to-latest-generation.md) om Compute Optimized prestanda genom att uppgradera SQL Data Warehouse. 
