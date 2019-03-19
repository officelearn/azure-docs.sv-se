---
title: Server begrepp i Azure Database for MySQL
description: Det här avsnittet innehåller riktlinjer och överväganden för att arbeta med Azure Database for MySQL-servrar.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 565e1bf7a4972e230b3cf56232ebd24519fcab5c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007897"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server begrepp i Azure Database for MySQL

Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure Database for MySQL-servrar.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Vad är en Azure Database for MySQL-server?

En Azure Database for MySQL-server är en central administrativ plats för flera databaser. Det är samma MySQL server-konstruktion som du kanske är bekant med lokala över hela världen. Mer specifikt Azure Database for MySQL-tjänsten hanteras, ger prestanda och exponerar åtkomst och funktioner på servernivå.

En Azure Database for MySQL-server:

- Skapas inom en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Tillhandahåller ett namnområde för databaser.
- Är en behållare med stark livslängdssemantik – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server och åtkomst till databasen.
- Tillhandahåller definitionsområdet för hanteringsprinciper som tillämpas på dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Är tillgänglig i flera versioner. Mer information finns i [stöds Azure Database för MySQL-databasversioner](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja om du vill skapa en databas per server att använda alla resurser eller att skapa flera databaser som delar resurser. Priserna är strukturerade per server, baserat på konfigurationen av prisnivå, virtuella kärnor, och lagringsutrymme (GB). Mer information finns i [prisnivåer](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hur jag för att ansluta och autentisera till en Azure Database for MySQL-server?

Följande element säkerställer säker åtkomst till databasen.

|     |     |
| :-- | :-- |
| **Autentisering och auktorisering** | Azure Database for MySQL-server har stöd för inbyggda MySQL-autentisering. Du kan ansluta och autentisera till en server med den serveradministratör. |
| **Protokoll** | Tjänsten stöder ett meddelandebaserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet som stöds via TCP/IP och över sockets för Unix-domän. |
| **Brandvägg** | För att skydda dina data, förhindrar en brandväggsregel all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Se [Azure Database for MySQL-serverbrandväggsregler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten stöder framtvinga SSL-anslutningar mellan dina program och din databasserver.  Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL). |

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?

Du kan hantera Azure Database for MySQL-servrar med hjälp av Azure portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

- En översikt över tjänsten finns i [Azure Database for MySQL-översikt](./overview.md)
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-service-tiers.md)
- Information om hur du ansluter till tjänsten finns i [anslutningsbibliotek för Azure Database for MySQL](./concepts-connection-libraries.md).
