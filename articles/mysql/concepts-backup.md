---
title: Säkerhetskopiering och återställning - Azure Database för MySQL
description: Lär dig mer om automatiska säkerhetskopieringar och återställning av din Azure-databas för MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 3a6162bb381f4e54114e3cabbf138f5b1c6aaae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373021"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Säkerhetskopiering och återställning i Azure Database för MySQL

Azure Database for MySQL skapar automatiskt säkerhetskopiering av servern och lagrar dem i användarkonfigurerad lokalt redundant eller geouppsagd lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del av en strategi för affärskontinuitet eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for MySQL tar säkerhetskopior av datafilerna och transaktionsloggen. Beroende på den maximala lagringsstorleken som stöds tar vi antingen fullständiga och differentiella säkerhetskopior (4 TB max lagringsservrar) eller säkerhetskopiering av ögonblicksbilder (upp till 16 TB max lagringsservrar). Med dessa säkerhetskopior kan du återställa en server till valfri tidpunkt under den konfigurerade lagringsperioden för säkerhetskopiering. Standardlagringsperioden för säkerhetskopiering är sju dagar. Du kan [också konfigurera den upp](howto-restore-server-portal.md#set-backup-configuration) till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Dessa säkerhetskopieringsfiler är inte användarexponerade och kan inte exporteras. Dessa säkerhetskopior kan endast användas för återställningsåtgärder i Azure Database för MySQL. Du kan använda [mysqldump](concepts-migrate-dump-restore.md) för att kopiera en databas.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

I allmänhet sker fullständiga säkerhetskopior varje vecka, differentiella säkerhetskopior sker två gånger om dagen för servrar med en lagring med max stöd på 4 TB. Säkerhetskopiering av ögonblicksbilder görs minst en gång per dag för servrar som har stöd för upp till 16 TB lagringsutrymme. I båda fallen säkerhetskopieras transaktionsloggar var femte minut. Den första ögonblicksbilden av fullständig säkerhetskopiering schemaläggs omedelbart efter att en server har skapats. Den första fullständiga säkerhetskopian kan ta längre tid på en stor återställd server. Den tidigaste tidpunkt då en ny server kan återställas till är den tidpunkt då den första fullständiga säkerhetskopieringen är klar. Eftersom ögonblicksbilder är momentana kan servrar med stöd för upp till 16 TB lagringsutrymme återställas hela vägen tillbaka till skapa tid.

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhetskopiering

Azure Database for MySQL ger flexibiliteten att välja mellan lokalt redundant eller geo-redundant säkerhetskopieringslagring på nivåerna Allmänt ändamål och Minne optimerad. När säkerhetskopiorna lagras i geouppsagbar lagring av säkerhetskopior lagras de inte bara i den region där servern finns, utan replikeras också till ett [parkopplat datacenter](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant lagring av säkerhetskopiering.

> [!IMPORTANT]
> Det är bara tillåtet att konfigurera lokalt redundant eller geouppsagbar lagring för säkerhetskopiering under serverskapningen. När servern har etablerats kan du inte ändra redundansalternativet för säkerhetskopieringslagring.

### <a name="backup-storage-cost"></a>Kostnad för lagring av säkerhetskopiering

Azure Database for MySQL tillhandahåller upp till 100 % av din etablerade serverlagring som lagring av säkerhetskopior utan extra kostnad. Vanligtvis är detta lämpligt för en lagring av säkerhetskopiering på sju dagar. All ytterligare säkerhetskopieringslagring som används debiteras i GB-månad.

Om du till exempel har etablerat en server med 250 GB har du 250 GB lagringsutrymme för säkerhetskopiering utan extra kostnad. Lagring som överstiger 250 GB debiteras.

## <a name="restore"></a>Återställ

I Azure Database for MySQL skapar en återställning en ny server från den ursprungliga serverns säkerhetskopior och alla databaser som finns på servern återställs.

Det finns två typer av återställning:

- **Point-in-time-återställning** är tillgänglig med antingen säkerhetskopieringsredundansalternativet och skapar en ny server i samma region som den ursprungliga servern som använder kombinationen av fullständiga säkerhetskopior och transaktionsloggsäkerheter.
- **Geoåterställning** är bara tillgängligt om du har konfigurerat servern för geouppsagbar lagring och du kan återställa servern till en annan region med den senaste säkerhetskopian.

Den beräknade återställningstiden beror på flera faktorer, inklusive databasstorlekar, transaktionsloggstorlek, nätverksbandbredd och det totala antalet databaser som återställs i samma region samtidigt. Återhämtningstiden är vanligtvis mindre än 12 timmar.

> [!IMPORTANT]
> Det **går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. Om du vill skydda serverresurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar kan administratörer utnyttja [hanteringslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av ditt redundansalternativ för säkerhetskopiering kan du utföra en återställning till vilken tidpunkt som helst inom lagringsperioden för säkerhetskopiering. En ny server skapas i samma Azure-region som den ursprungliga servern. Den skapas med den ursprungliga serverns konfiguration för prissättningsnivån, beräkningsgenerering, antal virtuella kärnor, lagringsstorlek, lagringsperiod för säkerhetskopiering och säkerhetskopieringsredundansalternativet.

Point-in-time-återställning är användbart i flera scenarier. Till exempel när en användare av misstag tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett programfel.

Du kan behöva vänta på att nästa transaktionsloggsäkerhetskopiering ska göras innan du kan återställa till en tidpunkt inom de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhetskopior. Servrar som stöder upp till 4 TB lagringsutrymme kan återställas till den geoparade regionen eller till en region som stöder upp till 16 TB lagringsutrymme. För servrar som stöder upp till 16 TB lagringsutrymme kan geosäkerhetskopior återställas i alla regioner som stöder 16 TB-servrar. Granska [Azure Database for MySQL-prisnivåer](concepts-pricing-tiers.md) för listan över regioner som stöds.

Geoåterställning är standardalternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där servern finns. Om en storskalig incident i en region leder till att databasprogrammet inte är tillgängliga kan du återställa en server från de geouppsagda säkerhetskopiorna till en server i någon annan region. Geo-återställning använder den senaste säkerhetskopian av servern. Det finns en fördröjning mellan när en säkerhetskopia tas och när den replikeras till olika regioner. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det finnas upp till en timmes dataförlust.

Under geo-återställning, de serverkonfigurationer som kan ändras inkluderar beräkningsgenerering, vCore, säkerhetskopiering kvarhållningsperiod och säkerhetskopiering redundans alternativ. Det går inte att ändra prisnivån (Basic, General Purpose eller Memory Optimized) eller lagringsstorlek under geoåterställning.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från någon av återställningsmekanismerna bör du utföra följande uppgifter för att få igång användare och program igen:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klientprogram till den nya servern
- Se till att det finns lämpliga VNet-regler för användare att ansluta. Dessa regler kopieras inte över från den ursprungliga servern.
- Se till att lämpliga inloggningar och behörigheter på databasnivå finns på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Mer information om kontinuitet i verksamheten finns i [översikten över kontinuitet](concepts-business-continuity.md)i verksamheten .
- Information om hur du återställer till en punkt i tid med Azure-portalen finns [i återställa servern till en punkt i tid med Azure-portalen](howto-restore-server-portal.md).
- Information om hur du återställer till en punkt i tid med Azure CLI finns [i återställa servern till en point-in-time med CLI](howto-restore-server-cli.md).
