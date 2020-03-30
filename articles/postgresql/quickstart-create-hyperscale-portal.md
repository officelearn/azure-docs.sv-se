---
title: Skapa distribuerade tabeller - Hyperskala (Citus) - Azure Database för PostgreSQL
description: Snabbstart för att skapa och fråga distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241516"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Snabbstart: Skapa en Azure-databas för PostgreSQL - Hyperskala (Citus) i Azure-portalen

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure-databas för PostgreSQL - Citus-servergrupp (Hyperscale) med hjälp av Azure-portalen. Du ska utforska distribuerade data: fragmentera tabeller över noder, inta exempeldata och köra frågor som körs på flera noder.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Skapa och distribuera tabeller

När du är ansluten till den hyperskalakoordinatornoden med psql kan du utföra vissa grundläggande uppgifter.

Inom Hyperscale-servrar finns det tre typer av tabeller:

- Distribuerade eller fragmenterade tabeller (utspridda för att hjälpa till att skala för prestanda och parallellisering)
- Referenstabeller (flera kopior underhålls)
- Lokala tabeller (används ofta för interna administratörstabeller)

I den här snabbstarten fokuserar vi främst på distribuerade tabeller och bekanta oss med dem.

Datamodellen vi ska arbeta med är enkel: användar- och händelsedata från GitHub. Händelser inkluderar skapande av gaffel, git commits relaterade till en organisation och mycket mer.

När du har anslutit via psql, låt oss skapa våra tabeller. I psql konsolen köra:

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

Fältet `payload` `github_events` har en JSONB-datatyp. JSONB är JSON-datatypen i binär form i Postgres. Datatypen gör det enkelt att lagra ett flexibelt schema i en enda kolumn.

Postgres kan `GIN` skapa ett index på den här typen, som indexerar varje nyckel och värde i den. Med ett index blir det snabbt och enkelt att fråga nyttolasten med olika villkor. Låt oss gå vidare och skapa ett par index innan vi laddar våra data. I psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Därefter tar vi postgres-tabellerna på koordinatornsnoden och säger till Hyperscale att fragmentera dem över arbetarna. För att göra det kör vi en fråga för varje tabell som anger nyckeln för att fragmentera den. I det aktuella exemplet ska vi fragmentera `user_id`både händelser och användare tabell på:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Vi är redo att ladda data. I psql fortfarande, punga ut för att ladda ner filerna:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Läs sedan in data från filerna i de distribuerade tabellerna:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Köra frågor

Nu är det dags för den roliga delen, faktiskt kör några frågor. Låt oss börja med `count (*)` en enkel att se hur mycket data vi läst in:

```sql
SELECT count(*) from github_events;
```

Det fungerade fint. Vi kommer tillbaka till den typen av aggregering i lite, men för nu ska vi titta på några andra frågor. I JSONB-kolumnen `payload` finns det en bra bit data, men den varierar beroende på händelsetyp. `PushEvent`händelser innehåller en storlek som innehåller antalet olika åtaganden för push. Vi kan använda den för att hitta det totala antalet åtaganden per timme:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Hittills har frågorna involverat github-händelserna\_uteslutande, men vi kan kombinera\_den här informationen med github-användare. Eftersom vi fragmenterade både användare och händelser`user_id`på samma identifierare ( ) kommer raderna i båda tabellerna med matchande användar-ID:er att [samlokaliseras](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) på samma databasnoder och kan enkelt anslutas.

Om vi `user_id`går med på kan Hyperskala trycka ned kopplingskörningen till shards för körning parallellt på arbetsnoder. Låt oss till exempel hitta de användare som skapade det största antalet databaser:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort servergruppen. Tryck på knappen **Ta bort** på sidan **Översikt** för servergruppen. När du uppmanas att göra det på en popup-sida **Delete** bekräftar du namnet på servergruppen och klickar på den slutliga delete-knappen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig att etablera en citus-servergrupp (Hyperscale). Du har anslutit till den med psql, skapat ett schema och distribuerade data.

Följ sedan en självstudiekurs för att skapa skalbara program med flera innehavare.
> [!div class="nextstepaction"]
> [Utforma en databas med flera innehavare](https://aka.ms/hyperscale-tutorial-multi-tenant)
