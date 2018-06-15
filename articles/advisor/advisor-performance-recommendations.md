---
title: Azure Advisor-rekommendationer | Microsoft Docs
description: Använda Advisor för att optimera prestanda för din Azure-distributioner.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 00abb5aafc6f3aec2e2dd7326a307bee74d97cc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149363"
---
# <a name="advisor-performance-recommendations"></a>Advisor-rekommendationer

Azure Advisor-rekommendationer att förbättra hastighet och tillgänglighet för verksamhetskritiska program. Du kan få rekommendationer från Advisor på den **prestanda** på Advisor-instrumentpanelen.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Förbättra prestanda för databasen med SQL DB Advisor

Advisor ger en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser. Den kan integreras med SQL Database Advisor för att göra rekommendationer för att förbättra prestandan för din SQL Azure-databas. SQL Database Advisor utvärderar prestanda för SQL Azure-databaser genom att analysera din användningshistorik. Den erbjuder sedan rekommendationer som passar bäst för att köra databasens vanlig arbetsbelastning. 

> [!NOTE]
> För att få rekommendationer, en databas måste ha om en vecka för användning och som veckan måste det finnas vissa konsekvent aktivitet. SQL Database Advisor kan optimera enklare för konsekvent frågemönster än för slumpmässiga belastning för aktiviteten.

Mer information om SQL Database Advisor finns [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Förbättra Redis-Cache-prestanda och tillförlitlighet

Advisor identifierar Redis-Cache instanser där prestanda kan påverkas negativt av hög minnesanvändning, serverbelastning, bandbredd i nätverket eller ett stort antal klientanslutningar. Advisor innehåller även metodtips rekommendationer som hjälper dig att undvika potentiella problem. Mer information om Redis-Cache rekommendationer finns [Redis-Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Förbättra Apptjänst prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer om bästa praxis för att förbättra din upplevelse Apptjänster och identifiera relevanta plattformsfunktioner. Exempel på Apptjänster rekommendationer är:
* Identifiera instanser där minne eller CPU-resurserna är uttömda av app körningar med alternativ för lösning.
* Identifiera instanser där collocating resurser som webbappar och databaser kan förbättra prestanda och den lägre kostnaden. 

Mer information om Apptjänster rekommendationer finns [bästa praxis för Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Hur du kommer åt rekommendationer i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  Advisor-instrumentpanelen, klicka på den **prestanda** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnad Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

