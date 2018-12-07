---
title: Azure Advisor-rekommendationer | Microsoft Docs
description: Använd Advisor för att optimera prestandan för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 349632c751c3116244bc8ef7708708f3aa45754c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013244"
---
# <a name="advisor-performance-recommendations"></a>Advisor-rekommendationer

Azure Advisor-rekommendationer att förbättra hastighet och tillgänglighet för dina verksamhetskritiska program. Du kan hämta prestandarekommendationer från Advisor den **prestanda** fliken Advisor-instrumentpanelen.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Minska tiden för DNS-TTL-värde på Traffic Manager-profilen att växla över till felfria slutpunkter snabbare

[Tid till Live (TTL) inställningar](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) på din Traffic Manager-profil kan du ange hur snabbt växla slutpunkter om en viss slutpunkt slutar svara på frågor. Minska TTL-värden innebär att klienter kommer att dirigeras till fungerande slutpunkter snabbare.

Azure Advisor identifierar Traffic Manager-profiler med ett längre TTL-värde som har konfigurerats och rekommenderar konfigurera TTL-värdet till 20 sekunder eller 60 sekunder beroende på om profilen som har konfigurerats för [snabb redundans](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Förbättra databasens prestanda med SQL DB Advisor

Advisor ger dig en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser. Det integreras med SQL Database Advisor för att ge dig rekommendationer för att förbättra prestandan för din SQL Azure-databas. SQL Database Advisor utvärderar prestanda för dina SQL Azure-databaser genom att analysera din användningshistorik. Den erbjuder sedan rekommendationer som passar bäst för att köra databasens normal belastning. 

> [!NOTE]
> För att få rekommendationer, en databas måste ha om en veckas användning och inom den veckan måste det finnas viss konsekvent aktivitet. SQL Database Advisor kan optimera enklare för konsekvent frågemönster än för slumpmässiga ökningar av aktivitet.

Läs mer om SQL Database Advisor [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-azure-cache-for-redis-performance-and-reliability"></a>Förbättra Azure Cache för Redis-prestanda och tillförlitlighet

Advisor identifierar Azure Cache för Redis-instanser där prestanda kan påverkas negativt av hög minnesanvändning, serverbelastningen, bandbredd i nätverket eller ett stort antal klientanslutningar. Advisor tillhandahåller också metodtips rekommendationer för att hjälpa dig att undvika potentiella problem. Läs mer om Azure Cache för Redis-rekommendationer, [Azure Cache för Redis Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Förbättra App Service-prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer om bästa praxis för att förbättra din upplevelse för App Services och identifiera relevanta plattformsfunktioner. Exempel på rekommendationer för App Services är:
* Identifiering av instanser där minne eller CPU-resurser är slut genom appen körningar med alternativ för lösning.
* Identifiering av instanser där collocating resurser som webbappar och databaser kan förbättra prestanda och lägre kostnader. 

Läs mer om rekommendationer för App Services, [Metodtips för Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Ta bort datasnedställning på SQL data warehouse-tabell att öka prestanda för frågor

Datasnedställning kan orsaka onödiga data movement eller resurs flaskhalsar när du kör din arbetsbelastning. Advisor identifierar distributionsdata förskjuta större än 15% och rekommenderar att du distribuera om dina data och gå tillbaka till dina viktiga val för tabell-distribution. Läs mer om identifiera och ta bort skeva i [felsökning skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Skapa eller uppdatera inaktuella tabellstatistik på SQL data warehouse-tabell att öka prestanda för frågor

Advisor identifierar tabeller som inte har uppdaterat [tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) och rekommenderar att skapa eller uppdatera tabellstatistik. SQL data warehouse-fråga optimering använder uppdaterade statiska värden för att beräkna kardinalitet eller antalet rader i frågeresultatet som gör att frågeoptimerare att skapa en högkvalitativ frågeplan för bästa prestanda.

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrera ditt Storage-konto till Azure Resource Manager för att få alla de senaste Azure-funktionerna

Migrera din modell för distribution av Storage-konto till Azure Resource Manager (ARM) att dra nytta av malldistributioner, ytterligare säkerhetsalternativ och möjlighet att uppgradera till ett GPv2-konto för användning av Azure Storage senaste funktionerna. Advisor identifierar eventuella fristående lagringskonton som använder den klassiska distributionsmodellen och rekommenderar att du migrerar till ARM-distributionsmodellen. 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Hur du kommer åt prestandarekommendationer i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på den **prestanda** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor kostnadsrekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

