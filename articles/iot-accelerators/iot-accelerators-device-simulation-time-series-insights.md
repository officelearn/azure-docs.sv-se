---
title: Visualisera simulerad telemetri med Time Series Insights - Azure | Microsoft-dokument
description: Lär dig hur du konfigurerar time series insights-miljön för att utforska och analysera telemetri som genereras av device simulation-lösningsacceleratorn.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889335"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Använd Time Series Insights för att visualisera telemetri som skickas från device simulation-lösningsacceleratorn

Med device simulation-lösningsacceleratorn kan du generera telemetri från simulerade enheter för att testa dina IoT-lösningar. Den här instruktioner kan du visa hur du visualiserar och analyserar den simulerade telemetrin med hjälp av en Time Series Insights-miljö.

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här programguiden behöver du en aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Stegen i den här programguiden förutsätter att du har distribuerat device simulation-lösningsacceleratorn till din Azure-prenumeration. Om du inte har distribuerat lösningsacceleratorn följer du stegen i [snabbstarten för distribuera och kör en molnbaserad enhetssimuleringslösning.](quickstart-device-simulation-deploy.md)

Den här artikeln förutsätter att namnet på lösningsacceleratorn är **contoso-simulering**. Ersätt **contoso-simulering** med namnet på din lösningsaccelerator när du slutför följande steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Du måste skapa en dedikerad konsumentgrupp i din IoT-hubb för att strömma telemetri till Time Series Insights. En händelsekälla i Time Series Insights bör ha exklusiv användning av en IoT Hub-konsumentgrupp.

I följande steg används Azure CLI i Azure Cloud Shell för att skapa konsumentgruppen:

1. IoT-hubben är en av flera resurser som skapades när du distribuerade lösningsacceleratorn för enhetssimulering. Kör följande kommando hitta namnet på din IoT-hubb - kom ihåg att använda namnet på din lösningsaccelerator:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT-hubben är resursen av typen **Microsoft.Devices/IotHubs**.

1. Lägg till en konsumentgrupp som kallas **devicesimulationtsi** till navet. I följande kommando använder du namnet på hubben och lösningsacceleratorn:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Du kan nu stänga Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny Time Series Insights-miljö

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) är en fullständigt hanterad analys-, lagrings- och visualiseringstjänst för hantering av tidsseriedata i IoT-skala i molnet. Så här skapar du en ny Time Series Insights-miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Skapa en resurs** > **Sakernas** > **Internet-insikter i tidsserier:**

    ![Nya Time Series Insikter](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Om du vill skapa en Time Series Insights-miljö i samma resursgrupp som lösningsacceleratorn använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på miljö | I följande skärmbild används namnet **Contoso-TSI**. Välj ditt eget unika namn när du slutför det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **contoso-simulering**. Använd namnet på lösningsacceleratorn. |
    | Location | I det här exemplet används **östra USA**. Skapa din miljö i samma region som enhetssimuleringsacceleratorn. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa insikter i tidsserier](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Att lägga till time series insights-miljön i samma resursgrupp som lösningsacceleratorn innebär att den tas bort när du tar bort lösningsacceleratorn.

1. Klicka på **Skapa**. Det kan ta några minuter innan miljön skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla för att ansluta till din IoT-hubb. Använd den konsumentgrupp som du skapade i föregående steg. En time series Insights-händelsekälla kräver en dedikerad konsumentgrupp som inte används av en annan tjänst.

1. Navigera till din nya Tidsseriemiljö i Azure-portalen.

1. Till vänster klickar du på **Händelsekällor:**

    ![Visa händelsekällor](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till:**

    ![Lägg till händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera IoT-hubben som en ny händelsekälla använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på händelsekälla | Följande skärmbild använder namnet **contoso-iot-hub**. Använd ditt eget unika namn när du slutför det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använda IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | Iot hub-namn | **contoso-simulering7d894**. Använd namnet på din IoT-hubb från device simulation-lösningsacceleratorn. |
    | Principnamn för IoT Hub | **iothubowner** |
    | Principnyckel för Iot-hubb | Det här fältet fylls i automatiskt. |
    | Iot hub konsumentgrupp | **enheterimulationtsi** |
    | Händelseserialiseringsformat | **Json** |
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Du kan [ge ytterligare användare åtkomst](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) till Utforskaren för Time Series Insights.

## <a name="start-a-simulation"></a>Starta en simulering

Konfigurera lösningsacceleratorn för enhetssimulering för att generera viss telemetri innan du använder Utforskaren för Time Series Insights. Följande skärmbild visar en simulering som körs med 10 kylmaskiner:

![Simulering av enheten körs](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Tidsseriestatistikutforskaren är en webbapp som du kan använda för att visualisera din telemetri.

1. Välj **fliken Översikt över Time** Series Insights i Azure-portalen.

1. Om du vill öppna webbappen Time Series Insights Explorer klickar du på **Gå till miljö:**

    ![Time Series Insights-utforskaren](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. I tidsvalspanelen väljer du **Senaste 30 minuterna** på snabbtidsmenyn och klickar på **Sök:**

    ![Explorer-sökning i Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. I termer panelen till vänster, välj **temperatur** som **Mått** och **iothub-anslutning-enhet-id** som **Split By** värde:

    ![Informationsfråga för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **Utforska händelser:**

    ![Explorer-händelser för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Händelsedata visas i ett rutnät:

    ![Explorer-tabellen För Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen perspektivvy:

    ![Explorer-perspektiv för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka **+** här om du vill lägga till en ny fråga i perspektivet:

    ![Informationsutforskaren Lägg till fråga för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Välj **Senaste 30 minuterna** som tidsspann, **Luftfuktighet** som **mått**och **iothub-anslutning-enhet-ID** som **delning** efter-värde:

    ![Informationsfråga för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektivvy om du vill visa instrumentpanelen för enhetstelemetri:

    ![Instrumentpanelen för Utforskaren för Tidsseriestatistik](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska ytterligare lämnar du lösningsacceleratorn distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan på **Ta bort lösning**.

Om du har lagt till time series insights-miljön i lösningsacceleratorns resursgrupp tas den automatiskt bort när du tar bort lösningsacceleratorn. Annars måste du manuellt ta bort time series insights-miljön från Azure-portalen.

## <a name="next-steps"></a>Efterföljande moment

Mer information om hur du utforskar och frågar data i utforskaren Time Series Insights finns i Utforskaren för [Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
