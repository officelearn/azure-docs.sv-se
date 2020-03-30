---
title: SSL-anslutning – Azure Database för MariaDB
description: Information om hur du konfigurerar Azure Database för MariaDB och associerade program för att korrekt använda SSL-anslutningar
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477076"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>SSL-anslutning i Azure Database för MariaDB
Azure Database för MariaDB stöder anslutning av databasservern till klientprogram med SSL (Secure Sockets Layer). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="default-settings"></a>Standardinställningar
Som standard bör databastjänsten konfigureras så att den kräver SSL-anslutningar när du ansluter till MariaDB.  Vi rekommenderar att du undviker att inaktivera SSL-alternativet när det är möjligt.

När du etablerar en ny Azure-databas för MariaDB-server via Azure-portalen och CLI aktiveras tvingande SSL-anslutningar som standard.

Anslutningssträngar för olika programmeringsspråk visas i Azure-portalen. Dessa anslutningssträngar innehåller de SSL-parametrar som krävs för att ansluta till databasen. Välj din server i Azure-portalen. Markera **anslutningssträngarna**under rubriken **Inställningar** . SSL-parametern varierar beroende på kopplingen, till exempel "ssl=true" eller "sslmode=require" eller "sslmode=required" och andra variationer.

Mer information om hur du aktiverar eller inaktiverar SSL-anslutning när du utvecklar programmet finns i [Så här konfigurerar du SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [regler för serverbrandvägg](concepts-firewall-rules.md)
- Lär dig hur du [konfigurerar SSL](howto-configure-ssl.md).
