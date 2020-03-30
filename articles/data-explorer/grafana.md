---
title: Visualisera data från Azure Data Explorer med Grafana
description: I den här artikeln lär du dig att konfigurera Azure Data Explorer som en datakälla för Grafana och sedan visualisera data från ett exempelkluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038030"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisera data från Azure Data Explorer i Grafana

Grafana är en analysplattform som gör att du kan fråga och visualisera data och sedan skapa och dela instrumentpaneler baserat på dina visualiseringar. Grafana tillhandahåller ett *Plugin-program*för Azure Data Explorer , som gör att du kan ansluta till och visualisera data från Azure Data Explorer. I den här artikeln lär du dig att konfigurera Azure Data Explorer som en datakälla för Grafana och sedan visualisera data från ett exempelkluster.

Använd följande video för att lära dig hur du använder Grafanas Azure Data Explorer-plugin- program, konfigurera Azure Data Explorer som en datakälla för Grafana och visualisera sedan data. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternativt kan du [konfigurera datakällan](#configure-the-data-source) och [visualisera data](#visualize-data) enligt beskrivningen i artikeln nedan.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra den här artikeln:

* [Grafana version 5.3.0 eller senare](https://docs.grafana.org/installation/) för ditt operativsystem

* [Plugin-programmet Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) för Grafana

* Ett kluster som innehåller Exempeldata för StormEvents. Mer information finns i [Snabbstart: Skapa ett Azure Data Explorer-kluster och databas](create-cluster-database-portal.md) och [matar in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Ange egenskaper och testa anslutningen

Med tjänstens huvudnamn tilldelad rollen *tittare* anger du nu egenskaper i din instans av Grafana och testar anslutningen till Azure Data Explorer.

1. I Grafana, på den vänstra menyn, välj kugghjulsikonen sedan **Datakällor**.

    ![Datakällor](media/grafana/data-sources.png)

1. Välj **Lägg till datakälla**.

1. På sidan **Datakällor /Ny** anger du ett namn för datakällan och väljer sedan typen **av Azure Data Explorer Datasource**.

    ![Anslutningsnamn och text](media/grafana/connection-name-type.png)

1. Ange namnet på klustret i formuläret https://{ClusterName}. {Region}.kusto.windows.net. Ange andra värden från Azure-portalen eller CLI. Se tabellen nedan för en mappning.

    ![Anslutningsegenskaper](media/grafana/connection-properties.png)

    | Grafana användargränssnitt | Azure Portal | Azure CLI |
    | --- | --- | --- |
    | Prenumerations-ID | PRENUMERATIONS-ID | SubscriptionId |
    | Klient-ID | Katalog-ID | tenant |
    | Klient-ID | Program-ID:t | appId |
    | Klienthemlighet | lösenord | password |
    | | | |

1. Välj **Spara & test**.

    Om testet lyckas går du till nästa avsnitt. Om du stöter på några problem kontrollerar du de värden som du har angett i Grafana och granskar tidigare steg.

## <a name="visualize-data"></a>Visualisera data

Nu när du har konfigurerat Azure Data Explorer som en datakälla för Grafana är det dags att visualisera data. Vi kommer att visa ett grundläggande exempel här, men det finns mycket mer du kan göra. Vi rekommenderar att du tittar på [skrivfrågor för Azure Data Explorer](write-queries.md) för exempel på andra frågor som ska köras mot exempeldatauppsättningen.

1. I Grafana, på den vänstra menyn, väljer du plusikonen och sedan **instrumentpanelen**.

    ![Skapa instrumentpanel](media/grafana/create-dashboard.png)

1. Välj **Diagram**under fliken **Lägg** till .

    ![Lägg till diagram](media/grafana/add-graph.png)

1. Välj **Panelrubrik** på diagrampanelen **och**redigera sedan .

    ![Panelen Redigera](media/grafana/edit-panel.png)

1. Längst ned på panelen väljer du **Datakälla** och väljer sedan den datakälla som du har konfigurerat.

    ![Välja datakälla](media/grafana/select-data-source.png)

1. Kopiera i följande fråga i frågefönstret och välj sedan **Kör**. Frågan buckets antalet händelser per dag för exempeldatauppsättningen.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Kör frågan](media/grafana/run-query.png)

1. Diagrammet visar inga resultat eftersom det är begränsat som standard till data från de senaste sex timmarna. Välj **Senaste 6 timmar**på den övre menyn .

    ![Senaste sex timmarna](media/grafana/last-six-hours.png)

1. Ange ett anpassat intervall som omfattar 2007, det år som ingår i vår StormEvents exempeldatauppsättning. Välj **Använd**.

    ![Anpassat datumintervall](media/grafana/custom-date-range.png)

    Nu visar diagrammet data från 2007, bucketed by day.

    ![Färdigt diagram](media/grafana/finished-graph.png)

1. Välj ikonen spara på den övre menyn: ![Ikonen Spara](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Skapa aviseringar

1. I Startpanelen väljer du **Aviseringar** > **i meddelandekanaler** för att skapa en ny meddelandekanal

    ![skapa meddelandekanal](media/grafana/create-notification-channel.png)

1. Skapa en ny **meddelandekanal** **och**spara sedan .

    ![Skapa ny meddelandekanal](media/grafana/new-notification-channel-adx.png)

1. Välj **Redigera** i listrutan på **instrumentpanelen.**

    ![välj redigera i instrumentpanelen](media/grafana/edit-panel-4-alert.png)

1. Välj ikonen för varningsklockan för att öppna **varningsfönstret.** Välj **Skapa avisering**. Slutför följande egenskaper i **varningsfönstret.**

    ![varningsegenskaper](media/grafana/alert-properties.png)

1. Välj ikonen **Spara instrumentpanel** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)
