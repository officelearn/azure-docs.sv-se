---
title: Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) Snabbstart
description: Snabbstart för att skapa och fråga distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus) (förhandsversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406448"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Snabbstart: Skapa en Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) i Azure portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) servergrupp med hjälp av Azure portal. Du vill utforska distribuerade data: horisontell partitionering tabeller över noder, mata in exempeldata och köra frågor som körs på flera noder.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Skapa en Azure Database för PostgreSQL

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
3. För alternativ för distribution, klickar du på den **skapa** knappen **hyperskala (Citus) servergrupp - FÖRHANDSVERSION.**
4. Fyll i formuläret om den nya servern och uppge följande information:
   - Resursgrupp: Klicka på den **Skapa nytt** länken under textrutan för det här fältet. Ange ett namn som **myresourcegroup**.
   - Servergruppnamn: Ange ett unikt namn för den nya servergruppen som också används för en server underdomän.
   - Administratörens användarnamn: Ange ett unikt användarnamn kommer den att användas senare att ansluta till databasen.
   - Lösenord: måste vara minst åtta tecken långt och måste innehålla tecken från tre av följande kategorier-engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv.)
   - Plats: Använd den plats som är närmast dina användare att ge dem snabbast åtkomst till data.

   > [!IMPORTANT]
   > Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

5. Klicka på **konfigurera servergrupp**. Lämna inställningarna i den avsnittet oförändrade och klicka på **spara**.
6. Klicka på **granska + skapa** och sedan **skapa** att etablera servern. Etableringen tar några minuter.
7. Sidan omdirigerar för att övervaka distributionen. Då live status ändras från **distributionen pågår** till **distributionen är klar**, klickar du på den **utdata** menyalternativ till vänster på sidan.
8. Sidan utdata innehåller ett coordinator värdnamn med en knapp bredvid den att kopiera värdet till Urklipp. Registrera den här informationen för senare användning.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för PostgreSQL – hyperskala (Citus) (förhandsgranskning)-tjänsten använder en brandvägg på servernivå. Som standard förhindrar brandväggen alla externa program och verktyg ansluter till koordinatornoden eller databaser i. Vi måste lägga till en regel som öppnar brandväggen för ett specifikt IP-adressintervall.

1. Från den **utdata** avsnitt där du tidigare kopierade coordinator noden värdnamn, klickar du på tillbaka till den **översikt** menyalternativ.

2. Namnet på din distribution, skalning grupp inleds med ”sg-”. Hitta det i listan över resurser och klicka på den.

3. Klicka på **brandväggen** under **Security** på den vänstra menyn.

4. Klicka på länken **+ Lägg till brandväggsregel för aktuella klientens IP-adress**. Klicka slutligen på den **spara** knappen.

5. Klicka på **Spara**.

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Ansluta till databasen med psql i Cloud Shell

Nu använder vi [psql](https://www.postgresql.org/docs/current/app-psql.html)-kommandoradsverktyget för att ansluta till Azure Database for PostgreSQL-servern.
1. Starta Azure Cloud Shell via terminalikonen överst i navigeringsfönstret.

   ![Azure Database för PostgreSQL – Azure Cloud Shell-terminalikonen](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Azure Cloud Shell öppnas i din webbläsare så att du kan skriva bash-kommandon.

   ![Azure Database för PostgreSQL – Azure Shell Bash-prompten](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. I Cloud Shell-prompten ansluter du till din Azure Database för PostgreSQL-server med psql-kommandona. Följande format används för att ansluta till en Azure Database för PostgreSQL-server med [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget:
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Till exempel följande kommando ansluter till standarddatabasen som heter **citus** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange ditt lösenord för serveradministratören när du uppmanas till detta.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Skapa och distribuera tabeller

När du är ansluten till koordinatornoden hyperskala med psql, kan du utföra några grundläggande uppgifter.

I hyperskala finns servrar det tre typer av tabeller:

- Distribuerade eller shardade tabeller (sprida ut för att skalning för prestanda och parallellisering)
- Referenstabeller (flera kopior underhålls)
- Lokala tabeller (som ofta används för interna admin tabeller)

I den här snabbstarten kommer vi främst fokusera på distribuerade tabeller och få bekant med dem.

Vi kommer att arbeta med datamodellen är enkel: användar- och event data från GitHub. Händelserna inkluderar skapa en förgrening, git-skrivningar relaterade till en organisation och mycket mer.

När du har anslutit via psql vi skapa våra tabeller. I psql-konsolen kör:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Den `payload` i `github_events` har datatypen JSONB. JSONB är JSON-datatypen i binär form i Postgres. Detta gör det enkelt att lagra ett mer flexibelt schema i en enda kolumn.

Postgres kan skapa en `GIN` indexet för den här typen som indexerar varje nyckel och värde i den. Med ett index, blir det snabbt och enkelt att fråga nyttolasten med olika villkor. Vi går vidare och skapa några index innan vi läser in våra data. I psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Sedan vi ikläd koordinatornoden dessa Postgres-tabeller och berätta hyperskala till shard dem över ”arbetarna”. Om du vill göra det vi kommer att köra en fråga för varje tabell som anger nyckeln till shard det på. I det aktuella exemplet kommer vi att fragment både händelser och användare tabellen på `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Vi är redo att läsa in data. Hämta filer för två exempel [användare.csv](https://examples.citusdata.com/users.csv) och [events.csv](https://examples.citusdata.com/events.csv). När du laddar ned filerna, ansluta till databasen med psql, var noga med att köra psql från den katalog som innehåller de filer som du hämtade. Läs in data med den `\copy` kommando:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Köra frågor

Nu är det dags för roligt del, som faktiskt kör några frågor. Låt oss börja med en enkel `count (*)` att se hur mycket data som har blivit inläst:

```sql
SELECT count(*) from github_events;
```

Som fungerade ett snyggt sätt. Vi ska gå tillbaka till den typ av aggregering om en stund, men nu ska vi titta på några andra frågor. I JSONB `payload` kolumnen det är en bra bit data, men det varierar beroende på händelsetyp. `PushEvent` händelser som innehåller en storlek som innehåller antalet distinkta incheckningar för push-meddelandet. Vi kan använda den för att hitta det totala antalet skrivningar per timme:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Så här långt frågorna hade det inneburit github\_händelser exklusivt, men vi kan kombinera den här informationen med github\_användare. Eftersom vi shardade både användare och händelser på samma identifierare (`user_id`), raderna i båda tabellerna med matchande användar-ID kommer att [samordnat](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) på samma databas noder och kan enkelt kopplas.

Om vi går med på `user_id`, storskaliga kan skicka join-körningen horisontellt för körning av parallella på arbetsnoderna. Exempelvis kan vi hitta de användare som skapade det högsta antalet databaser:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort servergruppen. Tryck på den **ta bort** knappen i den **översikt** sidan för din servergrupp. När du uppmanas att göra på en popup-sida bekräfta namnet på servergruppen och klickar på sista **ta bort** knappen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du etablerar en servergrupp i hyperskala (Citus). Du är ansluten till den med psql, skapas ett schema och distribuerade data.

Därefter Följ en självstudie för att bygga skalbara program för flera innehavare.
> [!div class="nextstepaction"]
> [Utforma en databas för flera innehavare](https://aka.ms/hyperscale-tutorial-multi-tenant)
