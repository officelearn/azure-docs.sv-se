---
title: Säkerhetskopiering och återställning i Azure-databas för PostgreSQL
description: Läs mer om automatisk säkerhetskopiering och återställning av databasen Azure för PostgreSQL-servern.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0f7ec38d2c271ebaa15e681a71eb32be7151921f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
ms.locfileid: "29693363"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Säkerhetskopiering och återställning i Azure-databas för PostgreSQL

Azure PostgreSQL-databas skapas server säkerhetskopieras automatiskt och lagrar dem med användare som konfigurerats lokalt redundant eller geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en i tidpunkt. Säkerhetskopiering och återställning är en viktig del av alla affärskontinuitetsstrategi eftersom de skyddar dina data från oavsiktliga skador eller tas bort.

## <a name="backups"></a>Säkerhetskopior

Azure-databas för PostgreSQL tar full och differentiella säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopior kan du återställa en server till någon i tidpunkt inom den konfigurerade säkerhetskopiering kvarhållningsperioden. Loggperioden för säkerhetskopian är sju dagar. Du kan också konfigurera den att 35 dagar. Alla säkerhetskopior är krypterad med AES 256-bitars kryptering.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

I allmänhet fullständig säkerhetskopiering varje vecka, sker differentiella säkerhetskopieringar inträffar två gånger per dag och säkerhetskopieringar av transaktionsloggen sker var femte minut. Den första kompletta säkerhetskopian kommer omedelbart när en server har skapats. Den första säkerhetskopian kan på en stor återställda server ta längre tid. Den tidigaste tidpunkten som en ny server kan återställas till är den tid då den första fullständiga säkerhetskopieringen har slutförts.

### <a name="backup-redundancy-options"></a>Redundansalternativ för säkerhetskopiering

Azure-databas för PostgreSQL ger möjlighet att välja mellan lokalt redundant eller geo-redundant lagring i generella och Minnesoptimerade nivåer för säkerhetskopiering. När säkerhetskopiorna lagras i geo-redundant lagring för säkerhetskopiering, de bara lagras inte i den region där din server är värd för, men också replikeras till en [parad Datacenter](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjligheten att återställa servern i en annan region vid en katastrof. Den grundläggande nivån erbjuder endast lokalt redundant lagring för säkerhetskopiering.

> [!IMPORTANT]
> Konfigurera lokalt redundant eller geo-redundant lagring för säkerhetskopiering är endast tillåtet under server skapar. När servern är etablerad kan du inte ändra alternativet säkerhetskopieringslagring redundans.

### <a name="backup-storage-cost"></a>Kostnaden för lagring för säkerhetskopiering

Azure-databas för PostgreSQL tillhandahåller upp till 100% etablerade server-utrymme som säkerhetskopieringslagring utan extra kostnad. Detta är vanligtvis lämpar sig för en säkerhetskopiering kvarhållning av sju dagar. All ytterligare lagring för säkerhetskopiering som används debiteras i GB-månad.

Om du har etablerat en server med 250 GB, har du till exempel 250 GB lagring för säkerhetskopiering utan extra kostnad. Lagring utöver 250 GB debiteras.

## <a name="restore"></a>Återställ

I Azure-databas för PostgreSQL skapar utför en återställning en ny server från den ursprungliga servern säkerhetskopior.

Det finns två typer av återställning:

- **Point-in-time-återställning** är tillgänglig med antingen säkerhetskopiering redundans-alternativet och skapar en ny server i samma region som den ursprungliga servern.
- **GEO-återställning** är bara tillgängligt om du har konfigurerat din server för geo-redundant lagring och du kan återställa servern till en annan region.

Beräknad tid för återställning beror på flera faktorer, inklusive databas storlekar, storleken på transaktionsloggen, nätverkets bandbredd och det totala antalet databaser som återställer i samma region på samma gång. Tiden för återställning är vanligtvis mindre än 12 timmar.

> [!IMPORTANT]
> Om du tar bort servern tas även bort alla databaser som hör till servern och kan inte återställas. Du kan inte återställa en server som har tagits bort.

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av säkerhetskopiering redundans-alternativet du utför en återställning till en punkt i tiden i din säkerhetskopiering Bevarandeperiod. En ny server skapas i samma Azure-region som den ursprungliga servern. Den har skapats med den ursprungliga servern konfiguration för prisnivån, compute generation, antalet vCores, lagringsutrymme, säkerhetskopiering Bevarandeperiod och säkerhetskopiering redundans alternativet.

Point-in-time-återställning är användbart i scenarier med flera. Till exempel när en användare av misstag tar bort data, släpper en viktig tabell eller databasen, eller om ett program oavsiktligt skriver över bra data med felaktiga data på grund av ett fel i programmet.

Du kan behöva vänta tills nästa transaction loggsäkerhetskopieringen som ska vidtas innan du kan återställa till en tidpunkt inom de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat din server för geo-redundant säkerhetskopieringar. GEO-återställning är standardalternativet när servern är inte tillgänglig på grund av en incident i den region där servern finns. Om en storskalig incident i en region resulterar i att programmets databasen kan du återställa en server från geo-redundant säkerhetskopieringar till en server i en annan region. Det finns en fördröjning mellan när en säkerhetskopia görs och när de replikeras till annan region. Den här fördröjningen kan vara upp till en timme, så om en olycka inträffar, det kan vara upp till en timme förlust av data.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från antingen funktion, bör du utföra följande uppgifter för att få användarna och program tillbaka igång:

- Om den nya servern är tänkt att ersätta den ursprungliga servern omdirigera klienter och program till den nya servern
- Se till lämpliga servernivå brandväggsregler för användare att ansluta
- Se till att lämpliga inloggningar och nivå databasbehörighet är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Mer information om kontinuitet för företag finns i [översikt över verksamhetskontinuitet](concepts-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure portal finns [återställa databasen till en tidpunkt med hjälp av Azure portal](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI, se [återställa databasen till en tidpunkt med hjälp av CLI](howto-restore-server-cli.md).