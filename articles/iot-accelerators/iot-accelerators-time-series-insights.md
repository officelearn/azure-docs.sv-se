---
title: Visualisera Data med Azure Time Series Insights för fjärrövervakning | Microsoft Docs
description: Lär dig hur du konfigurerar miljön för Time Series Insights för att utforska och analysera time series-data för lösningen för fjärrövervakning.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: f16fdfca704b8f8cb175de637ad7f3ef143d3ed7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968963"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualisera Remote övervakningsdata med Time Series Insights

En operatör kanske vill utöka out data box visualiseringen som tillhandahålls av den fjärrövervakning förkonfigurera lösning. Vår lösningsaccelerator skyddar från box-integrering med TSD. I den här anvisningen du lära dig hur du konfigurerar Time Series Insights för att analysera enheternas telemetri och identifiera avvikelser.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här anvisningen, behöver du följande:

* [Distribuera av den förkonfigurerade lösningen för fjärrövervakning](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Du måste skapa en dedikerad konsumentgrupp i din IoT-hubb som ska användas för strömmande data på Time Series Insights.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Varje konsumentgrupp kan upp till fem utdata konsumenter. Du bör skapa en ny konsumentgrupp för var femte utdata mottagare och du kan skapa upp till 32 konsumentgrupper.

1. Klicka på knappen Cloud Shell i Azure-portalen.

1. Kör följande kommando för att skapa en ny konsumentgrupp:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny Time Series Insights-miljö

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som du kan använda för att hantera Time Series-data på IoT-skala i molnet. Du får en enormt skalbar datalagringsplats för Time Series-data som du kan använda för att utforska och analysera miljarder händelser som strömmas i hela världen på bara några sekunder. Använd Time Series Insights att lagra och hantera terabyte av Times series-data, utforska och visualisera miljarder händelser samtidigt, utföra Rotorsaksanalys och jämföra flera platser och tillgångar.

1. Logga in på [Azure-portalen](http://portal.azure.com/).

1. Välj **skapa en resurs** > **Internet of Things** > **Time Series Insights**.

    ![Ny Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Skapa din Time Series Insights-miljö använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Miljönamn | Följande skärmbild använder namnet **contorosrmtsi**. Välj ditt eget unika namn när du har slutfört det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Skapa en ny**. Vi använder namnet **ContosoRM**. |
    | Plats | Vi använder **USA, östra**. Skapa din miljö i samma region som din lösning för fjärrövervakning. |
    | Sku |**S1** |
    | Kapacitet | **1** |
    | Fäst vid instrumentpanelen | **Ja** |

    ![Skapa Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Klicka på **Skapa**. Det kan ta en stund medan miljön som ska skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla att ansluta till din IoT-hubb. Se till att du använder konsumentgruppen skapades i föregående steg. Time Series Insights kräver varje tjänst och har en dedikerad konsumentgrupp inte är i användning av en annan tjänst.

1. Gå till din nya Time Series-miljö.

1. Till vänster, Välj **händelsekällor**.

    ![Visa händelsekällor](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**.

    ![Lägga till händelsekälla](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera din IoT-hubb som en ny händelsekälla, använda värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelsekälla namn | Följande skärmbild använder namnet **contosorm-iot-hub**. Använd ditt eget unika namn när du har slutfört det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID | I listrutan väljer du din Azure-prenumeration. |
    | Namnet på IOT hub | **contosorma57a6**. Använd namnet på IoT-hubben från lösningen för fjärrövervakning. |
    | Principnamn för IOT hub | **iothubowner** se till att den princip som används är en ägare. |
    | Principnyckel för IOT hub | Det här fältet fylls i automatiskt. |
    | IOT hub-konsumentgrupp | **timeseriesinsights** |
    | Händelseserialiseringsformat | **JSON**     | Egenskapsnamnet för tidsstämpeln | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Om du vill bevilja ytterligare användare åtkomst till Time Series Insights explorer kan du använda dessa steg för att [bevilja åtkomst till data](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Time Series Insights explorer är en webbapp som hjälper dig att skapa visualiseringar av data.

1. Välj den **översikt** fliken.

1. Klicka på **går du till miljön**, vilket öppnar Time Series Insights explorer-webbapp.

    ![Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Panelen för val av tid Välj **senaste 12 timmarna** från Snabbstart tider menyn och klickar på **Search**.

    ![Time Series Insights Explorer-sökning](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. I panelen villkor till vänster väljer du måttvärdet **temperatur** och delning av värdet **iothub-anslutning-enhet-id**.

    ![Time Series Insights Explorer-fråga](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **utforska händelser**.

    ![Time Series Insights Explorer-händelser](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Händelserna återges i rutnätet i tabellformat.

    ![Time Series Insights Explorer-tabell](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen Visa perspektiv.

    ![Time Series Insights Explorer perspektiv](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka på **Lägg till** att skapa en ny fråga i perspektivet.

    ![Time Series Insights Explorer Lägg till fråga](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Välj en snabb tid på **senaste 12 timmarna**, ett mått på **fuktighet** och en delning av av **iothub-anslutning-enhet-id**.

    ![Time Series Insights Explorer-fråga](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektiv vy för att visa instrumentpanelen enhetsmått.

    ![Time Series Insights Explorer instrumentpanel](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Nästa steg

Läs om hur du utforska och fråga efter data i Time Series Insights explorer i [Azure Time Series Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
