---
title: Visualisera data från Azure Datautforskaren med Grafana
description: I den här artikeln lär du dig att konfigurera Azure Datautforskaren som en data källa för Grafana och sedan visualisera data från ett exempel kluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038030"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisera data från Azure Datautforskaren i Grafana

Grafana är en analys plattform som gör det möjligt att fråga och visualisera data och sedan skapa och dela instrument paneler baserat på dina visualiseringar. Grafana tillhandahåller ett Azure Datautforskaren- *plugin-program*som gör att du kan ansluta till och visualisera data från Azure datautforskaren. I den här artikeln lär du dig att konfigurera Azure Datautforskaren som en data källa för Grafana och sedan visualisera data från ett exempel kluster.

Använd följande video för att lära dig hur du använder Grafana-plugin-programmet för Azure Datautforskaren, konfigurerar Azure Datautforskaren som en data källa för Grafana och sedan visualiserar data. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternativt kan du [Konfigurera data källan](#configure-the-data-source) och [visualisera data](#visualize-data) som beskrivs i artikeln nedan.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra den här artikeln:

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
    | Prenumeration-ID | PRENUMERATIONS-ID | SubscriptionId |
    | Klient-ID | Katalog-ID | tenant |
    | Klient-ID | Program-ID:t | appId |
    | Klienthemlighet | Lösenord | lösenord |
    | | | |

1. Välj **spara & test**.

    Om testet lyckas går du till nästa avsnitt. Om du kommer över alla problem, kontrollerar du de värden som du angav i Grafana och granskar föregående steg.

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

## <a name="create-alerts"></a>Skapa aviseringar

1. I Start-instrumentpanelen väljer du **aviseringar** > **aviserings kanaler** för att skapa en ny meddelande kanal

    ![Skapa meddelande kanal](media/grafana/create-notification-channel.png)

1. Skapa en ny **meddelande kanal**och **Spara**sedan.

    ![Skapa ny meddelande kanal](media/grafana/new-notification-channel-adx.png)

1. På **instrument panelen**väljer du **Redigera** i list rutan.

    ![Välj Redigera på instrument panelen](media/grafana/edit-panel-4-alert.png)

1. Välj ikonen aviserings klocka för att öppna **aviserings** fönstret. Välj **skapa avisering**. Slutför följande egenskaper i **aviserings** fönstret.

    ![aviserings egenskaper](media/grafana/alert-properties.png)

1. Välj ikonen **Spara instrument panel** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)
