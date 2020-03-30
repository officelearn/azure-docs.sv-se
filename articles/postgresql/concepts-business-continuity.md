---
title: Affärskontinuitet - Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs affärskontinuitet (tidsåterställning, avbrott i datacenter, geoåterställning) när du använder Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981921"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Översikt över affärskontinuitet med Azure Database för PostgreSQL - Single Server

I den här översikten beskrivs de funktioner som Azure Database for PostgreSQL tillhandahåller för affärskontinuitet och haveriberedskap. Lär dig mer om alternativ för att återställa från störande händelser som kan orsaka dataförlust eller orsaka att databasen och programmet blir otillgängliga. Lär dig vad du kan göra när en användare eller ett programfel påverkar dataintegriteten, en Azure-region har ett avbrott eller ditt program kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärskontinuitet

Azure Database for PostgreSQL tillhandahåller funktioner för affärskontinuitet som inkluderar automatiska säkerhetskopior och möjligheten för användare att initiera geoåterställning. Var och en har olika egenskaper för beräknad återställningstid (ERT) och potentiell dataförlust. När du förstår dessa alternativ kan du välja mellan dem och använda dem tillsammans för olika scenarier. När du utvecklar din kontinuitetsplan måste du förstå den maximala godtagbara tiden innan programmet återhämtar sig helt efter den störande händelsen - det här är ditt återställningstidsmål (RTO). Du måste också förstå den maximala mängden senaste datauppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återhämtar sig efter den störande händelsen - det här är ditt återställningspunktsmål (RPO).

I följande tabell jämförs ERT och RPO för de tillgängliga funktionerna:

| **Funktion** | **Basic** | **Allmänt syfte** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställningspunkter inom kvarhållningsperioden | Alla återställningspunkter inom kvarhållningsperioden | Alla återställningspunkter inom kvarhållningsperioden |
| Geoåterställning från geo-replikerade säkerhetskopior | Stöds inte | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Det **går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. Använd [Azure-resurslås](../azure-resource-manager/management/lock-resources.md) för att förhindra oavsiktlig borttagning av servern.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar- eller programfel

Du kan använda tjänstens säkerhetskopior för att återställa en server från olika störande händelser. En användare kan av misstag ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett programfel och så vidare.

Du kan utföra en **point-in-time-restore** för att skapa en kopia av servern till en fungerande tidpunkt. Den här tidpunkten måste vara inom den lagringsperiod för säkerhetskopiering som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-data-center-outage"></a>Återställa från ett avbrott i Azure-datacenter

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar orsakar det ett avbrott i verksamheten som bara kan vara några minuter, men som kan pågå i timmar.

Ett alternativ är att vänta på att servern är online igen när datacentrets avbrott är över. Detta fungerar för program som har råd att ha servern offline under en viss tid, till exempel en utvecklingsmiljö. När ett datacenter har ett avbrott vet du inte hur länge avbrottet kan pågå, så det här alternativet fungerar bara om du inte behöver servern på ett tag.

## <a name="geo-restore"></a>Geo-återställning

Geoåterställningsfunktionen återställer servern med hjälp av geouppsagade säkerhetskopior. Säkerhetskopiorna finns i serverns [parkopplade region](../best-practices-availability-paired-regions.md). Du kan återställa från dessa säkerhetskopior till någon annan region. Geoåterställning skapar en ny server med data från säkerhetskopiorna. Läs mer om geoåterställning från [artikeln för säkerhetskopiering och återställning](concepts-backup.md)av koncept .

> [!IMPORTANT]
> Geoåterställning är endast möjlig om du har etablerat servern med geouppsagd lagring för säkerhetskopiering. Om du vill växla från lokalt redundant till geo-redundanta säkerhetskopior för en befintlig server måste du ta en dump med pg_dump av din befintliga server och återställa den till en nyligen skapad server som konfigurerats med geo-redundanta säkerhetskopior.

## <a name="cross-region-read-replicas"></a>Läsrepliker mellan regioner
Du kan använda läsa repliker över flera regioner för att förbättra affärskontinuiteten och planeringen för haveriberedskap. Läsrepliker uppdateras asynkront med PostgreSQL:s fysiska replikeringsteknik. Läs mer om läsrepliker, tillgängliga regioner och hur du växlar över från [artikeln med läsrepliker](concepts-read-replicas.md). 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [de automatiska säkerhetskopiorna i Azure Database för PostgreSQL](concepts-backup.md). 
- Lär dig hur du återställer med [Azure-portalen](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Lär dig mer om [lästa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).