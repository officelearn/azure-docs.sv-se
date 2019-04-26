---
title: Minimal stilleståndstid vid migrering till Azure Database för PostgreSQL
description: Den här artikeln beskriver hur du utför en minimal stilleståndstid vid migrering av en PostgreSQL-databas till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421254"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Minimal stilleståndstid vid migrering till Azure Database för PostgreSQL
Du kan utföra PostgreSQL migrering till Azure Database för PostgreSQL med minimal avbrottstid med hjälp av den Nyintroducerade **kapaciteten för kontinuerlig synkronisering** för den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Den här funktionen begränsar stilleståndstiden som krävs av programmet.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsningen av dina lokala till Azure Database för PostgreSQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet är igång. När data kommer det ikapp på mål-Azure-sidan, du stoppa programmet under en kort stund (minimal stilleståndstid), vänta tills den senaste batchen av data (från den tidpunkt du stoppa programmet förrän programmet är ett effektivt sätt inte kan ta någon ny trafik) att fånga upp upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar, kommer programmet att live i Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [Appmodernisering med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demo som visar hur du migrerar PostgreSQL appar till Azure Database för PostgreSQL.
- Finns i självstudierna [migrera PostgreSQL till Azure Database för PostgreSQL med hjälp av DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
