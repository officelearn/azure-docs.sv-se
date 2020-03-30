---
title: Skapa användare - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure-databas för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384355"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Skapa användare i Azure Database för PostgreSQL - Single Server

I den här artikeln beskrivs hur du kan skapa användare i en Azure-databas för PostgreSQL-server.

Om du vill veta mer om hur du skapar och hanterar Azure-prenumerationsanvändare och deras privilegier kan du besöka [RBAC-artikeln (Azure Role Based Access Control)](../role-based-access-control/built-in-roles.md) eller granska [hur du anpassar roller](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Serveradministratörskontot

När du först skapade din Azure-databas för PostgreSQL angav du ett användarnamn och lösenord för serveradministratörer. Om du vill ha mer information kan du följa [snabbstarten](quickstart-create-server-database-portal.md) för att se steg-för-steg-metoden. Eftersom serveradministratörens användarnamn är ett eget namn kan du hitta det valda användarnamnet för serveradministratörer från Azure-portalen.

Azure-databasen för PostgreSQL-servern skapas med de tre standardrollerna definierade. Du kan se dessa roller genom att köra kommandot:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- din serveradministratörsanvändare

Serveradministratörsanvändaren är medlem i azure_pg_admin-rollen. Serveradministratörskontot är dock inte en del av den azure_superuser rollen. Eftersom den här tjänsten är en hanterad PaaS-tjänst är endast Microsoft en del av superanvändarrollen.

PostgreSQL-motorn använder privilegier för att styra åtkomsten till databasobjekt, som beskrivs i [postgresql-produktdokumentationen](https://www.postgresql.org/docs/current/static/sql-createrole.html). I Azure Database for PostgreSQL beviljas serveradministratörsanvändaren dessa behörigheter: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

Användarkontot för serveradministratör kan användas för att skapa ytterligare användare och bevilja dessa användare i den azure_pg_admin rollen. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare och roller som har åtkomst till enskilda databaser och scheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Skapa ytterligare administratörsanvändare i Azure Database för PostgreSQL

1. Hämta anslutningsinformation och administratörsanvändarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta servernamn och inloggningsinformation från sidan **Översikt** över servern eller sidan **Egenskaper** i Azure-portalen.

2. Använd administratörskontot och lösenordet för att ansluta till databasservern. Använd ditt önskade klientverktyg, till exempel pgAdmin eller psql.
   Om du är osäker på hur du ansluter läser du [snabbstarten](./quickstart-create-server-database-portal.md)

3. Redigera och kör följande SQL-kod. Ersätt ditt nya användarnamn för platshållarvärdet <new_user> och ersätt platshållarlösenordet med ditt eget starka lösenord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Så här skapar du databasanvändare i Azure Database för PostgreSQL

1. Hämta anslutningsinformation och administratörsanvändarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta servernamn och inloggningsinformation från sidan **Översikt** över servern eller sidan **Egenskaper** i Azure-portalen.

2. Använd administratörskontot och lösenordet för att ansluta till databasservern. Använd ditt önskade klientverktyg, till exempel pgAdmin eller psql.

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `<db_user>` med ditt avsedda nya användarnamn `<newdb>` och platshållarvärdet med ditt eget databasnamn. Ersätt platshållarlösenordet med ditt eget starka lösenord.

   Den här sql-kodsyntaxen skapar en ny databas med namnet testdb, till exempel. Sedan skapas en ny användare i PostgreSQL-tjänsten och ger anslutningsbehörighet till den nya databasen för den användaren.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Med hjälp av ett administratörskonto kan du behöva bevilja ytterligare privilegier för att skydda objekten i databasen. Mer information om databasroller och behörigheter finns i Dokumentationen för [PostgreSQL.](https://www.postgresql.org/docs/current/static/ddl-priv.html) Ett exempel:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Logga in på servern och ange den angivna databasen med det nya användarnamnet och lösenordet. I det här exemplet visas psql-kommandoraden. Med det här kommandot uppmanas du att ange lösenordet för användarnamnet. Ersätt ditt eget servernamn, databasnamn och användarnamn.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Nästa steg

Öppna brandväggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [Skapa och hantera Azure Database for PostgreSQL-brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-using-portal.md) eller Azure [CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton finns i PostgreSQL-produktdokumentation för [databasroller och behörigheter,](https://www.postgresql.org/docs/current/static/user-manag.html) [BEVILJA syntax](https://www.postgresql.org/docs/current/static/sql-grant.html)och [privilegier](https://www.postgresql.org/docs/current/static/ddl-priv.html).
