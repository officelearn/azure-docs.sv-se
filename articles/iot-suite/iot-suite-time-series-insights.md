---
title: Visualisera Remote övervakningsdata med Azure tid serien insikter | Microsoft Docs
description: Lär dig mer om att konfigurera miljön för att utforska och analysera serien tidsdata i lösningen Fjärrövervaknings tid serien insikter.
services: ''
suite: iot-suite
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: article
ms.service: iot-suite
ms.openlocfilehash: 12248a719d7d30c9b83be9c72539e0a81f19fb7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualisera Remote övervakningsdata med tiden serien insikter

En operator kanske vill utöka out rutan data visualiseringen som tillhandahålls av fjärråtkomst övervakning förkonfigurera lösning. Vår utvecklingsaccelerator ger out-of-box-integrering med TSD. I den här anvisningar du lära dig hur du konfigurerar tid serien insikter om du vill analysera enhetstelemetrin och identifiera avvikelser.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här anvisningar:

* [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Du behöver skapa en dedikerad konsumentgrupp i din IoT-hubb som ska användas för strömmande data till tid serien insikter.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Varje konsumentgrupp kan upp till fem konsumenterna i utdata. Du bör skapa en ny konsumentgrupp för var femte utdata sänkor och du kan skapa upp till 32 konsumentgrupper.

1. Klicka på knappen molnet Shell i Azure-portalen.

1. Kör följande kommando för att skapa en ny konsumentgrupp:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny tid serien insikter-miljö

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som du kan använda för att hantera Time Series-data på IoT-skala i molnet. Du får en enormt skalbar datalagringsplats för Time Series-data som du kan använda för att utforska och analysera miljarder händelser som strömmas i hela världen på bara några sekunder. Använd tid serien insikter att lagra och hantera terabyte time series-data, utforska, och visualisera miljarder händelser samtidigt, genomföra analys av grundorsaken, och jämföra flera webbplatser och tillgångar.

1. Logga in på [Azure-portalen](http://portal.azure.com/).

1. Välj **skapar du en resurs** > **Sakernas Internet** > **tid serien insikter**.

    ![Nya tid serien insikter](media/iot-suite-time-series-insights/new-time-series-insights.png)

1. Använd värdena i följande tabell för att skapa tid serien insikter miljön:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Följande skärmbild använder namnet **contorosrmtsi**. Välj ditt eget unikt namn när du slutför det här steget. |
    | Prenumeration | Välj din Azure-prenumeration i listrutan. |
    | Resursgrupp | **Skapa en ny**. Vi använder namnet **ContosoRM**. |
    | Plats | Vi använder **östra USA**. Skapa miljön i samma region som din lösning för övervakning av fjärråtkomst. |
    | Sku |**S1** |
    | Kapacitet | **1** |
    | Fäst vid instrumentpanelen | **Ja** |

    ![Skapa insikter om serien tid](media/iot-suite-time-series-insights/new-time-series-insights-create.png)

1. Klicka på **Skapa**. Det kan ta en stund miljö skapas.

## <a name="create-event-source"></a>Skapa händelsekällan

Skapa en ny händelsekälla att ansluta till din IoT-hubb. Kontrollera att du använder konsumentgrupp skapade i föregående steg. Tid serien insikter kräver varje tjänst har en dedikerad konsumentgrupp inte användning av en annan tjänst.

1. Gå till din nya serie miljön.

1. Till vänster, Välj **händelsekällor**.

    ![Visa händelsekällor](media/iot-suite-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**.

    ![Lägg till händelsekälla](media/iot-suite-time-series-insights/time-series-insights-event-sources-add.png)

1. Använd värdena i tabellen nedan för att konfigurera din IoT-hubb som en ny händelsekälla:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelsekälla namn | Följande skärmbild använder namnet **contosorm iot hub**. Använda ditt eget unikt namn när du slutför det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT-hubb från tillgängliga prenumerationer** |
    | Prenumeration-ID | Välj din Azure-prenumeration i listrutan. |
    | IoT Hub-namn | **contosorma57a6**. Använd namnet på din IoT-hubb från din lösning för övervakning av fjärråtkomst. |
    | IoT Hub-principnamn | **iothubowner** se till att den princip som används är en ägare. |
    | IoT Hub-principnyckel | Det här fältet fylls i automatiskt. |
    | IoT Hub-konsumentgrupp | **timeseriesinsights** |
    | Händelseserialiseringsformat | **JSON**     | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekällan](media/iot-suite-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Om du behöver ge ytterligare användare åtkomst till Utforskaren tid serien insikter du kan använda dessa steg för att [bevilja åtkomst till data](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Tid serien insikter explorer är en webbapp som hjälper dig att skapa visualiseringar av dina data.

1. Välj den **översikt** fliken.

1. Klicka på **går du till miljön**, som kan öppna tid serien insikter explorer webbapp.

    ![Time Series Insights Explorer](media/iot-suite-time-series-insights/time-series-insights-environment.png)

1. I panelen tid markeringen väljer **senaste 12 timmarna** från snabbstartsidan tider-menyn och klicka på **Sök**.

    ![Time Series insikter Explorer-sökning](media/iot-suite-time-series-insights/time-series-insights-search-time.png)

1. Välj mått värdet i panelen villkor till vänster **temperatur** och delning av värdet **iothub-anslutning-enhet-id**.

    ![Time Series insikter Explorer fråga](media/iot-suite-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **utforska händelser**.

    ![Tid serien insikter Explorer händelser](media/iot-suite-time-series-insights/time-series-insights-explore-events.png)

1. Händelser visas i rutnätet i tabellformat.

    ![Tid serien insikter Explorer tabell](media/iot-suite-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen perspektiv.

    ![Tid serien insikter Explorer perspektiv](media/iot-suite-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka på **Lägg till** att skapa en ny fråga i perspektiv.

    ![Tid serien insikter Explorer lägga till frågan](media/iot-suite-time-series-insights/time-series-insights-new-query.png)

1. Välj en snabb tid **senaste 12 timmarna**, ett mått på **fuktighet** och en delning av **iothub-anslutning-enhet-id**.

    ![Time Series insikter Explorer fråga](media/iot-suite-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektiv vyn om du vill visa instrumentpanelen enheten mått.

    ![Tid serien insikter Explorer instrumentpanelen](media/iot-suite-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Nästa steg

Läs om hur du utforska och frågar efter data i tid serien insikter explorer i [Azure tid serien Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-dashboard.png).
