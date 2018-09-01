---
title: Visualisera simulering av enhetstelemetri med Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar din Time Series Insights-miljö för att utforska och analysera telemetri som genereras av Enhetssimulering lösningsaccelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 49cecca4e5ebef9f43fdda16cfa1fdc2ad7b6f94
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383121"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Använd Time Series Insights för att visualisera telemetri som skickas från lösningsaccelerator Enhetssimulering

Lösningsaccelerator Enhetssimulering kan du generera telemetri från simulerade enheter för att testa dina IoT-lösningar. Den här guiden visar hur du visualisera och analysera den simulerade telemetrin som använder en Time Series Insights-miljö.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här guiden behöver du en aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Stegen i den här guiden förutsätter att du har distribuerat lösningsaccelerator Enhetssimulering till din Azure-prenumeration. Om du inte har distribuerat solution accelerator, följer du stegen i den [distribuera och kör en molnbaserad lösning för simulering](quickstart-device-simulation-deploy.md) Snabbstart.

Den här artikeln förutsätter att namnet på utvecklingsacceleratorn är **contoso-simulering**. Ersätt **contoso-simulering** med namnet på utvecklingsacceleratorn som du utför följande steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>Starta en simulering

Innan du använder Time Series Insights explorer kan du konfigurera Enhetssimulering lösningsaccelerator för att generera telemetri. I följande skärmbild visas en pågående simulering med 10 kylaggregat enheter:

![Kör enhetssimulering](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Time Series Insights explorer är en webbapp som du kan använda för att visualisera din telemetri.

1. I Azure-portalen väljer du Time Series Insights **översikt** fliken.

1. Klicka för att öppna Time Series Insights explorer webbappen **går du till miljön**:

    ![Time Series Insights-utforskaren](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Panelen för val av tid Välj **senaste 30 minuterna** från Snabbstart tider menyn och klickar på **Search**:

    ![Time Series Insights explorer-sökning](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. I panelen villkor till vänster väljer **temperatur** som den **mått** och **iothub-anslutning-enhet-id** som den **delning av** värde:

    ![Time Series Insights explorer fråga](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **utforska händelser**:

    ![Time Series Insights explorer händelser](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Händelsedata innehåller i ett rutnät:

    ![Time Series Insights explorer tabell](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen perspektiv visa:

    ![Time Series Insights explorer perspektiv](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka på **+** att lägga till en ny fråga i perspektivet:

    ![Time Series Insights explorer Lägg till fråga](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Välj **senaste 30 minuterna** som tidsintervallet **fuktighet** som den **mått**, och **iothub-anslutning-enhet-id** som **Delning av** värde:

    ![Time Series Insights explorer fråga](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektiv vyn om du vill visa din telemetri instrumentpanelen:

    ![Time Series Insights explorer instrumentpanel](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforska och fråga efter data i Time Series Insights explorer finns [Azure Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md).
