---
title: Så här lägger du till en IoT-hubbhändelsekälla – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du lägger till en IoT-hubbhändelsekälla i din Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: 3ea73e2ca20faea30294bc5d5e1788415095c39f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905363"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Lägga till en IoT-hubbhändelsekälla i time series insights-miljön

I den här artikeln beskrivs hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från Azure IoT Hub till din Azure Time Series Insights-miljö.

> [!NOTE]
> Instruktionerna i den här artikeln gäller både Azure Time Series Insights GA och time series insights Preview-miljöer.

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Time Series Insights-miljö](time-series-insights-update-create-environment.md).
* Skapa en [IoT-hubb med hjälp av Azure-portalen](../iot-hub/iot-hub-create-through-portal.md).
* IoT-hubben måste ha aktiva meddelandehändelser som skickas in.
* Skapa en dedikerad konsumentgrupp i IoT-hubben för time series insights-miljön att använda från. Varje Time Series Insights-händelsekälla måste ha en egen dedikerad konsumentgrupp som inte delas med någon annan konsument. Om flera läsare använder händelser från samma konsumentgrupp, är det troligt att alla läsare uppvisar fel. Mer information finns i [utvecklarhandboken för Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsumentgrupp i din IoT-hubb

Program använder konsumentgrupper för att hämta data från Azure IoT Hub. Om du vill läsa data från din IoT-hubb på ett tillförlitligt sätt kan du tillhandahålla en dedikerad konsumentgrupp som endast används av den här Time Series Insights-miljön.

Så här lägger du till en ny konsumentgrupp i din IoT-hubb:

1. Leta reda på och öppna din IoT-hubb i [Azure-portalen.](https://portal.azure.com)

1. Under **Inställningar**väljer du **Inbyggda slutpunkter**och väljer sedan slutpunkten **Händelser.**

   [![På sidan Slutpunkter i build väljer du knappen Händelser](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Under **Konsumentgrupper**anger du ett unikt namn för konsumentgruppen. Använd samma namn i time series insights-miljön när du skapar en ny händelsekälla.

1. Välj **Spara**.

## <a name="add-a-new-event-source"></a>Lägga till en ny händelsekälla

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla resurser** i menyn till vänster. Välj Time Series Insights-miljö.

1. Under **Inställningar**väljer du **Händelsekällor**och väljer sedan **Lägg till**.

   [![Välj händelsekällor och välj sedan knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Ange ett namn som är unikt för den här time series insights-miljön för **händelsekällan**i fönstret **Nytt händelsekälla.** Ange till exempel **händelseström**.

1. För **Källa**väljer du **IoT Hub**.

1. Välj ett värde för **importalternativ:**

   * Om du redan har en IoT-hubb i en av dina prenumerationer väljer du **Använd IoT Hub från tillgängliga prenumerationer**. Det här alternativet är det enklaste tillvägagångssättet.
   
     [![Välj alternativ i fönstret Ny händelsekälla](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * I följande tabell beskrivs de egenskaper som krävs för alternativet **Använd IoT Hub från tillgängliga prenumerationer:**

       [![Fönstret Ny händelsekälla – Egenskaper som ska anges i alternativet Använd IoT Hub från tillgängliga prenumerationer](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen som önskas iot-hubben tillhör. |
       | IoT-hubbnamn | Namnet på den valda iot-hubben. |
       | Namn på IoT-hubbprincip | Välj principen för delad åtkomst. Du hittar principen för delad åtkomst på fliken Inställningar för IoT-hubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. Principen för delad åtkomst för händelsekällan *måste* ha behörighet att **ansluta tjänsten.** |
       | Principnyckel för IoT-hubb | Nyckeln är förifylld. |

    * Om IoT-hubben är externt för dina prenumerationer, eller om du vill välja avancerade alternativ, väljer **du Ange IoT Hub-inställningar manuellt**.

      I följande tabell beskrivs de egenskaper som krävs för inställningarna för **Provide IoT Hub manuellt:**

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID:t | Prenumerationen som önskas iot-hubben tillhör. |
       | Resursgrupp | Resursgruppsnamnet där IoT-hubben skapades. |
       | IoT-hubbnamn | Namnet på din IoT-hubb. När du skapade IoT-hubben angav du ett namn för IoT-hubben. |
       | Namn på IoT-hubbprincip | Principen för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken Inställningar för IoT-hubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. Principen för delad åtkomst för händelsekällan *måste* ha behörighet att **ansluta tjänsten.** |
       | Principnyckel för IoT-hubb | Den delade åtkomstnyckeln som används för att autentisera åtkomst till namnområdet Azure Service Bus. Ange den primära eller sekundära nyckeln här. |

    * Båda alternativen delar följande konfigurationsalternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsumentgrupp för IoT-hubb | Konsumentgruppen som läser händelser från IoT-hubben. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla. |
       | Händelseserialiseringsformat | För närvarande är JSON det enda tillgängliga serialiseringsformatet. Händelsemeddelandena måste vara i det här formatet eller så går det inte att läsa några data. |
       | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet måste du förstå meddelandeformatet för meddelandedata som skickas till IoT-hubben. Det här värdet är **namnet** på den specifika händelseegenskapen i de meddelandedata som du vill använda som händelsetidsstämpel. Värdet är skiftlägeskänsligt. Om händelsen lämnas tom används **händelsens kötid** i händelsekällan som tidsstämpel för händelsen. |


1. Lägg till det dedikerade konsumentgruppsnamnet Time Series Insights som du har lagt till i din IoT-hubb.

1. Välj **Skapa**.

1. När du har skapat händelsekällan börjar Time Series Insights automatiskt strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* Definiera principer för [dataåtkomst](time-series-insights-data-access.md) för att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till din miljö i [Utforskaren för Time Series Insights](https://insights.timeseries.azure.com).
