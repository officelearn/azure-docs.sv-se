---
title: Minimal-nedtid migrering till Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du utför en minimal stillestånds migrering av en PostgreSQL-databas till Azure Database for PostgreSQL-enskild server med hjälp av Azure Database Migration Service.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1f1af53388f177dc348c5cb805ef8e6fbe9f9436
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710829"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimal-nedtid migrering till Azure Database for PostgreSQL-enskild server
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

Du kan utföra PostgreSQL-migreringar för att Azure Database for PostgreSQL med minimal nedtid genom att använda den nyligen introducerade **kontinuerliga synkroniseringen** av [Azure Database migration service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar den mängd stillestånds tid som programmet ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsning av din lokala plats för Azure Database for PostgreSQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet fortsätter att köras. När data har fångats upp på Azure-sidan, stoppar du programmet under en kort stund (minsta stillestånds tid), väntar på den senaste data insamlingen (från den tidpunkt då du avbryter programmet tills programmet är effektivt otillgängligt för att ta ny trafik) och sedan uppdatera anslutnings strängen så att den pekar på Azure. När du är färdig är ditt program Live på Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Kontinuerlig synkronisering med Azure Database Migration Service":::

## <a name="next-steps"></a>Nästa steg
- Visa video [appens modernisering med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demo som visar hur du migrerar postgresql-appar till Azure Database for PostgreSQL.
- Se själv studie kursen [migrera postgresql till Azure Database for PostgreSQL online med DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
