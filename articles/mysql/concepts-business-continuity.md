---
title: Översikt över affärskontinuitet med Azure Database for MySQL
description: Översikt över affärskontinuitet med Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8d0a8ac1aedcac26f6ac67e0e1f2a9d265a9b576
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714353"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql"></a>Översikt över affärskontinuitet med Azure Database for MySQL

Den här översikten beskriver de funktioner som Azure Database for MySQL tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Lär dig vad du ska göra när en användare eller ett program fel påverkar dataintegriteten, uppstår ett avbrott i en Azure-region eller ditt program kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att upprätthålla affärskontinuitet

Azure Database for MySQL tillhandahåller funktioner för affärskontinuitet med automatiserade säkerhetskopieringar och möjligheten för användare för att initiera geo-återställning. Var och en har olika egenskaper för beräknad Recovery tid (ERT) och dataförluster. När du förstår dessa alternativ kan du välja mellan dem och använda dem tillsammans för olika scenarier. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse – detta är ditt Recovery mål för återställningstid (RTO). Du måste också att förstå längsta senaste data uppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse – detta är ditt mål för återställningspunkt (RPO).

I följande tabell jämförs ERT och RPO för de tillgängliga funktionerna:

| **Funktionen** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden |
| GEO-återställning från geo-replikerade säkerhetskopior | Stöds inte | ERT < 12 timme<br/>RPO < 1 timme | ERT < 12 timme<br/>RPO < 1 timme |

> [!IMPORTANT]
> Ta bort servrar **kan** återställas. Om du tar bort servern tas även bort alla databaser som hör till servern och kan inte återställas.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter en användare eller ett program-fel

Du kan använda tjänstens säkerhetskopieringar för att återställa en server från olika avbrottshändelser. En användare kan av misstag tar bort vissa data, oavsiktligt släppa en viktig tabell eller även en hel databas. Ett program kan av misstag skriva över värdefulla data med felaktiga data på grund av ett fel i programmet och så vidare.

Du kan utföra en punkt i-återställning till tidpunkt för att skapa en kopia av servern till en känd bra tidpunkt i tid. Den här tidpunkt måste finnas inom kvarhållningsperioden för säkerhetskopior du har konfigurerat för din server. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från en Azure regionalt avbrott på datacentret

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar ett verksamhetsavbrott som kan vara ett par minuter, men kan senaste i timmar.

Ett alternativ är att vänta på din server så att du är tillbaka online när datadatacentret är över. Detta fungerar för program som har råd att ha servern offline under en tid, till exempel en utvecklingsmiljö. När det uppstår ett avbrott i datacentret, vet inte du hur länge driftstörningarna kan vara, så det här alternativet fungerar bara om du inte behöver din server ett tag.

Ett annat alternativ är att använda Azure Database för MySQL: s geo-återställning funktion som återställer servern med geo-redundanta säkerhetskopieringar. Dessa säkerhetskopior är tillgängliga även om den region som din server är värd för är offline. Du kan återställa från dessa säkerhetskopior till valfri region och ta med din server online igen.

> [!IMPORTANT]
> GEO-återställning är bara möjligt om du har etablerat på servern med geo-redundant lagring av säkerhetskopior. Om du vill växla från lokalt redundant till geo-redundanta säkerhetskopieringar för en befintlig server måste du ta en dump med pg_dump av din befintliga server och återställer dem till en nyligen skapade server som konfigurerats med geo-redundanta säkerhetskopieringar.

## <a name="next-steps"></a>Nästa steg

- Mer information om automatiska säkerhetskopior finns [säkerhetskopior i Azure Database for MySQL](concepts-backup.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure-portalen, se [återställa databasen till en tidpunkt med hjälp av Azure-portalen](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI, se [återställa databasen till en tidpunkt med hjälp av CLI](howto-restore-server-cli.md).