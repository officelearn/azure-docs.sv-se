---
title: Minimal stilleståndstid vid migrering till Azure Database for MySQL
description: Den här artikeln beskriver hur du utför en minimal stilleståndstid vid migrering av en MySQL-databas till Azure Database för MySQL med hjälp av Azure Database Migration Service.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 267212d8f832b96bf66145b97c3464471e28593d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014199"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal stilleståndstid vid migrering till Azure Database for MySQL
Du kan utföra MySQL-migrering till Azure Database för MySQL med minimal avbrottstid med hjälp av den Nyintroducerade **kapaciteten för kontinuerlig synkronisering** för den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar stilleståndstiden som krävs av programmet.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsningen av dina lokala till Azure Database för MySQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet är igång. När data kommer det ikapp på mål-Azure-sidan, du stoppa programmet under en kort stund (minimal stilleståndstid), vänta tills den senaste batchen av data (från den tidpunkt du stoppa programmet förrän programmet är ett effektivt sätt inte kan ta någon ny trafik) att fånga upp upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar, kommer programmet att live i Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/PostgreSQL appar till Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database för MySQL.
- Finns i självstudierna [migrera MySQL till Azure Database för MySQL med hjälp av DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
