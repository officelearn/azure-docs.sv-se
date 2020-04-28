---
title: Minimal-nedtid migrering till Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du utför en minimal stillestånds migrering av en PostgreSQL-databas till Azure Database for PostgreSQL-enskild server med hjälp av Azure Database Migration Service.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "65067517"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimal-nedtid migrering till Azure Database for PostgreSQL-enskild server
Du kan utföra PostgreSQL-migreringar för att Azure Database for PostgreSQL med minimal nedtid genom att använda den nyligen introducerade **kontinuerliga synkroniseringen** av [Azure Database migration service](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar den mängd stillestånds tid som programmet ådragit sig.

## <a name="overview"></a>Översikt
Azure DMS utför en inledande inläsning av din lokala plats för Azure Database for PostgreSQL och synkroniserar sedan kontinuerligt alla nya transaktioner till Azure medan programmet fortsätter att köras. När data har fångats upp på Azure-sidan, stoppar du programmet under en kort stund (minsta stillestånds tid), väntar på den senaste data insamlingen (från den tidpunkt då du avbryter programmet tills programmet är effektivt otillgängligt för att ta ny trafik) och sedan uppdatera anslutnings strängen så att den pekar på Azure. När du är färdig är ditt program Live på Azure!

![Kontinuerlig synkronisering med Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nästa steg
- Visa video [appens modernisering med Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), som innehåller en demo som visar hur du migrerar postgresql-appar till Azure Database for PostgreSQL.
- Se själv studie kursen [migrera postgresql till Azure Database for PostgreSQL online med DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
