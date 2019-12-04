---
title: Server koncept – Azure Database for MySQL
description: Det här avsnittet innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL-servrar.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9a2e2eb022d96af1437ea4189d11f5fa69339325
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770007"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server koncept i Azure Database for MySQL

Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL-servrar.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Vad är en Azure Database for MySQL-Server?

En Azure Database for MySQL-server är en central administrativ punkt för flera databaser. Det är samma server konstruktion för MySQL som du kanske är van vid i den lokala världen. Mer specifikt är Azure Database for MySQLs tjänsten hanterad, ger prestanda garantier och visar åtkomst och funktioner på server nivå.

En Azure Database for MySQL server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutnings slut punkt för Server-och databas åtkomst.
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Är tillgänglig i flera versioner. Mer information finns i [Azure Database for MySQL databas versioner som stöds](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja att skapa en enskild databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [pris nivåer](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hur gör jag för att ansluta och autentisera till en Azure Database for MySQL-Server?

Följande element hjälper till att säkerställa säker åtkomst till din databas.

|     |     |
| :-- | :-- |
| **Autentisering och auktorisering** | Azure Database for MySQL Server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns Administratörs inloggning. |
| **Protokoll** | Tjänsten har stöd för ett Message-baserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Se [Azure Database for MySQL server brand Väggs regler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten har stöd för att tvinga SSL-anslutningar mellan dina program och databas servern.  Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL). |

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?

Du kan hantera Azure Database for MySQL-servrar med hjälp av Azure Portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

- En översikt över tjänsten finns i [Azure Database for MySQL översikt](./overview.md)
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån**finns i [tjänst nivåer](./concepts-service-tiers.md)
- Information om hur du ansluter till tjänsten finns i [anslutnings bibliotek för Azure Database for MySQL](./concepts-connection-libraries.md).
