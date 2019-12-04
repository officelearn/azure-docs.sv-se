---
title: Lägsta-nedtid-migrering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du utför en migrering med minimalt avbrott i en MySQL-databas till Azure Database for MySQL med hjälp av Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774151"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal-nedtid-migrering till Azure Database for MySQL
Du kan utföra MySQL-migreringar för att Azure Database for MySQL med minimal nedtid genom att använda den nyligen introducerade **kontinuerliga synkroniseringen** av [Azure Database migration service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar den mängd stillestånds tid som programmet ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsning av din lokala plats för Azure Database for MySQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet fortsätter att köras. När data har fångats upp på Azure-sidan, stoppar du programmet under en kort stund (minsta stillestånds tid), väntar på den senaste data insamlingen (från den tidpunkt då du avbryter programmet tills programmet är effektivt otillgängligt för att ta ny trafik) för att fånga i målet och uppdatera sedan anslutnings strängen så att den pekar på Azure. När du är färdig är ditt program Live på Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/postgresql-appar till Azure Managed Service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database for MySQL.
- Se självstudien [migrera MySQL till Azure Database for MySQL online med DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
