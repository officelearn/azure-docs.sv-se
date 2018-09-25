---
title: Prestandarekommendationer i Azure Database för PostgreSQL
description: Den här artikeln beskriver prestandarekommendationer kan komma in Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 25ab2f735cfd4b0870729cb86992665fa8984580
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976384"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Prestandarekommendationer i Azure Database för PostgreSQL

**Gäller för:** Azure Database för PostgreSQL 9.6 och 10

> [!IMPORTANT]
> Prestandarekommendationer finns i offentlig förhandsversion.

Funktionen Prestandarekommendationer identifierar de översta index som kan skapas i Azure Database for PostgreSQL-server för att förbättra prestanda. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna. 

## <a name="permissions"></a>Behörigheter
**Ägare** eller **deltagare** behörigheter som krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den [Prestandarekommendationer](concepts-performance-recommendations.md) funktionen analyserar arbetsbelastningar mellan din server för att identifiera index med möjligheter att förbättra prestanda.

Öppna **Prestandarekommendationer** från den **Support och felsökning** delen av menyraden på sidan för Azure portal för din PostgreSQL-server.

![Prestandarekommendationer landningssida](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Välj **analysera** och välj en databas. Detta kommer att börja analysen. Det kan ta flera minuter att slutföra beroende på arbetsbelastningen. När analysen är klar, kommer det att ett meddelande i portalen.

Den **Prestandarekommendationer** i fönstret visas en lista över rekommendationer om några. En rekommendation visas information om den relevanta **databasen**, **tabell**, **kolumnen**, och **Indexstorlek**.

![Ny sida för prestanda-rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Om du vill implementera rekommendationen kopiera frågetexten och köra den från din klient föredrar.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database för PostgreSQL.

