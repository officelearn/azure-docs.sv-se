---
title: Minimal stilleståndstid vid migrering till Azure Database for MySQL
description: Den här artikeln beskriver hur du utför en minimal stilleståndstid vid migrering av en MySQL-databas till Azure Database för MySQL med hjälp av Azure Database Migration Service.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: cb2e1a34748875de9c7f8dd59be27c408ec82818
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090339"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal stilleståndstid vid migrering till Azure Database for MySQL
Du kan utföra MySQL-migrering till Azure Database för MySQL med minimal avbrottstid med hjälp av den Nyintroducerade **kapaciteten för kontinuerlig synkronisering** för den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar stilleståndstiden som krävs av programmet.

## <a name="overview"></a>Översikt
DMS utför en inledande inläsningen av dina lokala till Azure Database för MySQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet är igång. När data kommer det ikapp på mål-Azure-sidan, du stoppa programmet under en kort stund (minimal stilleståndstid), vänta tills den senaste batchen av data (från den tidpunkt du stoppa programmet förrän programmet är ett effektivt sätt inte kan ta någon ny trafik) att fånga upp upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar, kommer programmet att live i Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

- DMS migrering av MySQL-källor finns för närvarande i offentlig förhandsversion. Om du vill prova att migrera dina arbetsbelastningar för MySQL-tjänsten kan du gå vidare i portalen. Din feedback är ovärderlig gäller att hjälpa att ytterligare förbättra tjänsten.

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/PostgreSQL appar till Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database för MySQL.
- Finns i självstudierna [migrera MySQL till Azure Database för MySQL med hjälp av DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
