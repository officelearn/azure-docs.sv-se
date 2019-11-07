---
title: Visualisera data från Azure Datautforskaren med Grafana
description: I den här instruktionen får du lära dig hur du konfigurerar Azure Datautforskaren som en data källa för Grafana och sedan visualiserar data från ett exempel kluster.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581855"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisera data från Azure Datautforskaren i Grafana

Grafana är en analys plattform som gör det möjligt att fråga och visualisera data och sedan skapa och dela instrument paneler baserat på dina visualiseringar. Grafana tillhandahåller ett Azure Datautforskaren- *plugin-program*som gör att du kan ansluta till och visualisera data från Azure datautforskaren. I den här artikeln får du lära dig hur du konfigurerar Azure Datautforskaren som en data källa för Grafana och sedan visualiserar data från ett exempel kluster.

Med hjälp av följande video kan du lära dig att använda Grafana-plugin-programmet för Azure Datautforskaren, konfigurera Azure Datautforskaren som en data källa för Grafana och sedan visualisera data. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternativt kan du [Konfigurera data källan](#configure-the-data-source) och [visualisera data](#visualize-data) som beskrivs i artikeln nedan.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna göra följande:

* [Grafana version 5.3.0 eller senare](https://docs.grafana.org/installation/) för ditt operativ system

* [Azure datautforskaren-plugin-programmet](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) för Grafana

* Ett kluster som innehåller exempel data för StormEvents. Mer information finns i [snabb start: skapa ett azure datautforskaren-kluster och databas](create-cluster-database-portal.md) och mata [in exempel Data i Azure datautforskaren](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Ange egenskaper och testa anslutningen

När tjänstens huvud namn har tilldelats till *visnings* rollen, anger du nu egenskaper i din instans av Grafana och testar anslutningen till Azure datautforskaren.

1. I Grafana väljer du kugg hjuls ikonen på den vänstra menyn och sedan **data källor**.

    ![Datakällor](media/grafana/data-sources.png)

1. Välj **Lägg till data källa**.

1. På sidan **data källor/ny** anger du ett namn för data källan och väljer sedan typen **Azure datautforskaren DataSource**.

    ![Anslutnings namn och-typ](media/grafana/connection-name-type.png)

1. Ange namnet på ditt kluster i formatet https://{kluster namn}. {Region}. kusto. Windows. net. Ange de andra värdena från Azure Portal eller CLI. Se tabellen nedanför följande bild för en mappning.

    ![Anslutningsegenskaper](media/grafana/connection-properties.png)

    | Grafana-gränssnitt | Azure Portal | Azure CLI |
    | --- | --- | --- |
    | Prenumerations-ID | PRENUMERATIONS-ID | SubscriptionId |
    | Klient-ID | Katalog-ID | innehav |
    | Klient-ID | Program-ID:t | appId |
    | Klienthemlighet | Lösenord | lösenord |
    | | | |

1. Välj **spara & test**.

    Om testet lyckas går du till nästa avsnitt. Om du stöter på problem kan du kontrol lera värdena som du angav i Grafana och granska föregående steg.

## <a name="visualize-data"></a>Visualisera data

Nu när du har slutfört konfigurationen av Azure Datautforskaren som en data källa för Grafana är det dags att visualisera data. Vi visar ett Basic-exempel här, men det är mycket mer du kan göra. Vi rekommenderar att du tittar på [Skriv frågor för Azure datautforskaren](write-queries.md) för exempel på andra frågor som ska köras mot exempel data uppsättningen.

1. I Grafana, på den vänstra menyn, väljer du plus ikonen och **instrument panelen**.

    ![Skapa instrument panel](media/grafana/create-dashboard.png)

1. Välj **diagram**på fliken **Lägg till** .

    ![Lägg till diagram](media/grafana/add-graph.png)

1. I diagram panelen väljer du **panel rubrik** och sedan **Redigera**.

    ![Redigera panel](media/grafana/edit-panel.png)

1. Klicka på **data källa** längst ned på panelen och välj sedan den data källa som du konfigurerade.

    ![Välja datakälla](media/grafana/select-data-source.png)

1. I rutan fråga kopierar du i följande fråga och väljer sedan **Kör**. Frågan buckerar antalet händelser per dag för exempel data uppsättningen.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Kör frågan](media/grafana/run-query.png)

1. Grafen visar inte några resultat eftersom de är begränsade till data från de senaste sex timmarna. På den översta menyn väljer du **senaste 6 timmar**.

    ![Senaste sex timmarna](media/grafana/last-six-hours.png)

1. Ange ett anpassat intervall som täcker 2007, året som ingår i vår StormEvents exempel data uppsättning. Välj **Använd**.

    ![Anpassat datum intervall](media/grafana/custom-date-range.png)

    Nu visar diagrammet data från 2007, Bucket per dag.

    ![Diagrammet är klart](media/grafana/finished-graph.png)

1. På den översta menyn väljer du ikonen Spara: ![Ikonen Spara](media/grafana/save-icon.png).

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)

* [Självstudie: visualisera data från Azure Datautforskaren i Power BI](visualize-power-bi.md)
