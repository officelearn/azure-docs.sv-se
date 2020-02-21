---
title: Skapa användare – storskalig (citus) – Azure Database for PostgreSQL
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484935"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Skapa användare i Azure Database for PostgreSQL-storskalig (citus)

> [!NOTE]
> Termen "användare" syftar på användare i en citus-servergrupp (enscales). Om du vill lära dig i stället för Azures prenumerations användare och deras behörigheter går du till [artikeln Azure rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/built-in-roles.md) eller granskar [hur du anpassar roller](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Serveradministratörskontot

PostgreSQL-motorn använder [roller](https://www.postgresql.org/docs/current/sql-createrole.html) för att kontrol lera åtkomsten till databas objekt och en nyskapad citus-servergrupp () innehåller flera roller som definierats:

* [Standard rollerna för postgresql](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Eftersom den storskaliga skalningen är en hanterad PaaS-tjänst kan endast Microsoft logga in med rollen `postgres` super-användare. För begränsad administrativ åtkomst ger skalning rollen `citus`.

Behörigheter för `citus`-rollen:

* Läs alla modellvariabler, även variabler som normalt sett endast visas för superanvändare.
* Läs alla PG\_stat\_\* vyer och Använd olika statistik relaterade tillägg – även vyer eller tillägg som normalt sett endast visas för superanvändare.
* Kör övervaknings funktioner som kan få åtkomst till resurs lås i tabeller, eventuellt en längre tid.
* [Skapa postgresql-tillägg](concepts-hyperscale-extensions.md) (eftersom rollen är medlem i `azure_pg_admin`).

I synnerhet har `citus` rollen vissa begränsningar:

* Det går inte att skapa roller
* Det går inte att skapa databaser

## <a name="how-to-create-additional-user-roles"></a>Så här skapar du ytterligare användar roller

Som nämnts saknar `citus` administratörs kontot behörighet att skapa ytterligare användare. Använd Azure Portal-gränssnittet om du vill lägga till en användare.

1. Gå till sidan **roller** för din skalnings Server grupp och klicka på **+ Lägg till**:

   ![Sidan roller](media/howto-hyperscale-create-users/1-role-page.png)

2. Ange roll namn och lösen ord. Klicka på **Save** (Spara).

   ![Lägg till roll](media/howto-hyperscale-create-users/2-add-user-fields.png)

Användaren kommer att skapas på noden koordinator i Server gruppen och spridas till alla arbetsnoder. Roller som skapas via Azure Portal har attributet `LOGIN`, vilket innebär att de är sanna användare som kan logga in på databasen.

## <a name="how-to-modify-privileges-for-user-role"></a>Ändra behörigheter för användar rollen

Nya användar roller används ofta för att ge åtkomst till databasen med begränsade privilegier. Om du vill ändra användar behörigheter använder du standard PostgreSQL-kommandon med ett verktyg som PgAdmin eller psql. (Se [ansluta med psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) i snabb starten av citus ().)

Om du till exempel vill tillåta `db_user` läsa `mytable`tilldelar du behörigheten:

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus (storskalig) sprider GRANT-uttryck med en tabell genom hela klustret och tillämpar dem på alla arbetsnoder. Detta gäller dock för hela systemet (till exempel för alla tabeller i ett schema) måste köras på alla date-noder.  Använd `run_command_on_workers()` Helper-funktionen:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Ta bort en användar roll eller ändra deras lösen ord

Om du vill uppdatera en användare går du till sidan **roller** för din skalnings Server grupp och klickar på ellipserna **...** bredvid användaren. Ellipserna öppnar en meny för att ta bort användaren eller återställa lösen ordet.

   ![Redigera en roll](media/howto-hyperscale-create-users/edit-role.png)

`citus` rollen är privilegie rad och kan inte tas bort.

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [skapa och hantera citus-brandvägg (storskalig) brand Väggs regler med hjälp av Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Mer information om hantering av databas användar konton finns i produkt dokumentation för PostgreSQL:

* [Databas roller och behörigheter](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT-syntax](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Höjd](https://www.postgresql.org/docs/current/static/ddl-priv.html)
