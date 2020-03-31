---
title: Migrering med minimal stilleståndstid – Azure Database för MySQL
description: I den här artikeln beskrivs hur du utför en minimal driftstoppsmigrering av en MySQL-databas till Azure Database for MySQL med hjälp av Migreringstjänsten för Azure Database.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063346"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minsta driftstoppsmigrering till Azure Database för MySQL
Du kan utföra MySQL-migreringar till Azure Database for MySQL med minimal stilleståndstid med hjälp av den nyligen introducerade funktionen för **kontinuerlig synkronisering** för Azure Database [Migration Service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar mängden driftstopp som programmet har ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en första belastning av din lokala till Azure Database för MySQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet körs. När data na hinner ikapp på azure-sidan för målet stoppar du programmet en kort stund (minsta driftstopp), väntar på den sista omgången data (från den tidpunkt då du stoppar programmet tills programmet faktiskt inte är tillgängligt för att ta någon ny trafik) för att fånga upp upp i målet och uppdatera sedan anslutningssträngen så att den pekar på Azure. När du är klar kommer ditt program att visas på Azure!

![Kontinuerlig synkronisering med tjänsten för migrering av Azure-databas](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [Migrera enkelt MySQL/PostgreSQL-appar till Azure-hanterad tjänst](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database för MySQL.
- Se självstudien [Migrera MySQL till Azure Database för MySQL online med DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
