---
title: "Server begrepp i Azure-databas för MySQL | Microsoft Docs"
description: "Det här avsnittet innehåller information och riktlinjer för att arbeta med Azure-databas för MySQL-servrar."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d3de3fdf28997b63321bf23443472db43ebb5c52
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server begrepp i Azure för MySQL-databas
Den här artikeln innehåller information och riktlinjer för att arbeta med Azure-databas för MySQL-servrar.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Vad är en Azure-databas för MySQL-servern?

En Azure-databas för MySQL-server är en central administrativ plats för flera databaser. Det är samma MySQL server-konstruktion som du kanske känner till i den lokala världen. Mer specifikt Azure-databas för MySQL-tjänst hanteras, ger garanti vad gäller prestanda och visar åtkomst och funktioner på servernivå.

En Azure-databas för MySQL-server:

- Skapas inom en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Ger ett namnområde för databaser.
- Är en behållare med starka livstid semantik - ta bort en server och tar bort de inneslutna databaserna.
- Collocates resurser i en region.
- Ger en Anslutningens slutpunkt för server och åtkomst till databasen.
- Scope för av hanteringsprinciper som gäller för dess databaser: inloggning, brandvägg, användare, roller, konfigurationer och så vidare.
- Är tillgänglig i flera versioner. Mer information finns i [stöd för Azure-databas för MySQL-databasversioner](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja om du vill skapa en enskild databas per server att använda alla resurser eller skapa flera databaser att dela resurser. Prissättning är strukturerade per server, baserat på konfigurationen av prisnivå, Compute enheter, och lagringsutrymme (GB). Mer information finns i [prisnivåer](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hur gör ansluta och autentisera till en Azure-databas för MySQL-servern?

Följande element att säkerställa säker åtkomst till databasen.
|||
| :-- | :-- |
| **Autentisering och auktorisering** | Azure-databas för MySQL-server stöder interna MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns administratör för inloggning. |
| **Protokoll** | Tjänsten stöder en message-baserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds via TCP/IP och över sockets för Unix-domän. |
| **Brandvägg** | För att skydda dina data, förhindrar en brandväggsregel all åtkomst till databasservern, förrän du anger vilka datorer som har behörighet. Se [Azure-databas för MySQL serverbrandväggsreglerna](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten stöder att framtvinga SSL-anslutningar mellan dina program och databasservern.  Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL). |

## <a name="how-do-i-manage-a-server"></a>Hur hanterar ett server?
Du kan hantera Azure-databas för MySQL-servrar med hjälp av Azure-portalen eller Azure CLI.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns [Azure Database MySQL-översikt](./overview.md)
- Information om specifik resurs kvoter och begränsningar baserat på din **tjänstnivån**, se [tjänstnivåer](./concepts-service-tiers.md)
- Information om hur du ansluter till tjänsten finns [anslutningsbibliotek för Azure-databas för MySQL](./concepts-connection-libraries.md).
