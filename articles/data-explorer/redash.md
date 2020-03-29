---
title: Visualisera Azure Data Explorer med Redash
description: I den här artikeln får du lära dig hur du visualiserar data i Azure Data Explorer med Redash native connector.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773946"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualisera data från Azure Data Explorer i Redash

[Redash](https://redash.io/) ansluter och frågar dina datakällor, skapar instrumentpaneler för att visualisera data och dela dem med peer-datorer. I den här artikeln får du lära dig hur du konfigurerar Azure Data Explorer som en datakälla för Redash och sedan visualiserar data.

## <a name="prerequisites"></a>Krav

1. [Skapa kluster och databas](create-cluster-database-portal.md).
1. Inta data enligt beskrivningen i [intag av exempeldata i Azure Data Explorer](ingest-sample-data.md). Fler intagsalternativ finns [i översikt över inmatning](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Skapa Azure Data Explorer Connector i Redash 

1. Logga in på [Redash](https://www.redash.io/). Välj **Kom igång** om du vill skapa ett konto.
1. Under **Kom igång**väljer du Anslut en **datakälla**.

    ![Anslut en datakälla](media/redash/connect-data-source.png)

1. I Fönstret **Skapa en ny datakälla** väljer du **Azure Data Explorer (Kusto)** och väljer sedan **Skapa**. 

    ![Välj Azure Data Explorer-datakälla](media/redash/select-adx-data-source.png)

1. I **Fönstret Azure Data Explorer (Kusto)** fyller du i följande formulär och väljer **Skapa**.

    ![Fönstret Inställningar för Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. I fönstret **Inställningar** väljer du **Spara** och **testa anslutning** för att testa din **Kusto-datakällaanslutning (Azure Data Explorer).**

## <a name="create-queries-in-redash"></a>Skapa frågor i Redash

1. Längst upp till vänster i Redash väljer du **Skapa** > **fråga**. Klicka på **Ny fråga** och byt namn på frågan.

    ![Skapa fråga](media/redash/create-query.png)

1. Skriv frågan i det övre redigeringsfönstret och välj **Spara** och **kör**. Välj **Publicera** för att publicera frågan för framtida bruk.

    ![Spara och kör fråga](media/redash/save-and-execute-query.png)

    I den vänstra rutan kan du se anslutningsnamnet för datakällan **(Github-anslutning** i vårt flöde) i den nedrullningsbara menyn och tabellerna i den valda databasen. 

1. Visa frågeresultaten i det nedre mittfönstret. Skapa en visualisering som du vill gå med frågan genom att välja knappen **Ny visualisering.**

    ![Ny visualisering](media/redash/new-visualization.png)

1. På visualiseringsskärmen väljer du **visualiseringstypen** och relevanta fält som **X-kolumn** och **Y-kolumn**. **Spara** visualiseringen.

    ![Konfigurera och spara visualisering](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Skapa en fråga med hjälp av en parameter

1. **Skapa** > **fråga** för att skapa en ny fråga. Lägg till en parameter{}i den med { } kladdiga parenteser. Välj **{}{ }** om du vill öppna fönstret **Lägg till parameter.** Du kan också välja *inställningsikonen* för att ändra attributen för en befintlig parameter och öppna **fönstret<parameter_name>.** 

    ![parameter infoga](media/redash/insert-parameter.png)

1. Ge din parameter ett namn. Välj **Typ:** **Lista med frågebaserad list** från den rullgardinsmenyn. Välj **OK**

    ![lista över frågebaserade](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Frågan använder flera värden, därför måste `| where Type in ((split('{{Type}}', ',')))`du inkludera följande syntax . Mer information finns [i operatorn](/azure/kusto/query/inoperator). Detta resulterar i [flera frågeparameteralternativ i redash-appen](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Skapa en instrumentpanel i Redash

1. Skapa instrumentpanel **Create** > för att skapa**instrumentpanelen**. Du kan också välja befintlig instrumentpanel, **instrumentpaneler** > välja en instrumentpanel i listan.

    ![Skapa instrumentpanel](media/redash/create-dashboard.png)

1. I fönstret **Ny instrumentpanel** namnger du instrumentpanelen och väljer **Spara**. I **<Dashboard_name>** fönster väljer du **Lägg till widget** för att skapa en ny widget. 

1. Välj frågenamn i fönstret **Lägg till widget,** **Välj visualisering**och **Parametrar**. Välj **Lägg till i instrumentpanelen**

   ![Välj visualiseringar och lägg till på instrumentpanelen](media/redash/add-widget-window.png)

1. Välj **Klar redigering** för att skapa instrumentpanelen.

1.  I instrumentpanelsredigeringsläget väljer du **Använd filtrfilter på instrumentpanelsnivå** om du vill använda den **textparameter** som tidigare definierats.

    ![Fullständig skapande av instrumentpaneler](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)


