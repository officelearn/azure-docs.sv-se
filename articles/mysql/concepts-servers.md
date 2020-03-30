---
title: Serverkoncept - Azure Database för MySQL
description: Det här avsnittet innehåller överväganden och riktlinjer för att arbeta med Azure Database för MySQL-servrar.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537014"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Serverbegrepp i Azure Database för MySQL

Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure Database för MySQL-servrar.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Vad är en Azure-databas för MySQL-server?

En Azure-databas för MySQL-server är en central administrativ punkt för flera databaser. Det är samma MySQL server konstruktion som du kanske är bekant med i den lokala världen. Azure Database for MySQL-tjänsten hanteras, ger prestandagarantier och exponerar åtkomst och funktioner på servernivå.

En Azure-databas för MySQL-server:

- Skapas i en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Innehåller ett namnområde för databaser.
- Är en behållare med stark livstidssemantik - ta bort en server och den tar bort de inneslutna databaserna.
- Samlokaliseringar av resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server- och databasåtkomst.
- Innehåller utrymme för hanteringsprinciper som gäller för dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Finns i flera versioner. Mer information finns i [Azure Database for MySQL-databasversioner som stöds](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja att skapa en enda databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Prissättningen är strukturerad per server, baserat på konfigurationen av prisnivå, virtuella kärnor och lagring (GB). Mer information finns i [Prisnivåer](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hur ansluter och autentiserar jag till en Azure-databas för MySQL-server?

Följande element hjälper till att säkerställa säker åtkomst till databasen.

|     |     |
| :-- | :-- |
| **Autentisering och auktorisering** | Azure Database för MySQL-server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns administratörsinloggning. |
| **Protokollet** | Tjänsten stöder ett meddelandebaserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds via TCP/IP och över Unix-domänuttag. |
| **Brandvägg** | För att skydda dina data förhindrar en brandväggsregel all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Se [Azure Database for MySQL Server-brandväggsregler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten stöder att tvinga fram SSL-anslutningar mellan dina program och databasservern.  Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL). |

## <a name="how-do-i-manage-a-server"></a>Hur hanterar jag en server?

Du kan hantera Azure Database för MySQL-servrar med hjälp av Azure-portalen eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

- En översikt över tjänsten finns i [Översikt över Azure Database for MySQL](./overview.md)
- Information om specifika resurskvoter och begränsningar baserat på **tjänstnivån**finns i [Tjänstnivåer](./concepts-service-tiers.md)
- Information om hur du ansluter till tjänsten finns i [Anslutningsbibliotek för Azure Database för MySQL](./concepts-connection-libraries.md).
