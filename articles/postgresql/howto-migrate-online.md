---
title: Minimal stilleståndstid vid migrering till Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver hur du utför en minimal stilleståndstid vid migrering av en PostgreSQL-databas till Azure Database för PostgreSQL – enskild Server med hjälp av Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067517"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimal stilleståndstid vid migrering till Azure Database för PostgreSQL – enskild Server
Du kan utföra PostgreSQL migrering till Azure Database för PostgreSQL med minimal avbrottstid med hjälp av den Nyintroducerade **kapaciteten för kontinuerlig synkronisering** för den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Den här funktionen begränsar stilleståndstiden som krävs av programmet.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsningen av dina lokala till Azure Database för PostgreSQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet är igång. När data kommer det ikapp på mål-Azure-sidan, du stoppa programmet under en kort stund (minimal stilleståndstid), vänta tills den senaste batchen av data (från den tidpunkt du stoppa programmet förrän programmet är ett effektivt sätt inte kan ta någon ny trafik) att fånga upp upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar, kommer programmet att live i Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [Appmodernisering med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demo som visar hur du migrerar PostgreSQL appar till Azure Database för PostgreSQL.
- Finns i självstudierna [migrera PostgreSQL till Azure Database för PostgreSQL med hjälp av DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
