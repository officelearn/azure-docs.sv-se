---
title: Säkerhetskopiering och återställning i Azure Database för PostgreSQL
description: Läs mer om automatisk säkerhetskopiering och återställning av Azure Database för PostgreSQL-server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 49277fc260743d0179c40374241edeece0a0e1e2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814341"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Säkerhetskopiering och återställning i Azure Database för PostgreSQL

Azure Database för PostgreSQL skapar säkerhetskopiering av servrar och lagrar dem i Användarkonfigurerad lokalt redundant eller geo-redundant lagring automatiskt. Säkerhetskopieringar kan användas för att återställa servern till point-in-time. Säkerhetskopiering och återställning är en viktig del av alla strategi för affärskontinuitet eftersom de skyddar dina data från oavsiktliga skador eller tas bort.

## <a name="backups"></a>Säkerhetskopior

Azure Database för PostgreSQL tar fullständig, differentiell och säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopior kan du återställa en server till valfri point-in-time inom din konfigurerade kvarhållningsperiod. Standard-kvarhållningsperiod är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopieringar krypteras med AES 256-bitars kryptering.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

I allmänhet fullständiga säkerhetskopieringar sker varje vecka, differentiella säkerhetskopieringar sker två gånger per dag och säkerhetskopieringar av transaktionsloggen inträffar var femte minut. Den första fullständiga säkerhetskopian schemaläggs omedelbart när en server har skapats. Den första säkerhetskopieringen kan ta längre tid på en stor återställda server. Den tidigaste tidpunkten som en ny server kan återställas till är den tid då den första fullständiga säkerhetskopieringen är klar.

### <a name="backup-redundancy-options"></a>Redundansalternativ för säkerhetskopiering

Azure Database för PostgreSQL ger dig flexibilitet att välja mellan lokalt redundant eller geo-redundant lagring generell användning och Minnesoptimerad nivåer av säkerhetskopior. När säkerhetskopiorna som lagras i geo-redundant lagring av säkerhetskopior kan de bara lagras inte i den region där din server är värd för, men replikeras också till en [kopplat Datacenter](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant lagring av säkerhetskopior.

> [!IMPORTANT]
> Konfigurera lokalt redundant eller geo-redundant lagring för säkerhetskopiering tillåts endast under server skapar. När servern har etablerats kan du inte ändra redundansalternativ för lagring av säkerhetskopior.

### <a name="backup-storage-cost"></a>Kostnaden för lagring av säkerhetskopior

Azure Database for PostgreSQL får upp till 100% av ditt etablerade serverutrymme som lagring av säkerhetskopior utan extra kostnad. Vanligtvis är lämplig för en kvarhållning av säkerhetskopior på sju dagar. Eventuella ytterligare säkerhetskopieringslagring används debiteras i GB-månad.

Om du har etablerat en server med 250 GB, har du till exempel 250 GB säkerhetskopieringslagring utan extra kostnad. Lagring som överstiger 250 GB debiteras.

## <a name="restore"></a>Återställ

I Azure Database för PostgreSQL skapar utför en återställning en ny server från den ursprungliga servern säkerhetskopior.

Det finns två typer av återställning:

- **Point-in-time-återställning** är tillgängliga med något av alternativen säkerhetskopieringsredundans och skapar en ny server i samma region som den ursprungliga servern.
- **GEO-återställning** är bara tillgängligt om du har konfigurerat din server för geo-redundant lagring och du kan återställa servern till en annan region.

Beräknad tid för återställning är beroende av flera faktorer, inklusive databasernas storlek, transaktionsloggarnas storlek, nätverkets bandbredd och det totala antalet databaser som återställs i samma region på samma gång. Tiden för återställning är vanligtvis mindre än 12 timmar.

> [!IMPORTANT]
> Ta bort servrar **kan** återställas. Om du tar bort servern tas även bort alla databaser som hör till servern och kan inte återställas. Om du vill skydda serverresurser efter distributionen, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanteringslås](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av din säkerhetskopiering redundansalternativ du utföra en återställning till valfri punkt i tiden i din kvarhållningsperiod. En ny server skapas i samma Azure-region som den ursprungliga servern. Den har skapats med den ursprungliga servern konfiguration för prisnivå, beräkningsgenerering och antalet virtuella kärnor, lagringsstorlek, kvarhållningsperiod för säkerhetskopiering och redundansalternativ för säkerhetskopiering.

Point-in-time-återställning är användbart i scenarier med flera. Till exempel när en användare av misstag tar bort data, släpper en viktig tabell eller databas, eller om ett program oavsiktligt skriver över värdefulla data med felaktiga data på grund av ett fel i programmet.

Du kan behöva vänta på Nästa säkerhetskopiering av transaktionsloggen som ska vidtas innan du kan återställa till en tidpunkt inom de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat din server för geo-redundanta säkerhetskopieringar. GEO-återställning är standardalternativet för återställning när servern är inte tillgänglig på grund av en incident i den region där servern finns. Om en storskalig incident i en region blir otillgänglig programmets databasen återställa du en server från geo-redundanta säkerhetskopieringar till en server i alla andra regioner. Det finns en fördröjning mellan när en säkerhetskopia görs och när de replikeras till annan region. Den här fördröjningen kan vara upp till en timme, så om en olycka inträffar, det kan ta upp till en timme dataförlust.

Under geo-återställning inkludera serverkonfigurationerna kan ändras compute-generering, vCore, kvarhållningsperiod för säkerhetskopiering och redundansalternativ för säkerhetskopiering. Ändra prisnivå (Basic, generell användning eller Minnesoptimerade) eller lagringsstorleken stöds inte.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från endera återställningsmetod bör du utföra följande uppgifter för att få dina användare och program igen igång:

- Om den nya servern är avsedd att ersätta den ursprungliga servern, omdirigera klienter och klientprogram till den nya servern
- Se till att lämpliga brandväggsregler på servernivå är på plats för användare att ansluta
- Se till att rätt inloggningar och databasnivåbehörigheter är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Läs mer om affärskontinuitet i den [översikt över affärskontinuitet](concepts-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure-portalen, se [återställa databasen till en tidpunkt med hjälp av Azure-portalen](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI, se [återställa databasen till en tidpunkt med hjälp av CLI](howto-restore-server-cli.md).
