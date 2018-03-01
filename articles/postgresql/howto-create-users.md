---
title: "Skapa användare i Azure-databas för PostgreSQL-server"
description: "Den här artikeln beskriver hur du kan skapa nya användarkonton för att interagera med en Azure-databas för PostgreSQL-servern."
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Skapa användare i Azure-databas för PostgreSQL-server 
Den här artikeln beskriver hur du kan skapa användare i en Azure-databas för PostgreSQL-servern.

## <a name="the-server-admin-account"></a>Serveradministratörskontot
När du först skapade din Azure-databas för PostgreSQL angav du ett server admin-användarnamn och lösenord. Mer information kan du följa den [Quickstart](quickstart-create-server-database-portal.md) att se steg för steg-metod. Eftersom användarnamn för server-administratör är ett anpassat namn, kan du hitta det valda servern admin användarnamnet från Azure-portalen.

Azure-databasen för PostgreSQL server skapas med 3 standardrollerna definierats. Du kan se dessa roller genom att köra kommandot: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- din serveradministratörsanvändaren

Din serveradministratörsanvändaren är medlem i rollen azure_pg_admin. Administratörskonto server är dock inte en del av rollen azure_superuser. Eftersom tjänsten är en hanterad PaaS-tjänst, är endast Microsoft en del av den överordnade användarrollen. 

PostgreSQL motorn använder behörighet för att styra åtkomsten till objekt i databasen, enligt beskrivningen i den [PostgreSQL produktdokumentationen](https://www.postgresql.org/docs/current/static/sql-createrole.html). I Azure-databas för PostgreSQL server-administratören beviljas dessa privilegier: inloggning, NOSUPERUSER, ÄRV, CREATEDB, CREATEROLE, NOREPLICATION

Användarkontot för server-administratör kan användas för att skapa ytterligare användare och tilldela dessa användare i rollen azure_pg_admin. Administratörskonto server kan också användas för att skapa mindre privilegierade användare och roller som har åtkomst till enskilda databaser och scheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Så här skapar du ytterligare administrativa användare i Azure-databas för PostgreSQL
1. Hämta anslutningsnamn för administration och information.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du servernamnet och logga in information från servern **översikt** sidan eller **egenskaper** sida i Azure-portalen. 

2. Använd administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, t.ex pgAdmin eller psql.
   Om du är osäker på hur du ansluter [Anslut till PostgreSQL-databas med hjälp av psql i molnet Shell](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Redigera och kör följande SQL-kod. Ersätt ditt nya användarnamn för platshållaren värdet < new_user > och Ersätt platshållaren lösenordet med starka lösenord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Skapa databasanvändare i Azure-databas för PostgreSQL

1. Hämta anslutningsnamn för administration och information.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du servernamnet och logga in information från servern **översikt** sidan eller **egenskaper** sida i Azure-portalen. 

2. Använd administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, t.ex pgAdmin eller psql.

3. Redigera och kör följande SQL-kod. Ersätt platshållaren värdet `<db_user>` med rätt användarnamn och platshållare värdet `<newdb>` med din egen databasnamn. Ersätt platshållaren lösenordet med starka lösenord. 

   Den här syntaxen för sql-kod skapar en ny databas med namnet testdb, exempel. Skapar sedan en ny användare i PostgreSQL-tjänsten och beviljar ansluter behörighet till den nya databasen för den användaren. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. Med ett administratörskonto kan behöva du ge ytterligare behörigheter om du vill skydda objekten i databasen. Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/current/static/ddl-priv.html) för mer information om databasroller och privilegier. Exempel: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Logga in på servern, anger den avsedda databas med nytt användarnamn och lösenord. Det här exemplet visar psql kommandoraden. Med det här kommandot uppmanas du lösenordet för användarnamnet. Ersätt egna servernamnet, databasnamnet och användarnamn.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarna datorer för att kunna ansluta: [skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton finns i produktdokumentationen för PostgreSQL för [databasroller och privilegier](https://www.postgresql.org/docs/current/static/user-manag.html), [BEVILJA Syntax](https://www.postgresql.org/docs/current/static/sql-grant.html), och [privilegier](https://www.postgresql.org/docs/current/static/ddl-priv.html).
