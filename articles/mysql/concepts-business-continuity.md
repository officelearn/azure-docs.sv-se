---
title: Verksamhets kontinuitet – Azure Database for MySQL
description: Lär dig mer om verksamhets kontinuitet (tidpunkts återställning, avbrott i Data Center, geo-återställning) när du använder Azure Database for MySQL-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537235"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Förstå affärs kontinuitet i Azure Database for MySQL

Den här artikeln beskriver de funktioner som Azure Database for MySQL tillhandahåller för verksamhets kontinuitet och haveri beredskap. Lär dig mer om alternativ för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärs kontinuitet

Azure Database for MySQL tillhandahåller funktioner för affärs kontinuitet som inkluderar automatiserade säkerhets kopieringar och användarens möjlighet att initiera geo-återställning. Varje har olika egenskaper för den beräknade återställnings tiden (ERT) och potentiell data förlust. När du har förstått de här alternativen kan du välja bland dem och använda dem tillsammans för olika scenarier. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter störnings händelsen – detta är ditt återställnings tids mål (RTO). Du måste också förstå den maximala mängden senaste data uppdateringar (tidsintervall) som programmet kan tolerera vid återställning efter en störnings händelse – detta är återställnings punkt målet.

I följande tabell jämförs ERT och återställnings punkt för de tillgängliga funktionerna:

| **Funktion** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden |
| Geo-återställning från geo-replikerade säkerhets kopieringar | Stöds inte | ERT < 12 h<br/>Återställnings < 1 h | ERT < 12 h<br/>Återställnings < 1 h |

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar-eller program fel

Du kan använda tjänstens säkerhets kopieringar för att återställa en server från olika störande händelser. En användare kan oavsiktligt ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett program fel och så vidare.

Du kan utföra en tidpunkts återställning för att skapa en kopia av servern till en känd lämplig tidpunkt. Den här tidpunkten måste ligga inom den bevarande period för säkerhets kopior som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från ett Azure regional Data Center-avbrott

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar kan det orsaka ett verksamhets avbrott som bara tar några minuter, men som kan ha varit under några timmar.

Ett alternativ är att vänta tills servern kommer tillbaka online när avbrott i data centret är över. Detta fungerar för program som kan ge servern att vara offline under en viss tids period, till exempel en utvecklings miljö. När data Center har ett avbrott vet du inte hur lång tid det tar för avbrottet, så det här alternativet fungerar bara om du inte behöver servern en stund.

Det andra alternativet är att använda Azure Database for MySQL ' geo-Restore-funktionen som återställer servern med geo-redundanta säkerhets kopieringar. Dessa säkerhets kopior är tillgängliga även när den region som servern finns i är offline. Du kan återställa från dessa säkerhets kopior till en annan region och ta servern online igen.

> [!IMPORTANT]
> Geo-återställning är bara möjlig om du har upprättat servern med Geo-redundant lagring av säkerhets kopior. Om du vill växla från lokalt redundant till Geo-redundant säkerhets kopiering för en befintlig server måste du ta en dump med mysqldump av din befintliga server och återställa den till en nyskapad server som kon figurer ATS med geo-redundanta säkerhets kopieringar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [automatiska säkerhets kopieringarna i Azure Database for MySQL](concepts-backup.md).
- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Läs mer om att [läsa repliker i Azure Database for MySQL](concepts-read-replicas.md).
