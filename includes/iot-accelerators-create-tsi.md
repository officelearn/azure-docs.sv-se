---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ab3d4cbe8a200e91d02177e49446065cd9c228df
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609080"
---
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

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) är en fullständigt hanterad tjänst för analys, lagring och visualisering för att hantera IoT-skala time series-data i molnet. Skapa en ny Time Series Insights-miljö:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **skapa en resurs** > **Internet of Things** > **Time Series Insights**:

    ![Ny Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Skapa Time Series Insights-miljön i samma resursgrupp som utvecklingsacceleratorn använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Miljönamn | Följande skärmbild använder namnet **Contoso TSI**. Välj ditt eget unika namn när du har slutfört det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Contoso-simulering**. Använd namnet på din lösningsaccelerator. |
    | Plats | Det här exemplet används **USA, östra**. Skapa din miljö i samma region som din enhet simulering accelerator. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Lägga till Time Series Insights innebär miljö till samma resursgrupp som lösningsaccelerator att det tas bort när du tar bort solution accelerator.

1. Klicka på **Skapa**. Det kan ta några minuter för miljön som ska skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla att ansluta till din IoT-hubb. Använd konsumentgrupp som du skapade i föregående steg. En händelsekälla för Time Series Insights kräver en dedikerad konsumentgrupp inte är i användning av en annan tjänst.

1. Gå till din nya Time Series-miljö i Azure-portalen.

1. Till vänster, klicka på **händelsekällor**:

    ![Visa händelsekällor](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Klicka på **lägga till**:

    ![Lägga till händelsekälla](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera din IoT-hubb som en ny händelsekälla, använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelsekälla namn | Följande skärmbild använder namnet **contoso-iot-hub**. Använd ditt eget unika namn när du har slutfört det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumeration-ID | I listrutan väljer du din Azure-prenumeration. |
    | Namnet på IOT hub | **Contoso-simulation7d894**. Använd namnet på IoT-hubben från utvecklingsacceleratorn Enhetssimulering. |
    | Principnamn för IOT hub | **iothubowner** |
    | Principnyckel för IOT hub | Det här fältet fylls i automatiskt. |
    | IOT hub-konsumentgrupp | **devicesimulationtsi** |
    | Händelseserialiseringsformat | **JSON** |
    | Egenskapsnamnet för tidsstämpeln | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Du kan [ger ytterligare användare åtkomst](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) till Time Series Insights explorer.