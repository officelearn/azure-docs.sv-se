---
title: Säkerhets kopiering och återställning – Azure Database for MySQL
description: Lär dig mer om automatisk säkerhets kopiering och att återställa Azure Database for MySQL-servern.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 47fa4083c26f18149b0b69b05f2cfd0b227de868
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619579"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Säkerhets kopiering och återställning i Azure Database for MySQL

Azure Database for MySQL skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhets kopiering och återställning är en viktig del av en strategi för affärs kontinuitet eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for MySQL säkerhetskopierar datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds tar vi antingen fullständig och differentiell säkerhets kopiering (4 TB max lagrings servrar) eller säkerhets kopior av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan [också konfigurera det](howto-restore-server-portal.md#set-backup-configuration) upp till 35 dagar. Alla säkerhets kopior krypteras med AES 256-bitars kryptering.

Det går inte att exportera de här säkerhetskopierade filerna. Säkerhets kopieringarna kan bara användas för återställnings åtgärder i Azure Database for MySQL. Du kan använda [mysqldump](concepts-migrate-dump-restore.md) för att kopiera en databas.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

I allmänhet sker fullständiga säkerhets kopieringar varje vecka, differentiella säkerhets kopieringar sker två gånger per dag för servrar med en högsta lagring på 4 TB som stöds. Säkerhetskopieringar av ögonblicksbilder sker minst en gång per dag för servrar som har stöd för upp till 16 TB lagringsutrymme. I båda fallen säkerhetskopieras transaktionsloggar var femte minut. Den första ögonblicks bilden av fullständig säkerhets kopiering schemaläggs direkt efter att en server har skapats. Den första fullständiga säkerhets kopieringen kan ta längre tid på en stor återställd Server. Den tidigaste tidpunkt som en ny server kan återställas till är den tid då den första fullständiga säkerhets kopieringen är klar. När ögonblicks bilder är instantanious kan servrar med stöd för upp till 16 TB lagrings utrymme återställas till skapande tiden igen.

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhets kopiering

Azure Database for MySQL ger flexibiliteten att välja mellan lokalt redundant eller Geo-redundant lagring av säkerhets kopior i Generell användning och minnesoptimerade nivåer. När säkerhets kopiorna lagras i Geo-redundant lagring av säkerhets kopior lagras de inte bara i den region där servern finns, men replikeras också till ett [parat Data Center](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant säkerhets kopierings lagring.

> [!IMPORTANT]
> Det går bara att konfigurera lokalt redundant eller Geo-redundant lagring för säkerhets kopiering när servern skapas. När servern har tillhandahållits kan du inte ändra redundans alternativet för lagring av säkerhets kopior.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Azure Database for MySQL tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Detta är vanligt vis lämpligt för kvarhållning av säkerhets kopior på sju dagar. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB-månad.

Om du till exempel har etablerad en server med 250 GB har du 250 GB lagring av säkerhets kopior utan extra kostnad. Lagring som överstiger 250 GB debiteras.

## <a name="restore"></a>Återställ

I Azure Database for MySQL skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior.

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

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhets kopieringar. Servrar som har stöd för upp till 4 TB lagrings utrymme kan återställas till den geo-kopplade regionen eller till en region som har stöd för upp till 16 TB lagring. För servrar som har stöd för upp till 16 TB lagrings utrymme kan geo-säkerhetskopieringar återställas i valfri region som har stöd för 16 TB-servrar. Granska [Azure Database for MySQL pris nivåer](concepts-pricing-tiers.md) för listan över regioner som stöds.

Geo-återställning är standard alternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där-servern finns. Om en storskalig incident i en region resulterar i att databas programmet inte är tillgängligt, kan du återställa en server från de geo-redundanta säkerhets kopieringarna till en server i någon annan region. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den replikeras till en annan region. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det vara upp till en timmes data förlust.

Vid geo-återställning kan de serverkonfigurationer som kan ändras omfatta beräknings generering, vCore, bevarande period för säkerhets kopior och alternativ för säkerhets kopiering. Det finns inte stöd för att ändra pris nivå (Basic, Generell användning eller Minnesoptimerade) eller lagrings storlek under geo-återställning.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från återställnings metoden bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern
- Se till att det finns lämpliga brand Väggs regler på server nivå för att användare ska kunna ansluta
- Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Mer information om verksamhets kontinuitet finns i [Översikt över affärs kontinuitet](concepts-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure Portal, se [restore Database till en tidpunkt med hjälp av Azure Portal](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI, se [restore Database till en tidpunkt med hjälp av CLI](howto-restore-server-cli.md).
