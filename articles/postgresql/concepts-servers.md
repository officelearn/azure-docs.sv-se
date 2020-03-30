---
title: Servrar - Azure Database för PostgreSQL - Single Server
description: Den här artikeln innehåller överväganden och riktlinjer för att konfigurera och hantera Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768171"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – enskild server
Den här artikeln innehåller överväganden och riktlinjer för att arbeta med Azure Database för PostgreSQL - Single Server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Vad är en Azure Database for PostgreSQL-server?
En server i Azure Database for PostgreSQL - Single Server distributionsalternativ är en central administrativ punkt för flera databaser. Det är samma PostgreSQL server konstruktion som du kanske är bekant med i den lokala världen. PostgreSQL-tjänsten hanteras, ger prestandagarantier, exponerar åtkomst och funktioner på servernivå.

En Azure-databas för PostgreSQL-server:

- Skapas i en Azure-prenumeration.
- Är den överordnade resursen för databaser.
- Innehåller ett namnområde för databaser.
- Är en behållare med stark livstidssemantik - ta bort en server och den tar bort de inneslutna databaserna.
- Samlokaliseringar av resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för server- och databasåtkomst 
- Innehåller utrymme för hanteringsprinciper som gäller för dess databaser: inloggning, brandvägg, användare, roller, konfigurationer osv.
- Finns i flera versioner. Mer information finns i [PostgreSQL-databasversioner som stöds](concepts-supported-versions.md).
- Är utökningsbar av användare. Mer information finns i [PostgreSQL-tillägg](concepts-extensions.md).

I en Azure-databas för PostgreSQL-server kan du skapa en eller flera databaser. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Prissättningen är strukturerad per server, baserat på konfigurationen av prisnivå, virtuella kärnor och lagring (GB). Mer information finns i [Prisnivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hur ansluter och autentiserar jag till en Azure-databas för PostgreSQL-server?
Följande element hjälper till att säkerställa säker åtkomst till databasen:

|||
|:--|:--|
| **Autentisering och auktorisering** | Azure Database for PostgreSQL-server stöder ursprunglig PostgreSQL-autentisering. Du kan ansluta och autentisera till servern med serverns administratörsinloggning. |
| **Protokollet** | Tjänsten stöder ett meddelandebaserat protokoll som används av PostgreSQL. |
| **TCP/IP** | Protokollet stöds via TCP/IP och över Unix-domän sockets. |
| **Brandvägg** | För att skydda dina data förhindrar en brandväggsregel all åtkomst till servern och dess databaser, tills du anger vilka datorer som har behörighet. Se [Azure Database for PostgreSQL Server-brandväggsregler](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Hantera servern
Du kan hantera Azure Database för PostgreSQL-servrar med hjälp av [Azure-portalen](https://portal.azure.com) eller [Azure CLI](/cli/azure/postgres).

När du skapar en server konfigurerar du autentiseringsuppgifterna för administratörsanvändaren. Administratörsanvändaren är den användare med högsta behörighet som du har på servern. Den tillhör den roll azure_pg_admin. Den här rollen har inte fullständig superanvändarbehörighet. 

Attributet PostgreSQL superuser tilldelas azure_superuser, som tillhör den hanterade tjänsten. Du har inte åtkomst till den här rollen.

En Azure-databas för PostgreSQL-server har standarddatabaser: 
- **postgres** - En standarddatabas som du kan ansluta till när servern har skapats.
- **azure_maintenance** - Den här databasen används för att separera de processer som tillhandahåller den hanterade tjänsten från användaråtgärder. Du har inte åtkomst till den här databasen.
- **azure_sys** - En databas för Frågearkivet. Den här databasen ackumulerar inte data när Query Store är inaktiverat. Detta är standardinställningen. Mer information finns i [översikten query store](concepts-query-store.md).


## <a name="server-parameters"></a>Serverparametrar
PostgreSQL-serverparametrarna bestämmer serverns konfiguration. I Azure Database for PostgreSQL kan listan över parametrar visas och redigeras med Azure-portalen eller Azure CLI. 

Som en hanterad tjänst för Postgres är de konfigurerbara parametrarna i Azure Database for PostgreSQL en delmängd av parametrarna i en lokal Postgres-instans (Mer information om Postgres-parametrar finns i [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Azure-databasen för PostgreSQL-servern är aktiverad med standardvärden för varje parameter när den skapas. Vissa parametrar som kräver en omstart av servern eller superanvändarens åtkomst för att ändringarna ska börja gälla kan inte konfigureras av användaren.


## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for PostgreSQL Overview](overview.md).
- Information om specifika resurskvoter och begränsningar baserat på **tjänstnivån**finns i [Tjänstnivåer](concepts-pricing-tiers.md).
- Information om hur du ansluter till tjänsten finns i [Anslutningsbibliotek för Azure Database för PostgreSQL](concepts-connection-libraries.md).
- Visa och redigera serverparametrar via [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).
