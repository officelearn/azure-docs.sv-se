---
title: Minsta driftstoppsmigrering till Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du utför en minimal driftstoppsmigrering av en PostgreSQL-databas till Azure Database for PostgreSQL - Single Server med hjälp av Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067517"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minsta driftstoppsmigrering till Azure Database för PostgreSQL - Single Server
Du kan utföra PostgreSQL-migreringar till Azure Database för PostgreSQL med minimal stilleståndstid med hjälp av den nyligen introducerade funktionen för **kontinuerlig synkronisering** för Azure Database [Migration Service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar mängden driftstopp som programmet har ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en första belastning av din lokala till Azure Database för PostgreSQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet körs. När data na hinner ikapp på azure-sidan för målet stoppar du programmet en kort stund (minsta driftstopp), väntar på den sista omgången data (från den tidpunkt då du stoppar programmet tills programmet faktiskt inte är tillgängligt för att ta någon ny trafik) för att fånga upp upp i målet och uppdatera sedan anslutningssträngen så att den pekar på Azure. När du är klar kommer ditt program att visas på Azure!

![Kontinuerlig synkronisering med tjänsten för migrering av Azure-databas](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa [videoappmodernisering med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demo som visar hur du migrerar PostgreSQL-appar till Azure Database för PostgreSQL.
- Se självstudien [Migrera PostgreSQL till Azure Database för PostgreSQL online med DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
