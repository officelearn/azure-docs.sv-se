---
title: Hämtar exempeldata i Azure Data Explorer
description: Lär dig mer om hur du intänder (läser in) väderrelaterade exempeldata i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975184"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Snabbstart: Matar in exempeldata i Azure Data Explorer

Den här artikeln visar hur du inbetar (läser in) exempeldata i en Azure Data Explorer-databas. Det finns [flera sätt att få in data](ingest-data-overview.md); Den här artikeln fokuserar på en grundläggande metod som är lämplig för testning.

> [!NOTE]
> Du har redan dessa data om du har slutfört [Ingest-data med Azure Data Explorer Python-biblioteket](python-ingest-data.md).

## <a name="prerequisites"></a>Krav

[Ett testkluster och en databas](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Mata in data

Exempeldatauppsättningen **StormEvents** innehåller väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Logga in [https://dataexplorer.azure.com](https://dataexplorer.azure.com)på .

1. Överst till vänster i programmet väljer du **Lägg till kluster**.

1. I dialogrutan **Lägg till kluster** anger du `https://<ClusterName>.<Region>.kusto.windows.net/`kluster-URL:en i formuläret och väljer sedan **Lägg till**.

1. Klistra in följande kommando och välj **Kör** för att skapa en StormEvents-tabell.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Klistra in följande kommando och välj **Kör** för att förtära data i tabellen StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. När inmatningen är klar klistrar du in i följande fråga, markerar frågan i fönstret och väljer **Kör**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Frågan returnerar följande resultat från de intagna exempeldata.

    ![Frågeresultat](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Nästa steg

* [Azure Data Explorer datainmatning](ingest-data-overview.md) om du vill veta mer om inmatningsmetoder.
* [Snabbstart: Frågedata i Azure Data Explorer](web-query-data.md) Webbgränssnitt.
* [Skriv frågor](write-queries.md) med Kusto Query Language.
