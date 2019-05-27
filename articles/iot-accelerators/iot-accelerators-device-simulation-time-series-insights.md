---
title: Visualisera simulering av enhetstelemetri med Time Series Insights – Azure | Microsoft Docs
description: Lär dig hur du konfigurerar din Time Series Insights-miljö för att utforska och analysera telemetri som genereras av Enhetssimulering lösningsaccelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834928"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Använd Time Series Insights för att visualisera telemetri som skickas från lösningsaccelerator Enhetssimulering

Lösningsaccelerator Enhetssimulering kan du generera telemetri från simulerade enheter för att testa dina IoT-lösningar. Den här guiden visar hur du visualisera och analysera den simulerade telemetrin som använder en Time Series Insights-miljö.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa stegen i den här guiden behöver du en aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Stegen i den här guiden förutsätter att du har distribuerat lösningsaccelerator Enhetssimulering till din Azure-prenumeration. Om du inte har distribuerat solution accelerator, följer du stegen i den [distribuera och kör en molnbaserad lösning för simulering](quickstart-device-simulation-deploy.md) Snabbstart.

Den här artikeln förutsätter att namnet på utvecklingsacceleratorn är **contoso-simulering**. Ersätt **contoso-simulering** med namnet på utvecklingsacceleratorn som du utför följande steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Du behöver skapa en dedikerad konsumentgrupp i IoT-hubben till stream telemetri till Time Series Insights. En händelsekälla för Time Series Insights bör ha exklusiv användning av en IoT Hub-konsumentgrupp.

Följande steg använder Azure CLI i Azure Cloud Shell för att skapa konsumentgruppen:

1. IoT hub är en av flera resurser som skapades när du distribuerade Enhetssimulering lösningsaccelerator. Kör följande kommando hitta namnet på din IoT hub - Kom ihåg att använda namnet på utvecklingsacceleratorn:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT-hubben är resurs av typen **Microsoft.Devices /**.

1. Lägg till en konsumentgrupp som heter **devicesimulationtsi** till hubben. Använd namnet på din lösning och hub accelerator i följande kommando:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Nu kan du stänga Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny Time Series Insights-miljö

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) är en fullständigt hanterad tjänst för analys, lagring och visualisering för att hantera IoT-skala time series-data i molnet. Skapa en ny Time Series Insights-miljö:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **skapa en resurs** > **Internet of Things** > **Time Series Insights**:

    ![Ny Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Skapa Time Series Insights-miljön i samma resursgrupp som utvecklingsacceleratorn använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Miljönamn | Följande skärmbild använder namnet **Contoso TSI**. Välj ditt eget unika namn när du har slutfört det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Contoso-simulering**. Använd namnet på din lösningsaccelerator. |
    | Location | Det här exemplet används **USA, östra**. Skapa din miljö i samma region som din enhet simulering accelerator. |
    | SKU |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Lägga till Time Series Insights innebär miljö till samma resursgrupp som lösningsaccelerator att det tas bort när du tar bort solution accelerator.

1. Klicka på **Skapa**. Det kan ta några minuter för miljön som ska skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla att ansluta till din IoT-hubb. Använd konsumentgrupp som du skapade i föregående steg. En händelsekälla för Time Series Insights kräver en dedikerad konsumentgrupp inte är i användning av en annan tjänst.

1. Gå till din nya Time Series-miljö i Azure-portalen.

1. Till vänster, klicka på **händelsekällor**:

    ![Visa händelsekällor](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **lägga till**:

    ![Lägga till händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera din IoT-hubb som en ny händelsekälla, använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelsekälla namn | Följande skärmbild använder namnet **contoso-iot-hub**. Använd ditt eget unika namn när du har slutfört det här steget. |
    | Source | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | IoT Hub-namn | **contoso-simulation7d894**. Använd namnet på IoT-hubben från utvecklingsacceleratorn Enhetssimulering. |
    | IoT Hub-principnamn | **iothubowner** |
    | IoT Hub-principnyckel | Det här fältet fylls i automatiskt. |
    | IoT Hub-konsumentgrupp | **devicesimulationtsi** |
    | Händelseserialiseringsformat | **JSON** |
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Du kan [ger ytterligare användare åtkomst](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) till Time Series Insights explorer.

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

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker utforska ytterligare lämna lösningsaccelerator distribueras.

Om du inte längre behöver lösningsaccelerator kan ta bort den från den [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) sidan genom att markera den och sedan på **ta bort lösningen**.

Om du har lagt till Time Series Insights-miljö till resursgruppen för solution accelerator raderas automatiskt när du tar bort solution accelerator. Annars du måste manuellt ta bort Time Series Insights-miljön från Azure-portalen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforska och fråga efter data i Time Series Insights explorer finns [Azure Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md).
