---
title: Konfigurera hög tillgänglighet – Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Aktivera eller inaktivera hög tillgänglighet
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977681"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurera hyperskala (Citus) hög tillgänglighet

Azure Database for PostgreSQL - Hyperscale (Citus) ger hög tillgänglighet (HA) för att undvika avbrott i databasen. Med HA aktiverat får varje nod i en servergrupp en vänteläge. Om den ursprungliga noden blir felaktig kommer dess vänteläge att befordras för att ersätta den.

> [!IMPORTANT]
> Eftersom HA fördubblar antalet servrar i gruppen, kommer det också att fördubbla kostnaden.

Det går att aktivera HA när servergruppen skapas eller efteråt på fliken **Konfigurera** för servergruppen i Azure-portalen. Användargränssnittet ser likadant ut i båda fallen. Dra skjutreglaget för **Hög tillgänglighet** till JA:

![ha skjutreglage](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Klicka på knappen **Spara** om du vill använda ditt val. Det kan ta lite tid att aktivera HA eftersom servergruppen etablerar väntelägen och strömmar data till dem.

På fliken **Översikt** för servergruppen visas alla noder och väntelägen, tillsammans med kolumnen **Hög tillgänglighet** som anger om HA har aktiverats för varje nod.

![ha-kolumnen i servergruppsöversikten](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Nästa steg

Läs mer om [hög tillgänglighet](concepts-hyperscale-high-availability.md).
