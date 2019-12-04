---
title: Hög tillgänglighet – Azure Database for MariaDB
description: Det här avsnittet innehåller information om hög tillgänglighet när du använder Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 797a94a561351ac7f5317f2f215b56f6944c023f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772534"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Koncept med hög tillgänglighet i Azure Database for MariaDB
Tjänsten Azure Database for MariaDB ger en garanterad hög tillgänglighets nivå. Det ekonomiskt åtgärdade service nivå avtalet (SLA) är 99,99% vid allmän tillgänglighet. Det finns praktiskt taget ingen programs otillgänglig tid när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Modellen med hög tillgänglighet (HA) baseras på inbyggda metoder för växling vid fel när ett avbrott uppstår på radnivå. En avbrotts tid på radnivå kan inträffa på grund av ett maskin varu fel eller som svar på en tjänst distribution.

Vid alla tillfällen sker ändringar som gjorts i en Azure Database for MariaDB databas server i samband med en transaktion. Ändringar registreras synkront i Azure Storage när transaktionen har genomförts. Om ett avbrott på radnivå uppstår skapar databas servern automatiskt en ny nod och kopplar data lagring till den nya noden. Alla aktiva anslutningar släpps och eventuella synlighetssekvensnummer transaktioner är inte allokerade.

## <a name="application-retry-logic-is-essential"></a>Logik för omprövning av program är viktigt
Det är viktigt att MariaDB Database-program har skapats för att identifiera och försöka ansluta som har släppts, och transaktioner som misslyckades. När programmet försöker igen omdirigeras programmets anslutning transparent till den nyss skapade instansen, som tar över för den misslyckade instansen.

Internt i Azure används en gateway för att omdirigera anslutningarna till den nya instansen. Vid en avbrotts process tar hela processen normalt på flera sekunder. Eftersom omdirigeringen hanteras internt av gatewayen, förblir den externa anslutnings strängen densamma för klient programmen.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
Precis som för HA-modellen skapas en ny Server instans med den angivna storleken när en Azure Database for MariaDB skalas upp eller ned. Den befintliga data lagringen kopplas från den ursprungliga instansen och kopplas till den nya instansen.

Under skalnings åtgärden sker ett avbrott i databas anslutningarna. Klient programmen är frånkopplade och öppna ej allokerade transaktioner avbryts. När klient programmet försöker ansluta igen eller skapar en ny anslutning, dirigerar gatewayen anslutningen till den nya instansen.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for MariaDB översikt](overview.md)
