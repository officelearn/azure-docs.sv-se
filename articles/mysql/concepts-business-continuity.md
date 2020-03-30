---
title: Affärskontinuitet - Azure-databas för MySQL
description: Lär dig mer om kontinuitet (point-in-time-återställning, avbrott i datacenter, geo-återställning) när du använder Azure Database for MySQL-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537235"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Förstå affärskontinuitet i Azure Database för MySQL

I den här artikeln beskrivs de funktioner som Azure Database for MySQL tillhandahåller för affärskontinuitet och haveriberedskap. Lär dig mer om alternativ för att återställa från störande händelser som kan orsaka dataförlust eller orsaka att databasen och programmet blir otillgängliga. Lär dig vad du kan göra när en användare eller ett programfel påverkar dataintegriteten, en Azure-region har ett avbrott eller ditt program kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärskontinuitet

Azure Database for MySQL tillhandahåller funktioner för affärskontinuitet som inkluderar automatiska säkerhetskopior och möjligheten för användare att initiera geoåterställning. Var och en har olika egenskaper för beräknad återställningstid (ERT) och potentiell dataförlust. När du förstår dessa alternativ kan du välja mellan dem och använda dem tillsammans för olika scenarier. När du utvecklar din kontinuitetsplan måste du förstå den maximala godtagbara tiden innan programmet återhämtar sig helt efter den störande händelsen - det här är ditt återställningstidsmål (RTO). Du måste också förstå den maximala mängden senaste datauppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återhämtar sig efter den störande händelsen - det här är ditt återställningspunktsmål (RPO).

I följande tabell jämförs ERT och RPO för de tillgängliga funktionerna:

| **Funktion** | **Basic** | **Allmänt syfte** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställningspunkter inom kvarhållningsperioden | Alla återställningspunkter inom kvarhållningsperioden | Alla återställningspunkter inom kvarhållningsperioden |
| Geoåterställning från geo-replikerade säkerhetskopior | Stöds inte | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Det **går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar- eller programfel

Du kan använda tjänstens säkerhetskopior för att återställa en server från olika störande händelser. En användare kan av misstag ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett programfel och så vidare.

Du kan utföra en point-in-time-restore för att skapa en kopia av servern till en fungerande tidpunkt. Den här tidpunkten måste vara inom den lagringsperiod för säkerhetskopiering som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från ett avbrott i ett regionalt datacenter i Azure

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar orsakar det ett avbrott i verksamheten som bara kan vara några minuter, men som kan pågå i timmar.

Ett alternativ är att vänta på att servern är online igen när datacentrets avbrott är över. Detta fungerar för program som har råd att ha servern offline under en viss tid, till exempel en utvecklingsmiljö. När datacenter har ett avbrott vet du inte hur länge avbrottet kan pågå, så det här alternativet fungerar bara om du inte behöver servern på ett tag.

Det andra alternativet är att använda Azure Database for MySQL:s geoåterställningsfunktion som återställer servern med hjälp av geouppsagbara säkerhetskopior. Dessa säkerhetskopior är tillgängliga även när den region som servern finns i är offline. Du kan återställa från dessa säkerhetskopior till någon annan region och ansluta servern igen.

> [!IMPORTANT]
> Geoåterställning är endast möjlig om du har etablerat servern med geouppsagd lagring för säkerhetskopiering. Om du vill växla från lokalt redundant till geo-redundanta säkerhetskopior för en befintlig server, måste du ta en soptipp med mysqldump på din befintliga server och återställa den till en nyligen skapad server som konfigurerats med geo-redundanta säkerhetskopior.

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [automatiska säkerhetskopiorna i Azure Database for MySQL](concepts-backup.md).
- Lär dig hur du återställer med [Azure-portalen](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Lär dig mer om [läsa repliker i Azure Database för MySQL](concepts-read-replicas.md).
