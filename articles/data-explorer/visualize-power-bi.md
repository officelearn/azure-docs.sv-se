---
title: 'Självstudier: Visualisera data från Azure Data Explorer i Power BI'
description: I den här självstudien får du lära dig hur du ansluter till Azure Data Explorer med Power BI och visualiserar dina data.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 70a06b75db9ff49222c2de4aa78519b32d863478
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049888"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Självstudier: Visualisera data från Azure Data Explorer i Power BI

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Power BI är en lösning för företagsanalys där du kan visualisera dina data och dela resultaten i hela organisationen. I den här självstudien får du först lära dig hur du renderar visuella objekt i Azure Data Explorer. Därefter ansluter du till Azure Data Explorer med Power BI, skapar en rapport baserad på exempeldata och publicerar rapporten till Power BI-tjänsten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. Om du inte har registrerat dig för Power BI Pro så [registrerar du dig för en kostnadsfri utvärderingsversion](https://app.powerbi.com/signupredirect?pbi_source=web) innan du börjar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Rendera visuella objekt i Azure Data Explorer
> * Anslut till Azure Data Explorer i Power BI Desktop
> * Arbeta med data i Power BI Desktop
> * Skapa en rapport med visuella objekt
> * Publicera och dela rapporten

## <a name="prerequisites"></a>Förutsättningar

Förutom Azure- och Power BI-prenumerationer så behöver du följande för att slutföra den här självstudien:

* [En testkluster och databas](create-cluster-database-portal.md)

* [StormEvents exempeldata](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (välj **DOWNLOAD FREE**) (Ladda ned kostnadsfritt)

## <a name="render-visuals-in-azure-data-explorer"></a>Rendera visuella objekt i Azure Data Explorer

Innan vi går vidare till Power BI, tar vi en titt på hur du kan rendera visuella objekt i Azure Data Explorer. Det är perfekt för snabbanalys.

1. Logga in på [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. I vänster fönster, väljer du testdatabasen som innehåller StormEvents exempeldata.

1. Klistra in följande fråga i det högra fönstret och välj **Kör**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Den här frågan räknar väderhändelser efter delstat. Den renderar sedan ett stapeldiagram för alla delstater som har mer än 1 800 väderhändelser.

    ![Stapeldiagram för händelser](media/visualize-power-bi/events-column-chart.png)

1. Klistra in följande fråga i det högra fönstret och välj **Kör**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Den här frågan räknar antal väderhändelser efter typ för juli månad i delstaten Washington. Den renderar därefter ett cirkeldiagram som visar procentandelen av varje händelsetyp.

    ![Händelser cirkeldiagram](media/visualize-power-bi/events-pie-chart.png)

Nu är dags att titta på Power BI, men det finns mycket mer du kan göra med visuella objekt i Azure Data Explorer.

## <a name="connect-to-azure-data-explorer"></a>Anslut till Azure Data Explorer

Anslut nu till Azure Data Explorer i Power BI Desktop.

1. I Power BI Desktop går du till fliken **Start**, väljer **Hämta data** och sedan **Mer**.

    ![Hämta data](media/visualize-power-bi/get-data-more.png)

1. Sök efter *Azure Data Explorer*, välj **Azure Data Explorer (Beta)** och därefter **Anslut**.

    ![Sök efter och hämta data](media/visualize-power-bi/search-get-data.png)

1. På skärmen **Förhandsgranska anslutningsapp** väljer du **Fortsätt**.

1. På nästa skärm anger du namnet på ditt testkluster och databas. Kluster bör vara i formen `https://<ClusterName>.<Region>.kusto.windows.net`. Ange *StormEvents* för namnet på tabellen. Lämna alla andra alternativ med standardvärden och välj **OK**.

    ![Kluster-, databas-, tabellalternativ](media/visualize-power-bi/cluster-database-table.png)

1. På skärmen förhandsgranskning av data väljer du **Redigera**.

    Tabellen öppnas i Power Query Editor, där du kan redigera rader och kolumner innan du importerar data.

## <a name="work-with-data-in-power-bi-desktop"></a>Arbeta med data i Power BI Desktop

Nu när du har en anslutning till Azure Data Explorer kan du redigera data i Power Query Editor. Du släpper rader med null-värden i **BeginLat**-kolumnen och släpper **StormSummary** JSON-kolumnen helt och hållet. Det här är enkla åtgärder, men du kan även utföra komplexa transformeringar när du importerar data.

1. Välj pilen för **BeginLat**-kolumnen, rensa **null**-kryssrutan och välj sedan **OK**.

    ![Filterkolumn](media/visualize-power-bi/filter-column.png)

1. Högerklicka på kolumnrubriken **StormSummary** och välj **Ta bort**.

    ![Ta bort kolumn](media/visualize-power-bi/remove-column.png)

1. I fönstret **Frågeinställningar**, ändrar du namnet från *Query1* till *StormEvents*.

    ![Ändra frågans namn](media/visualize-power-bi/query-name.png)

1. På **Start**-fliken på menyfliksområdet, väljer du **Stäng och tillämpa**.

    ![Stäng och tillämpa](media/visualize-power-bi/close-apply.png)

    Power Query tillämpar dina ändringar och importerar därefter exempeldata i en *datamodellen*. Nästa steg visar hur du utökar den modellen. Det här är bara ett enkelt exempel för att ge dig en uppfattning om vad som är möjligt.

1. PÅ vänster sida om huvudfönstret, väljer du datavy.

    ![Datavy](media/visualize-power-bi/data-view.png)

1. På fliken **Modellering** på menyfliksområdet, väljer du **Ny kolumn**.

    ![Ny kolumn](media/visualize-power-bi/new-column.png)

1. Ange följande Dataanalysuttryck (DAX)-formel i formelfältet och tryck på Retur.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Formelfält](media/visualize-power-bi/formula-bar.png)

    Den här formeln skapar kolumnen *DurationHours* som beräknar hur många timmar varje väderhändelse varade. Du använder den här kolumnen i ett visuellt objekt i nästa avsnitt.

1. Bläddra ned till höger i tabellen för att se kolumnen.

## <a name="create-a-report-with-visuals"></a>Skapa en rapport med visuella objekt

Nu när data har importerats och du har förbättrat datamodellen, är det dags att skapa en rapport med visuella objekt. Du lägger till ett stapeldiagram baserat på händelsens varaktighet och en karta som visar skada på grödor.

1. På vänster sida om fönstret väljer du rapportvyn.

    ![Rapportvy](media/visualize-power-bi/report-view.png)

1. I fönstret **Visualiseringar** väljer du grupperat stapeldiagram.

    ![Lägg till stapeldiagram](media/visualize-power-bi/add-column-chart.png)

    Ett tomt diagram har lagts till i arbetsytan.

    ![Tomt diagram](media/visualize-power-bi/blank-chart.png)

1. I listan **Fält**, väljer du **DurationHours** och **Delstat**.

    ![Välj fält](media/visualize-power-bi/select-fields.png)

    Nu har du ett diagram som visar det totala antalet timmar för väderhändelser efter delstat under loppet av ett år.

    ![Varaktighet för stapeldiagram](media/visualize-power-bi/duration-column-chart.png)

1. Klicka någonstans på arbetsytan utanför stapeldiagrammet.

1. I fönstret **Visualiseringar** väljer du kartan.

    ![Lägg till karta](media/visualize-power-bi/add-map.png)

1. I listan **Fält**, väljer du **CropDamage** och **Delstat**. Ändra storlek på kartan så att du kan se de amerikanska delstaterna tydligt.

    ![Karta över skada på grödor](media/visualize-power-bi/crop-damage-map.png)

    Storleken på bubblorna representerar dollarvärdet för skadan på grödor. För muspekaren över bubblorna för att se information.

1. Flytta och ändra storlek på de visuella objekten så att du har en rapport som ser ut som följande bild.

    ![Färdig rapport](media/visualize-power-bi/finished-report.png)

1. Spara rapporten med namnet *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publicera och dela rapporten

Fram tills nu har det arbete som du har gjort i Power BI varit lokalt med Power BI Desktop. Nu kan du publicera rapporten till Power BI-tjänsten där du kan dela den med andra.

1. I Power BI Desktop, på **Start**-fliken i menyfliksområdet, väljer du **Publicera**.

    ![Knappen Publicera](media/visualize-power-bi/publish-button.png)

1. Om du inte redan är inloggad på Power BI, går du igenom inloggningsprocessen.

1. Välj **Min arbetsyta** och därefter **Välj**.

    ![Välj arbetsyta](media/visualize-power-bi/select-workspace.png)

1. När publiceringen är klar, väljer du **Öppna storm-events.pbix i Power BI**.

    ![Publiceringen är klar](media/visualize-power-bi/publishing-succeeded.png)

    Rapporten öppnas i tjänsten med samma visuella objekt och layout som du definierade i Power BI Desktop.

1. I det övre högra hörnet av rapporten väljer du **Dela**.

    ![Dela-knappen](media/visualize-power-bi/share-button.png)

1. På skärmen **Dela rapporten**, lägger du till en kollega i din organisation, lägger till en anteckning och väljer sedan **Dela**.

    ![Dela rapporten](media/visualize-power-bi/share-report.png)

    Om din kollega har rätt behörigheter så kan de komma åt den rapport som du delade.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill behålla den rapport som du skapade, tar du helt enkelt bort filen *storm events.pbix*. Följ dessa steg om du vill ta bort den rapport som du publicerade.

1. Under **Min arbetsyta**, bläddrar du ned till **Rapporter** och hittar **storm-events**.

1. Välj ellipsen (**. . .**) bredvid **storm-events** och välj därefter **Ta bort**.

    ![Ta bort rapporten](media/visualize-power-bi/remove-report.png)

1. Bekräfta borttagningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriva frågor](write-queries.md)
