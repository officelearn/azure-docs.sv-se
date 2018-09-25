---
title: Server begrepp i Azure Database for MariaDB
description: Det här avsnittet innehåller riktlinjer och överväganden för att arbeta med Azure-databas för MariaDB-servrar.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cf57acdcbcfa792a6c5ab62c6e8ec0589d625df7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994603"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Server begrepp i Azure Database for MariaDB
Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure-databas för MariaDB-servrar.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Vad är en Azure Database for MariaDB server?

En Azure Database for MariaDB-server är en central administrativ plats för flera databaser. Det är samma MariaDB server-konstruktion som du kanske är bekant med lokala över hela världen. Mer specifikt tjänsten Azure Database for MariaDB hanteras, ger prestanda och exponerar åtkomst och funktioner på servernivå.

En Azure Database for MariaDB-server:

- Skapas inom en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Tillhandahåller ett namnområde för databaser.
- Är en behållare med stark livslängdssemantik – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server och åtkomst till databasen.
- Tillhandahåller definitionsområdet för hanteringsprinciper som tillämpas på dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Är tillgängligt i MariaDB Motorversion 10.2. Mer information finns i [stöds Azure Database for MariaDB databasversioner](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser inom en Azure Database for MariaDB-server. Du kan välja om du vill skapa en databas per server att använda alla resurser eller att skapa flera databaser som delar resurser. Priserna är strukturerade per server, baserat på konfigurationen av prisnivå, virtuella kärnor, och lagringsutrymme (GB). Mer information finns i [prisnivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hur skyddar jag en Azure Database for MariaDB server?

Följande element säkerställer säker åtkomst till databasen.
|||
| :--| :--|
| **Autentisering och auktorisering** | Azure Database for MariaDB-server har stöd för inbyggda MySQL-autentisering. Du kan ansluta och autentisera till en server med den serveradministratör. |
| **Protokoll** | Tjänsten stöder ett meddelandebaserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet som stöds via TCP/IP och över sockets för Unix-domän. |
| **Brandvägg** | För att skydda dina data, förhindrar en brandväggsregel all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Se [Azure Database for MariaDB serverbrandväggsregler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten stöder framtvinga SSL-anslutningar mellan dina program och din databasserver.  <!--See [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md).--> |

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?
Du kan hantera Azure-databas för MariaDB-servrar med hjälp av Azure portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for MariaDB-översikt](./overview.md)
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-pricing-tiers.md)
<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
