---
title: Mata in exempeldata i Azure Data Explorer
description: Läs mer om hur du mata in (load) väderrelaterade exempeldata i Azure Data Explorer.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7eb0e48a5b66775ac97ed0cab751db0ef367f667
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964623"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Mata in exempeldata i Azure Data Explorer

Den här artikeln visar hur du mata in (load) exempeldata i en Azure Data Explorer-databas. Det finns [flera olika sätt att mata in data](ingest-data-overview.md); den här artikeln fokuserar på en grundläggande metod som passar för testning.

> [!NOTE]
> Du redan har dessa data om du har slutfört [Snabbstart: mata in data med hjälp av Azure Data Explorer Python-klientbiblioteket](python-ingest-data.md).

## <a name="prerequisites"></a>Förutsättningar

[En testkluster och databas](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Mata in data

Den **StormEvents** exempeldatauppsättning innehåller väder-relaterade data från den [National Lagringsprotokollet för miljöinformation](https://www.ncdc.noaa.gov/stormevents/).

1. Logga in på [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com).

1. I det övre vänstra av programmet, väljer **Lägg till klustret**.

1. I den **Lägg till klustret** dialogrutan, ange din kluster-URL i formatet `https://<ClusterName>.<Region>.kusto.windows.net/`och välj sedan **Lägg till**.

1. Klistra in följande kommando och välj **kör**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. När inmatning är klar, klistra in följande fråga, väljer du frågan i fönstret och välj **kör**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Frågan returnerar följande resultat från insamlade exempeldata.

    ![Frågeresultat](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Nästa steg

[Skriva frågor](write-queries.md)

[Azure Data Explorer-datainmatning](ingest-data-overview.md)