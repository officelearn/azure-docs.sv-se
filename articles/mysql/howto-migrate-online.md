---
title: Lägsta-nedtid-migrering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du utför en migrering med minimalt avbrott i en MySQL-databas till Azure Database for MySQL med hjälp av Azure Database Migration Service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 4c7fe3db3f85824dc2cfbe87fc22b63a59e931ab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546372"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal-nedtid-migrering till Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Du kan utföra MySQL-migreringar för att Azure Database for MySQL med minimal nedtid genom att använda den nyligen introducerade **kontinuerliga synkroniseringen** av [Azure Database migration service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar den mängd stillestånds tid som programmet ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsning av din lokala plats för Azure Database for MySQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet fortsätter att köras. När data har fångats upp på Azure-sidan, stoppar du programmet under en kort stund (minsta stillestånds tid), väntar på den senaste data insamlingen (från den tidpunkt då du avbryter programmet tills programmet är effektivt otillgängligt för att ta ny trafik) och sedan uppdatera anslutnings strängen så att den pekar på Azure. När du är färdig är ditt program Live på Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Kontinuerlig synkronisering med Azure Database Migration Service":::

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/postgresql-appar till Azure Managed Service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demo som visar hur du migrerar MySQL-appar till Azure Database for MySQL.
- Se självstudien [migrera MySQL till Azure Database for MySQL online med DMS](../dms/tutorial-mysql-azure-mysql-online.md).