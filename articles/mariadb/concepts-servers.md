---
title: Servrar - Azure Database för MariaDB
description: Det här avsnittet innehåller överväganden och riktlinjer för att arbeta med Azure Database för MariaDB-servrar.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527800"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Serverbegrepp i Azure Database för MariaDB
Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure Database för MariaDB-servrar.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Vad är en Azure-databas för MariaDB-server?

En Azure-databas för MariaDB-server är en central administrativ punkt för flera databaser. Det är samma MariaDB server konstruktion som du kanske är bekant med i den lokala världen. Azure Database for MariaDB-tjänsten hanteras, ger prestandagarantier och exponerar åtkomst och funktioner på servernivå.

En Azure-databas för MariaDB-server:

- Skapas i en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Innehåller ett namnområde för databaser.
- Är en behållare med stark livstidssemantik - ta bort en server och den tar bort de inneslutna databaserna.
- Samlokaliseringar av resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server- och databasåtkomst.
- Innehåller utrymme för hanteringsprinciper som gäller för dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Finns i MariaDB motor version 10.2. Mer information finns i [Azure Database som stöds för MariaDB-databasversioner](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database for MariaDB-server. Du kan välja att skapa en enda databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Prissättningen är strukturerad per server, baserat på konfigurationen av prisnivå, virtuella kärnor och lagring (GB). Mer information finns i [Prisnivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hur skyddar jag en Azure-databas för MariaDB-server?

Följande element hjälper till att säkerställa säker åtkomst till databasen.

|||
| :--| :--|
| **Autentisering och auktorisering** | Azure Database för MariaDB-server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns administratörsinloggning. |
| **Protokollet** | Tjänsten stöder ett meddelandebaserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds via TCP/IP och över Unix-domänuttag. |
| **Brandvägg** | För att skydda dina data förhindrar en brandväggsregel all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Se [Brandväggsregler för Azure-databas för MariaDB Server](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten stöder att tvinga fram SSL-anslutningar mellan dina program och databasservern. Se [Konfigurera SSL-anslutning i ditt program för att ansluta till Azure Database för MariaDB](./howto-configure-ssl.md)på ett säkert sätt . |

## <a name="how-do-i-manage-a-server"></a>Hur hanterar jag en server?
Du kan hantera Azure Database för MariaDB-servrar med hjälp av Azure-portalen eller Azure CLI.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Översikt över Azure Database för MariaDB](./overview.md)
- Information om specifika resurskvoter och begränsningar baserat på **tjänstnivån**finns i [Tjänstnivåer](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
