---
title: Skapa användare – storskalig (citus) – Azure Database for PostgreSQL
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d093d4c23fcc44e7e9f3461f875607926f4b612d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977581"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Skapa användare i Azure Database for PostgreSQL-storskalig (citus)

I den här artikeln beskrivs hur du kan skapa användare i en citus-Server (storskalig). Om du vill lära dig i stället för Azures prenumerations användare och deras behörigheter går du till [artikeln Azure rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/built-in-roles.md) eller granskar [hur du anpassar roller](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Serveradministratörskontot

En nyligen skapad citus-servergrupp () har flera roller som definierats:

* [Standard rollerna för postgresql](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

PostgreSQL-motorn använder behörigheter för att kontrol lera åtkomsten till databas objekt, enligt beskrivningen i [produkt dokumentationen för postgresql](https://www.postgresql.org/docs/current/static/sql-createrole.html).
Din server administratörs användare, *citus*, är medlem i *azure_pg_admin* -rollen.
Det är dock inte en del av rollen *postgres* (Super User).  Eftersom den här storleken är en hanterad PaaS-tjänst ingår endast Microsoft i superanvändarens roll. *Citus* -användaren har begränsad behörighet och kan t. ex. skapa nya databaser.

## <a name="how-to-create-additional-users"></a>Så här skapar du ytterligare användare

Administratörs kontot *citus* saknar behörighet att skapa ytterligare användare. Använd Azure Portal-gränssnittet om du vill lägga till en användare.

1. Gå till sidan **roller** för din skalnings Server grupp och klicka på **+ Lägg till**:

   ![Sidan roller](media/howto-hyperscale-create-users/1-role-page.png)

2. Ange roll namn och lösen ord. Klicka på **Save** (Spara).

   ![Lägg till roll](media/howto-hyperscale-create-users/2-add-user-fields.png)

Användaren kommer att skapas på noden koordinator i Server gruppen och spridas till alla arbetsnoder.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Ta bort en användare eller ändra deras lösen ord

Gå till sidan **roller** för din skalnings Server grupp och klicka på ellipserna **...** bredvid en användare. Ellipserna öppnar en meny för att ta bort användaren eller återställa lösen ordet.

   ![Redigera en roll](media/howto-hyperscale-create-users/edit-role.png)

*Citus* -rollen är privilegie rad och kan inte tas bort.

## <a name="how-to-modify-privileges-for-role"></a>Ändra behörigheter för rollen

Nya roller används ofta för att ge åtkomst till databasen med begränsade privilegier. Om du vill ändra användar behörigheter använder du standard PostgreSQL-kommandon med ett verktyg som PgAdmin eller psql. (Se [ansluta med psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) i snabb starten av citus ().)

Om du till exempel vill tillåta *db_user* att läsa *tabellen i tabellen*ger du behörigheten:

```sql
GRANT SELECT ON mytable TO db_user;
```

Citus (storskalig) sprider GRANT-uttryck med en tabell genom hela klustret och tillämpar dem på alla arbetsnoder. Detta gäller dock för hela systemet (t. ex. för alla tabeller i ett schema) måste köras på alla date-noder.  Använd hjälp funktionen *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [skapa och hantera citus-brandvägg (storskalig) brand Väggs regler med hjälp av Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Mer information om hantering av databas användar konton finns i produkt dokumentation för PostgreSQL:

* [Databas roller och behörigheter](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT-syntax](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegier](https://www.postgresql.org/docs/current/static/ddl-priv.html)
