---
title: Konfigurera hög tillgänglighet – storskalig (citus) – Azure Database for PostgreSQL
description: Så här aktiverar eller inaktiverar du hög tillgänglighet
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f08fb01cf26ddf73d31b575242b27d7d8b4017d9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033291"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurera storskalig (citus) hög tillgänglighet

Azure Database for PostgreSQL-storskalig (citus) ger hög tillgänglighet (HA) för att undvika avbrott i databasen. När HA Aktiver ATS kommer varje nod i en Server grupp att få ett vänte läge. Om den ursprungliga noden blir ohälsosam, kommer vänte läge att höjas för att ersätta den.

> [!IMPORTANT]
> Eftersom HA dubbelt så många servrar i gruppen kommer det också att dubblera kostnaden.

Det går att aktivera HA när Server gruppen skapas, eller efteråt i fliken **Compute + Storage** för Server gruppen i Azure Portal. Användar gränssnittet ser ut ungefär som i båda fallen. Dra skjutreglaget för **hög tillgänglighet** från nej till Ja:

![ha-skjutreglage](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Klicka på knappen **Spara** för att tillämpa ditt val. Det kan ta lite tid att aktivera HA, eftersom Server gruppen tillhandahåller vänte läge och strömmar data till dem.

Fliken **Översikt** för Server gruppen visar en lista över alla noder och deras vänte läge, tillsammans med en kolumn med **hög tillgänglighet** som visar om ha Aktiver ATS för varje nod.

![kolumnen ha i Server grupp översikt](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Nästa steg

Lär dig mer om [hög tillgänglighet](concepts-hyperscale-high-availability.md).
