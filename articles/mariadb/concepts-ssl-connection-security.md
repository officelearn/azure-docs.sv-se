---
title: SSL-anslutning – Azure Database for MariaDB
description: Information om hur du konfigurerar Azure Database for MariaDB och associerade program för att använda SSL-anslutningar korrekt
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b7206db24c813c8f273dd57407c43974932ff110
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772035"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>SSL-anslutning i Azure Database for MariaDB
Azure Database for MariaDB stöder anslutning av databas servern till klient program med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="default-settings"></a>Standardinställningar
Som standard ska databas tjänsten konfigureras för att kräva SSL-anslutningar vid anslutning till MariaDB.  Vi rekommenderar att du inte inaktiverar SSL-alternativet när det är möjligt.

När du konfigurerar en ny Azure Database for MariaDB-server via Azure Portal och CLI är tvingande av SSL-anslutningar aktiverat som standard.

Anslutnings strängar för olika programmeringsspråk visas i Azure Portal. Dessa anslutnings strängar innehåller de SSL-parametrar som krävs för att ansluta till databasen. I Azure Portal väljer du servern. Under rubriken **Inställningar** väljer du **anslutnings strängarna**. SSL-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

Information om hur du aktiverar eller inaktiverar SSL-anslutning när du utvecklar program finns i [så här konfigurerar du SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [regler för Server brand vägg](concepts-firewall-rules.md)
- Lär dig hur du [konfigurerar SSL](howto-configure-ssl.md).
