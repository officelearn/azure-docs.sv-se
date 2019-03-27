---
title: Prestandarekommendationer i Azure Database för PostgreSQL
description: Den här artikeln beskriver funktionen rekommendation för prestanda i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: d8edbc2847c06e95e658a1324f2e85f1758e60be
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487948"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Prestandarekommendationer i Azure Database för PostgreSQL

**Gäller för:** Azure Database för PostgreSQL 9.6 och 10

Funktionen Prestandarekommendationer identifierar de översta index som kan skapas i Azure Database for PostgreSQL-server för att förbättra prestanda. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna. 

## <a name="permissions"></a>Behörigheter
Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **Prestandarekommendationer** från avdelningen **Support och felsökning** i menyraden på Azure-portalsidan för din PostgreSQL-server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Välj **Analysera** och välj en databas. Detta startar analysen. Det kan ta flera minuter att slutföra beroende på arbetsbelastningen. När analysen är klar, visas ett meddelande i portalen.

I fönstret **Prestandarekommendationer** visas en lista över rekommendationer om sådana hittas. En rekommendation visar information om relevant **databas**, **tabell**, **kolumn** och **indexstorlek**.

![Ny sida för prestanda-rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Om du vill implementera rekommendationen kopierar du frågetexten och kör den från önskad klient.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.

