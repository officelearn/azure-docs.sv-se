---
title: Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) Snabbstart
description: Snabbstart för att skapa och fråga distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus) (förhandsversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757518"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Snabbstart: Skapa en Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) i Azure portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) servergrupp med hjälp av Azure portal. Du kommer att utforska distribuerade data: horisontell partitionering tabeller över noder, mata in exempeldata och köra frågor som körs på flera noder.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Skapa och distribuera tabeller

När du är ansluten till koordinatornoden hyperskala med psql, kan du utföra några grundläggande uppgifter.

I hyperskala finns servrar det tre typer av tabeller:

- Distribuerade eller shardade tabeller (sprida ut för att skalning för prestanda och parallellisering)
- Referenstabeller (flera kopior underhålls)
- Lokala tabeller (som ofta används för interna admin tabeller)

I den här snabbstarten kommer vi främst fokusera på distribuerade tabeller och få bekant med dem.

Vi kommer att arbeta med datamodellen är enkel: användar- och event data från GitHub. Händelserna inkluderar skapa en förgrening, git-skrivningar relaterade till en organisation och mycket mer.

När du har anslutit via psql kan vi skapa våra tabeller. I psql-konsolen kör:

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

Den `payload` i `github_events` har datatypen JSONB. JSONB är JSON-datatypen i binär form i Postgres. Datatypen gör det enkelt att lagra ett flexibelt schema i en enda kolumn.

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

Vi är redo att läsa in data. I psql fortfarande, shell ska hämta filerna:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Därefter in data från filer i de distribuerade tabellerna:

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
