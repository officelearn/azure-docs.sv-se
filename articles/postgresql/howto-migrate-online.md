---
title: Minimal avbrottstid migrering till Azure-databas för PostgreSQL
description: Den här artikeln beskriver hur du utför en minimal avbrottstid migrering av en PostgreSQL-databas till Azure-databas för PostgreSQL genom att använda tjänsten Azure Database migrering.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9ab5d4615a8baf763d0b7ee47bf0890124f8665c
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292550"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Minimal avbrottstid migrering till Azure-databas för PostgreSQL
Du kan utföra PostgreSQL migrering till Azure-databas för PostgreSQL med minimal avbrottstid genom att använda nyligen lanserade **kontinuerlig sync kapaciteten** för den [Azure migrering databastjänsten](https://aka.ms/get-dms) (DMS) . Den här funktionen begränsar mängden nedtid som krävs av programmet.

## <a name="overview"></a>Översikt
DMS utför en inledande belastningen på din lokala till Azure-databas för PostgreSQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet körs. När data fångar på målet Azure sida, du stoppa program under en kort stund (lägsta driftstopp), vänta tills den senaste batchen data (från den tidpunkt du stoppa programmet förrän programmet är effektivt tillgänglig för att ta någon ny trafik) att fånga upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar kommer programmet att live i Azure!

![Kontinuerlig synkronisering med tjänsten Azure Database migrering](./media/howto-migrate-online/ContinuousSync.png)

DMS migrering av PostgreSQL källor är för närvarande under förhandsgranskning. Om du vill testa tjänsten för att migrera PostgreSQL-arbetsbelastningar som registrering via Azure-DMS [förhandsgranskningssidan](https://aka.ms/dms-preview) att uttrycka ditt intresse. Din feedback är ovärderlig hjälpa att ytterligare förbättra tjänsten.

## <a name="next-steps"></a>Nästa steg
- Visa videon [modernisering App med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demonstration som visar hur du migrerar PostgreSQL appar till Azure-databas för PostgreSQL.
- Registrera dig för begränsad förhandsversionen för minimal driftstörning migrering av PostgreSQL till Azure-databas för PostgreSQL via Azure-DMS [förhandsgranskningssidan](https://aka.ms/dms-preview).
