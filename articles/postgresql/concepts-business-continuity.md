---
title: "Översikt över verksamhetskontinuitet med Azure-databas för PostgreSQL"
description: "Översikt över verksamhetskontinuitet med Azure-databas för PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1b981b650d75556f4521aaf0f089443bb88d064a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Översikt över verksamhetskontinuitet med Azure-databas för PostgreSQL

Den här översikten beskriver funktionerna som Azure-databas för PostgreSQL ger för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa från avbrott som kan leda till dataförlust eller leda till databasen och programmet ska bli tillgänglig. Lär dig vad du gör om en användare eller ett program fel påverkar dataintegritet, en Azure-region har ett avbrott eller programmet kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att tillhandahålla kontinuitet för företag

Azure-databas för PostgreSQL tillhandahåller funktionerna för verksamhetskontinuitet som omfattar automatiserad säkerhetskopieringar och möjligheten för användare för att initiera geo-återställning. Varje har olika egenskaper för beräknad Recovery tid (Infoga) och potentiell dataförlust. När du förstår de här alternativen kan du välja mellan dem och använda dem tillsammans för olika scenarier. När du utvecklar en kontinuitetsplan måste du förstå den maximala godtagbara tiden innan programmet helt återställer efter händelsen störande – det här är din Recovery tid mål för Återställningstid. Du måste också att förstå hur senaste data uppdateringar (tidsintervall) programmet kan tolerera förlorar när återställning efter händelsen störande – det här är återställning punkt mål (RPO).

I följande tabell jämförs infoga och Återställningspunktsmål för funktionerna som är tillgängliga:

| **Kapaciteten** | **Basic** | **Generella** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden | En återställningspunkt inom kvarhållningsperioden |
| GEO-återställning från säkerhetskopior georeplikerad | Stöds inte | Infoga < 12 timmar<br/>Återställningspunktmålet < 1 h | Infoga < 12 timmar<br/>Återställningspunktmålet < 1 h |

> [!IMPORTANT]
> Om du tar bort servern tas även bort alla databaser som hör till servern och kan inte återställas. Du kan inte återställa en server som har tagits bort.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter en användare eller ett program fel

Du kan använda tjänstens säkerhetskopieringar för att återställa en server från olika störande händelser. En användare kan av misstag tar bort vissa data, oavsiktligt släppa ett viktigt tabell eller även en hel databas. Ett program kan av misstag över bra data med felaktiga data på grund av ett fel i programmet och så vidare.

Du kan utföra en punkt-i-tid-återställning för att skapa en kopia av din server till en känd bra i tid. Den här tidpunkten måste vara inom den tiden för lagring av säkerhetskopior du har konfigurerat för din server. När data har återställts till den nya servern, kan du ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera de nödvändiga data från den återställda servern till den ursprungliga servern.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Återställa från ett avbrott i Azure regionala data center

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar gör en business-avbrott som kan endast senaste några minuter, men kan senaste i timmar.

Ett alternativ är att vänta på att servern ska komma online igen när avbrottet data center är över. Detta fungerar för program som har råd att ha servern offline under en tidsperiod, till exempel en utvecklingsmiljö. När ett avbrott har datacenter, vet du inte hur länge kan det senaste avbrottet, så det här alternativet fungerar bara om du inte behöver din server för en stund.

Ett annat alternativ är att använda Azure-databasen för Postgresql's geo-återställning funktion som återställer servern med geo-redundant säkerhetskopieringar. Dessa säkerhetskopior är tillgängliga även om den region som din server är värd för är offline. Du kan återställa från dessa säkerhetskopieringar till en annan region och ta servern online igen.

> [!IMPORTANT]
> GEO-återställning är bara möjligt om du har etablerats på servern med geo-redundant lagring för säkerhetskopiering.

## <a name="next-steps"></a>Nästa steg
- Mer information om automatisk säkerhetskopiering finns [säkerhetskopieringar i Azure-databas för PostgreSQL](concepts-backup.md). 
- Om du vill återställa till en tidpunkt med hjälp av Azure portal finns [återställa databasen till en tidpunkt med hjälp av Azure portal](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI, se [återställa databasen till en tidpunkt med hjälp av CLI](howto-restore-server-cli.md).