---
title: Server begrepp i Azure Database for PostgreSQL
description: Den här artikeln innehåller riktlinjer för att konfigurera och hantera Azure Database for PostgreSQL-servrar och överväganden.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: a5b6db9f4bfbe9a11119270e9236580b41fae8c7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540870"
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL-servrar
Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure Database for PostgreSQL-servrar.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Vad är en Azure Database for PostgreSQL-server?
En Azure Database for PostgreSQL-server är en central administrativ plats för flera databaser. Det är samma PostgreSQL server-konstruktion som du kanske är bekant med lokala över hela världen. Mer specifikt PostgreSQL-tjänsten hanteras, ger prestanda, exponerar åtkomst och funktioner på servernivå.

En Azure Database for PostgreSQL-server:

- Skapas inom en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Tillhandahåller ett namnområde för databaser.
- Är en behållare med stark livslängdssemantik – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server och databasåtkomst 
- Tillhandahåller definitionsområdet för hanteringsprinciper som tillämpas på dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Är tillgänglig i flera versioner. Mer information finns i [PostgreSQL-databas-versioner som stöds](concepts-supported-versions.md).
- Kan utökas av användare. Mer information finns i [PostgreSQL-tillägg](concepts-extensions.md).

Du kan skapa en eller flera databaser inom en Azure Database for PostgreSQL-server. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Priserna är strukturerade per server, baserat på konfigurationen av prisnivå, virtuella kärnor, och lagringsutrymme (GB). Mer information finns i [prisnivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hur jag för att ansluta och autentisera till en Azure Database for PostgreSQL-server?
Följande element att säkerställa säker åtkomst till databasen:

|||
|:--|:--|
| **Autentisering och auktorisering** | Azure Database for PostgreSQL-server stöder interna PostgreSQL-autentisering. Du kan ansluta och autentisera till servern med den serveradministratör. |
| **Protokoll** | Tjänsten stöder ett meddelandebaserat protokoll som används av PostgreSQL. |
| **TCP/IP** | Protokollet som stöds via TCP/IP och över sockets för Unix-domän. |
| **Brandvägg** | För att skydda dina data, förhindrar en brandväggsregel all åtkomst till servern och dess databaser tills du anger vilka datorer som har behörighet. Se [Azure Database for PostgreSQL-Server brandväggsregler](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Hantering av servern
Du kan hantera Azure Database for PostgreSQL-servrar med hjälp av den [Azure-portalen](https://portal.azure.com) eller [Azure CLI](/cli/azure/postgres).

När du skapar en server måste ställa du in autentiseringsuppgifterna för din administratörsanvändaren. Administratören är den högsta Privilegierade användaren som du har på servern. Det hör till rollen azure_pg_admin. Den här rollen har inte fullständig superanvändare behörigheter. 

Attributet PostgreSQL superanvändare har tilldelats azure_superuser, som tillhör den hanterade tjänsten. Du har inte åtkomst till den här rollen.

En Azure Database for PostgreSQL-server har standarddatabaser: 
- **postgres** -en standarddatabas som du kan ansluta till när servern har skapats.
- **azure_maintenance** -den här databasen används för att avgränsa de processer som tillhandahåller hanteringstjänster från användaråtgärder. Du har inte åtkomst till den här databasen.
- **azure_sys** -en-databas för Query Store. Den här databasen lagra inte data när Query Store är inaktiverad; Det här är standardinställningen. Mer information finns i den [Query Store översikt](concepts-query-store.md).


## <a name="server-parameters"></a>Serverparametrar
PostgreSQL-serverparametrar fastställa konfigurationen av servern. Azure Database för PostgreSQL listan över parametrar kan ses och redigeras med hjälp av Azure portal eller Azure CLI. 

Som en hanterad tjänst för Postgres de konfigurerbara parametrarna i Azure Database för PostgreSQL är en delmängd av parametrarna i en lokal Postgres-instans (Mer information om Postgres-parametrar finns i den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Azure Database för PostgreSQL-server har aktiverats med standardvärden för varje parameter skapats. Starta om vissa parametrar som kräver en server eller superanvändare åtkomst för att ändringarna ska börja gälla kan inte konfigureras av användaren.


## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for PostgreSQL översikt](overview.md).
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](concepts-pricing-tiers.md).
- Information om hur du ansluter till tjänsten finns i [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Visa och redigera serverparametrar via [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).
