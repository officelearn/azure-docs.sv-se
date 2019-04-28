---
title: Översikt över affärskontinuitet med Azure Database for MariaDB
description: Översikt över affärskontinuitet med Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043036"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Översikt över affärskontinuitet med Azure Database for MariaDB

Den här översikten beskriver de funktioner som Azure Database for MariaDB tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Lär dig vad du ska göra när en användare eller ett program fel påverkar dataintegriteten, uppstår ett avbrott i en Azure-region eller ditt program kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att upprätthålla affärskontinuitet

Azure Database for MariaDB tillhandahåller funktioner för affärskontinuitet med automatiserade säkerhetskopieringar och möjligheten för användare för att initiera geo-återställning. Var och en har olika egenskaper för beräknad Recovery tid (ERT) och dataförluster. När du förstår dessa alternativ kan du välja mellan dem och använda dem tillsammans för olika scenarier. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse – detta är ditt Recovery mål för återställningstid (RTO). Du måste också att förstå längsta senaste data uppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse – detta är ditt mål för återställningspunkt (RPO).

I följande tabell jämförs ERT och RPO för de tillgängliga funktionerna:

| **Funktionen** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden |
| GEO-återställning från geo-replikerade säkerhetskopior | Stöds inte | ERT < 12 timme<br/>RPO < 1 timme | ERT < 12 timme<br/>RPO < 1 timme |

> [!IMPORTANT]
> Om du tar bort servern tas även bort alla databaser som ingår i servern och kan inte återställas. Du kan inte återställa en borttagen server.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter en användare eller ett program-fel

Du kan använda tjänstens säkerhetskopieringar för att återställa en server från olika avbrottshändelser. En användare kan av misstag tar bort vissa data, oavsiktligt släppa en viktig tabell eller även en hel databas. Ett program kan av misstag skriva över värdefulla data med felaktiga data på grund av ett fel i programmet och så vidare.

Du kan utföra en punkt i-återställning till tidpunkt för att skapa en kopia av servern till en känd bra tidpunkt i tid. Den här tidpunkt måste finnas inom kvarhållningsperioden för säkerhetskopior du har konfigurerat för din server. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från en Azure regionalt avbrott på datacentret

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar ett verksamhetsavbrott som kan vara ett par minuter, men kan senaste i timmar.

Ett alternativ är att vänta på din server så att du är tillbaka online när datadatacentret är över. Detta fungerar för program som har råd att ha servern offline under en tid, till exempel en utvecklingsmiljö. När det uppstår ett avbrott i datacentret, vet inte du hur länge driftstörningarna kan vara, så det här alternativet fungerar bara om du inte behöver din server ett tag.

Ett annat alternativ är att använda Azure-databas för MariaDB geo-återställning funktion som återställer servern med geo-redundanta säkerhetskopieringar. Dessa säkerhetskopior är tillgängliga även om den region som din server är värd för är offline. Du kan återställa från dessa säkerhetskopior till valfri region och ta med din server online igen.

> [!IMPORTANT]
> GEO-återställning är bara möjligt om du har etablerat på servern med geo-redundant lagring av säkerhetskopior.

## <a name="next-steps"></a>Nästa steg

- Mer information om automatiska säkerhetskopior finns [säkerhetskopior i Azure Database for MariaDB](concepts-backup.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure-portalen, se [återställa databasen till en tidpunkt med hjälp av Azure-portalen](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
