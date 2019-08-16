---
title: Mata in exempel data i Azure Datautforskaren
description: Läs om hur du matar in (läser in) väder relaterade exempel data till Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: c803de599f6be98512b15e927c6d15f1c7d95ff1
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515751"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Snabbstart: Mata in exempel data i Azure Datautforskaren

Den här artikeln visar hur du matar in exempel data i en Azure Datautforskaren Database. Det finns [flera sätt att mata in data på](ingest-data-overview.md). den här artikeln fokuserar på en grundläggande metod som är lämplig för test ändamål.

> [!NOTE]
> Du har redan dessa data om du har slutfört inmatnings [data med Azure datautforskaren python-biblioteket](python-ingest-data.md).

## <a name="prerequisites"></a>Förutsättningar

[Ett testkluster och en databas](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Mata in data

I exempeldatauppsättningen **StormEvents** finns väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Överst till vänster i programmet väljer du **Lägg till kluster**.

1. I dialog rutan **Lägg till kluster** anger du din kluster-URL i formuläret `https://<ClusterName>.<Region>.kusto.windows.net/`och väljer sedan **Lägg till**.

1. Klistra in följande kommando och välj **Kör**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. När inmatningen är klar klistrar du in i följande fråga, väljer frågan i fönstret och väljer **Kör**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Frågan returnerar följande resultat från de inmatade exempel data.

    ![Frågeresultat](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Nästa steg

* [Azure datautforskaren data inmatning](ingest-data-overview.md) för att lära dig mer om inmatnings metoder.
* [Snabbstart: Fråga efter data i Azure](web-query-data.md) datautforskaren Web UI.
* [Skriv frågor](write-queries.md) med Kusto-frågespråk.
