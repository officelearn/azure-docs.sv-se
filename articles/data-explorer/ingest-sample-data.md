---
title: Mata in exempeldata i Azure Data Explorer
description: Läs mer om hur du mata in (load) väderrelaterade exempeldata i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e80322cda671e2145cf3e65aa1457f1fa1827737
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050636"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Mata in exempeldata i Azure Data Explorer

Den här artikeln visar hur du mata in (load) exempeldata i en Azure Data Explorer-databas. Det finns [flera olika sätt att mata in data](ingest-data-overview.md); den här artikeln fokuserar på en grundläggande metod som passar för testning.

> [!NOTE]
> Du redan har dessa data om du har slutfört [Snabbstart: Mata in data med hjälp av Azure Data Explorer Python-klientbiblioteket](python-ingest-data.md).

## <a name="prerequisites"></a>Förutsättningar

[En testkluster och databas](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Mata in data

I exempeldatauppsättningen **StormEvents** finns väderrelaterade data från [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Överst till vänster i programmet väljer du **Lägg till kluster**.

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

> [!div class="nextstepaction"]
> [Snabbstart: Fråga efter data i Azure Data Explorer](web-query-data.md)

> [!div class="nextstepaction"]
> [Skriva frågor](write-queries.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer-datainmatning](ingest-data-overview.md)