---
title: Minimal avbrottstid migrering till Azure-databas för MySQL
description: Den här artikeln beskriver hur du utför en minimal avbrottstid migrering av en MySQL-databas till Azure-databas för MySQL genom att använda tjänsten Azure Database migrering.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: ecbd35bd45bd11292bbe4a032329d704858d4c77
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293929"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal avbrottstid migrering till Azure-databas för MySQL
Du kan utföra MySQL migrering till Azure-databas för MySQL med minimal avbrottstid genom att använda nyligen lanserade **kontinuerlig sync kapaciteten** för den [Azure migrering databastjänsten](https://aka.ms/get-dms) (DMS). Den här funktionen begränsar mängden nedtid som krävs av programmet.

## <a name="overview"></a>Översikt
DMS utför en inledande belastningen på din lokala till Azure-databas för MySQL och sedan synkroniserar kontinuerligt alla nya transaktioner till Azure medan programmet körs. När data fångar på målet Azure sida, du stoppa program under en kort stund (lägsta driftstopp), vänta tills den senaste batchen data (från den tidpunkt du stoppa programmet förrän programmet är effektivt tillgänglig för att ta någon ny trafik) att fånga upp i mål- och uppdatera sedan anslutningssträngen så att den pekar till Azure. När du är klar kommer programmet att live i Azure!

![Kontinuerlig synkronisering med tjänsten Azure Database migrering](./media/howto-migrate-online/ContinuousSync.png)

DMS migrering av MySQL källor är för närvarande under förhandsgranskning. Om du vill testa tjänsten för att migrera din MySQL-arbetsbelastningar som registrering via Azure-DMS [förhandsgranskningssidan](https://aka.ms/dms-preview) att uttrycka ditt intresse. Din feedback är ovärderlig hjälpa att ytterligare förbättra tjänsten.

## <a name="next-steps"></a>Nästa steg
- Visa videon [enkelt migrera MySQL/PostgreSQL hanterade appar till Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), som innehåller en demonstration som visar hur du migrerar MySQL appar till Azure-databas för MySQL.
- Registrera dig för begränsad förhandsversionen för minimal driftstörning migrering av MySQL till Azure-databas för MySQL via Azure-DMS [förhandsgranskningssidan](https://aka.ms/dms-preview).
