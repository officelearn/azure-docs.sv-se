---
title: Prestandarekommendationer - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs funktionen Prestandarekommendation i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768477"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Prestandarekommendationer i Azure Database för PostgreSQL - Single Server

**Gäller:** Azure-databas för PostgreSQL - Single Server version 9.6, 10, 11

Funktionen Prestandarekommendationer analyserar dina databaser för att skapa anpassade förslag på bättre prestanda. För att ta fram rekommendationerna tittar analysen på olika databasegenskaper, inklusive schema. Aktivera [Query Store](concepts-query-store.md) på servern för att fullt ut utnyttja funktionen Prestandarekommendationer. När du har implementerat en prestandarekommendation bör du testa prestanda för att utvärdera effekten av dessa ändringar. 

## <a name="permissions"></a>Behörigheter
Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestandarekommendationer** från avsnittet **Intelligent prestanda** i menyraden på Azure-portalsidan för din PostgreSQL-server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **Analysera** och välj en databas som ska påbörja analysen. Beroende på din arbetsbelastning kan det ta flera minuter att slutföra analysen. När analysen är klar, visas ett meddelande i portalen. Analys utför en djup undersökning av din databas. Vi rekommenderar att du utför analyser under lågtrafikperioder. 

Fönstret **Rekommendationer** visar en lista med eventuella rekommendationer.

![Nytt sidsida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationer tillämpas inte automatiskt. Om du vill använda rekommendationen kopierar du frågetexten och kör den från valfri klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen. 

## <a name="recommendation-types"></a>Typ av rekommendation

För närvarande stöds två typer av rekommendationer: *Skapa index* och *släppindex*.

### <a name="create-index-recommendations"></a>Skapa indexrekommendationer
*Skapa indexrekommendationer* föreslår nya index för att påskynda de vanligaste körnings- eller tidskrävande frågorna i arbetsbelastningen. Den här rekommendationstypen kräver att [Query Store](concepts-query-store.md) aktiveras. Query Store samlar in frågeinformation och tillhandahåller detaljerad frågekörnings- och frekvensstatistik som analysen använder för att göra rekommendationen.

### <a name="drop-index-recommendations"></a>Släpp indexrekommendationer
Förutom att identifiera saknade index analyserar Azure Database for PostgreSQL prestanda för befintliga index. Om ett index antingen sällan används eller är redundant rekommenderar analysatorn att släppa det.

## <a name="considerations"></a>Överväganden
* Prestandarekommendationer är inte tillgängliga för [läsrepliker](concepts-read-replicas.md).
## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.

