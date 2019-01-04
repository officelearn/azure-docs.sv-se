---
title: Minimal stilleståndstid vid migrering till Azure Database for MySQL
description: Den här artikeln beskriver hur du utför en minimal stilleståndstid vid migrering av en MySQL-databas till Azure Database för MySQL med hjälp av Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 49e2662f215d845d416e46246b03e4408fae118b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543469"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal stilleståndstid vid migrering till Azure Database for MySQL
Du kan utföra MySQL-migrering till Azure Database för MySQL med minimal avbrottstid med hjälp av den Nyintroducerade **kapaciteten för kontinuerlig synkronisering** för den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar stilleståndstiden som krävs av programmet.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsningen av dina lokala till Azure Database för MySQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet är igång. När data kommer det ikapp på mål-Azure-sidan, du stoppa programmet under en kort stund (minimal stilleståndstid), vänta tills den senaste batchen av data (från den tidpunkt du stoppa programmet förrän programmet är ett effektivt sätt inte kan ta någon ny trafik) att fånga upp upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar, kommer programmet att live i Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/PostgreSQL appar till Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database för MySQL.
- Finns i självstudierna [migrera MySQL till Azure Database för MySQL med hjälp av DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
