---
title: Skapa användare - Hyperskala (Citus) - Azure-databas för PostgreSQL
description: I den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure-databas för PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484935"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Skapa användare i Azure Database för PostgreSQL - Hyperskala (Citus)

> [!NOTE]
> Termen "användare" avser användare inom en citus-servergrupp (Hyperscale). Om du i stället vill veta mer om Azure-prenumerationsanvändare och deras privilegier kan du besöka [RBAC-artikeln (Azure Role Based Access Control)](../role-based-access-control/built-in-roles.md) eller granska [hur du anpassar roller](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Serveradministratörskontot

PostgreSQL-motorn använder [roller](https://www.postgresql.org/docs/current/sql-createrole.html) för att styra åtkomsten till databasobjekt, och en nyligen skapad citus-servergrupp (Hyperscale) med flera fördefinierade roller:

* [Standardrollerna för PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Eftersom Hyperscale är en hanterad PaaS-tjänst `postgres` kan bara Microsoft logga in med rollen superanvändare. Hyperskala ger `citus` rollen för begränsad administrativ åtkomst.

Behörigheter för `citus` rollen:

* Läs alla konfigurationsvariabler, även variabler som normalt bara visas för superanvändare.
* Läs alla\_\_ \* pg stat visningar och använda olika statistik-relaterade tillägg - även vyer eller tillägg normalt synliga endast för superanvändare.
* Kör övervakningsfunktioner som kan ta ACCESS SHARE-lås på tabeller, eventuellt under lång tid.
* [Skapa PostgreSQL-tillägg](concepts-hyperscale-extensions.md) (eftersom rollen är `azure_pg_admin`medlem i ).

Framför allt `citus` har rollen vissa begränsningar:

* Det går inte att skapa roller
* Det går inte att skapa databaser

## <a name="how-to-create-additional-user-roles"></a>Så här skapar du ytterligare användarroller

Som nämnts `citus` saknar administratörskontot behörighet att skapa ytterligare användare. Om du vill lägga till en användare använder du Azure Portal-gränssnittet.

1. Gå till sidan **Roller** för servergruppen Hyperskala och klicka på **+ Lägg till**:

   ![Sidan Roller](media/howto-hyperscale-create-users/1-role-page.png)

2. Ange rollnamn och lösenord. Klicka på **Spara**.

   ![Lägg till roll](media/howto-hyperscale-create-users/2-add-user-fields.png)

Användaren skapas på koordinatornsnoden för servergruppen och spridas till alla arbetsnoder. Roller som skapas via `LOGIN` Azure-portalen har attributet, vilket innebär att de är sanna användare som kan logga in på databasen.

## <a name="how-to-modify-privileges-for-user-role"></a>Så här ändrar du behörigheter för användarroll

Nya användarroller används ofta för att ge databasåtkomst med begränsade privilegier. Om du vill ändra användarbehörigheter använder du vanliga PostgreSQL-kommandon med ett verktyg som PgAdmin eller psql. (Se [ansluta med psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) i snabbstarten Hyperscale (Citus).)

Om du till `db_user` exempel `mytable`vill tillåta att läsa bevilja du behörigheten:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperskala (Citus) sprider entabells-GRANT-satser genom hela klustret och tillämpar dem på alla arbetsnoder. Men GRANTs som är systemomfattande (till exempel för alla tabeller i ett schema) måste köras på varje datumnod.  Använd `run_command_on_workers()` hjälpfunktionen:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Så här tar du bort en användarroll eller ändrar deras lösenord

Om du vill uppdatera en användare besöker du sidan **Roller** för servergruppen Hyperskala och klickar på ellipserna **...** bredvid användaren. Ellipserna öppnar en meny för att ta bort användaren eller återställa lösenordet.

   ![Redigera en roll](media/howto-hyperscale-create-users/edit-role.png)

Rollen `citus` är privilegierad och kan inte tas bort.

## <a name="next-steps"></a>Nästa steg

Öppna brandväggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [Skapa och hantera citus-brandväggsregler (Hyperscale) med hjälp av Azure-portalen](howto-hyperscale-manage-firewall-using-portal.md).

Mer information om hantering av databasanvändarkonton finns i Produktdokumentationen för PostgreSQL:

* [Databasroller och privilegier](https://www.postgresql.org/docs/current/static/user-manag.html)
* [BIDRAGSFÖR SYNTAX](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Behörigheter](https://www.postgresql.org/docs/current/static/ddl-priv.html)
