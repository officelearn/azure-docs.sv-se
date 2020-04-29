---
title: Säkerhets kopiering och återställning – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om automatisk säkerhets kopiering och att återställa Azure Database for PostgreSQL Server – en server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77619630"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Säkerhets kopiering och återställning i Azure Database for PostgreSQL-enskild server

Azure Database for PostgreSQL skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhets kopiering och återställning är en viktig del av en strategi för affärs kontinuitet eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for PostgreSQL säkerhetskopierar datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds tar vi antingen fullständig och differentiell säkerhets kopiering (4 TB max lagrings servrar) eller säkerhets kopior av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhets kopior krypteras med AES 256-bitars kryptering.

Det går inte att exportera de här säkerhetskopierade filerna. Säkerhets kopieringarna kan bara användas för återställnings åtgärder i Azure Database for PostgreSQL. Du kan använda [pg_dump](howto-migrate-using-dump-and-restore.md) för att kopiera en databas.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

I allmänhet sker fullständiga säkerhets kopieringar varje vecka, differentiella säkerhets kopieringar sker två gånger per dag för servrar med en högsta lagring på 4 TB som stöds. Säkerhetskopiering av ögonblicksbilder görs minst en gång per dag för servrar som har stöd för upp till 16 TB lagringsutrymme. I båda fallen säkerhetskopieras transaktionsloggar var femte minut. Den första ögonblicks bilden av fullständig säkerhets kopiering schemaläggs direkt efter att en server har skapats. Den första fullständiga säkerhets kopieringen kan ta längre tid på en stor återställd Server. Den tidigaste tidpunkt som en ny server kan återställas till är den tid då den första fullständiga säkerhets kopieringen är klar. När ögonblicks bilder tas i gång kan servrar med stöd för upp till 16 TB lagrings utrymme återställas till skapande tiden igen.

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhets kopiering

Azure Database for PostgreSQL ger flexibiliteten att välja mellan lokalt redundant eller Geo-redundant lagring av säkerhets kopior i Generell användning och minnesoptimerade nivåer. När säkerhets kopiorna lagras i Geo-redundant lagring av säkerhets kopior lagras de inte bara i den region där servern finns, men replikeras också till ett [parat Data Center](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant säkerhets kopierings lagring.

> [!IMPORTANT]
> Det går bara att konfigurera lokalt redundant eller Geo-redundant lagring för säkerhets kopiering när servern skapas. När servern har tillhandahållits kan du inte ändra redundans alternativet för lagring av säkerhets kopior.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Azure Database for PostgreSQL tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Detta är vanligt vis lämpligt för kvarhållning av säkerhets kopior på sju dagar. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB-månad.

Om du till exempel har etablerad en server med 250 GB har du 250 GB lagring av säkerhets kopior utan extra kostnad. Lagring som överstiger 250 GB debiteras.

## <a name="restore"></a>Återställ

I Azure Database for PostgreSQL skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior.

Det finns två typer av återställning:

- **Återställning av tidpunkt** är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern.
- **Geo-återställning** är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av ditt alternativ för säkerhets kopiering kan du utföra en återställning till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna. En ny server skapas i samma Azure-region som den ursprungliga servern. Den skapas med den ursprungliga serverns konfiguration för pris nivån, beräknings generering, antalet virtuella kärnor, lagrings storlek, kvarhållning av säkerhets kopior och alternativet för redundans.

Återställning av tidpunkt är användbart i flera scenarier. Till exempel när en användare oavsiktligt tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett program fel.

Du kan behöva vänta tills nästa säkerhets kopiering av transaktions loggen tas innan du kan återställa till en tidpunkt under de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhets kopieringar. Servrar som har stöd för upp till 4 TB lagrings utrymme kan återställas till den geo-kopplade regionen eller till en region som har stöd för upp till 16 TB lagring. För servrar som har stöd för upp till 16 TB lagrings utrymme kan geo-säkerhetskopieringar återställas i valfri region som har stöd för 16 TB-servrar. Granska [Azure Database för PostgeSQL-pris nivåer](concepts-pricing-tiers.md) för att visa en lista över regioner som stöds.

Geo-återställning är standard alternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där-servern finns. Om en storskalig incident i en region resulterar i att databas programmet inte är tillgängligt, kan du återställa en server från de geo-redundanta säkerhets kopieringarna till en server i någon annan region. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den replikeras till en annan region. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det vara upp till en timmes data förlust.

Vid geo-återställning kan de serverkonfigurationer som kan ändras omfatta beräknings generering, vCore, bevarande period för säkerhets kopior och alternativ för säkerhets kopiering. Det finns inte stöd för att ändra pris nivå (Basic, Generell användning eller Minnesoptimerade) eller lagrings storlek.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från återställnings metoden bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern
- Se till att det finns tillräckligt med brand Väggs-och VNet-regler på server nivå för att användarna ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga servern.
- Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md).
- Lär dig hur du återställer med hjälp [av Azure CLI](howto-restore-server-cli.md).
- Mer information om verksamhets kontinuitet finns i [Översikt över affärs kontinuitet](concepts-business-continuity.md).
