---
title: Så här lägger du till en händelse källa för IoT Hub – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du lägger till en händelse källa för IoT Hub i din Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9aace01e2f3d514ee5f4b406f4067e104151e5d6
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863333"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Lägga till en händelse källa för IoT Hub i Time Series Insightss miljön

Den här artikeln beskriver hur du använder Azure Portal för att lägga till en händelse källa som läser data från Azure IoT Hub till din Azure Time Series Insights miljö.

> [!NOTE]
> Anvisningarna i den här artikeln gäller både för Azure Time Series Insights GA och för att Time Series Insights för hands versions miljöer.

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Time Series Insightss miljö](time-series-insights-update-create-environment.md).
* Skapa en [IoT-hubb med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* IoT-hubben måste ha aktiva meddelande händelser som skickas i.
* Skapa en dedikerad konsument grupp i IoT-hubben för Time Series Insightss miljön att använda. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra konsumenter. Om flera läsare förbrukar händelser från samma konsument grupp, kommer alla läsare att kunna uppvisa problem. Mer information finns i [Azure IoT Hub Developer Guide](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsument grupp i IoT Hub

Program använder konsument grupper för att hämta data från Azure IoT Hub. Om du vill läsa data på ett tillförlitligt sätt från din IoT Hub kan du ange en dedikerad konsument grupp som endast används av den här Time Series Insightss miljön.

Så här lägger du till en ny konsument grupp i IoT Hub:

1. Leta upp och öppna din IoT Hub i [Azure Portal](https://portal.azure.com).

1. Under **Inställningar**väljer du **inbyggda slut punkter**och väljer sedan **händelse** slut punkten.

   [![på sidan för build-in-slutpunkter väljer du knappen händelser](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Under **konsument grupper**anger du ett unikt namn på konsument gruppen. Använd samma namn i Time Series Insightss miljön när du skapar en ny händelse källa.

1. Välj **Spara**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla resurser** i menyn till vänster. Välj Time Series Insights-miljö.

1. Under **miljöns topologi**väljer **händelsekällor**, och välj sedan **Lägg till**.

   [![Välj händelse källor och välj sedan knappen Lägg till](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. I fönstret **ny händelse källa** , för **händelse källans namn**, anger du ett namn som är unikt för den här Time Series Insightss miljön. Ange till exempel **händelse-Stream**.

1. För **källa**väljer du **IoT Hub**.

1. Välj ett värde för **alternativet importera**:

   * Om du redan har en IoT-hubb i en av dina prenumerationer väljer du **använd IoT Hub från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.
   
     [![välja alternativ i fönstret ny händelse källa](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * I följande tabell beskrivs de egenskaper som krävs för alternativet **använd IoT Hub från tillgängliga prenumerationer** :

       [![fönstret ny händelse källa – egenskaper som anges i alternativet Använd IoT Hub från tillgängliga prenumerationer](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumeration | Prenumerationen på den önskade IoT-hubben tillhör. |
       | Namn på IoT Hub | Namnet på den valda IoT-hubben. |
       | Princip namn för IoT Hub | Välj princip för delad åtkomst. Du kan hitta principen för delad åtkomst på fliken Inställningar i IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha behörighet för **tjänst anslutning** . |
       | Princip nyckel för IoT Hub | Nyckeln är förifylld. |

    * Om IoT Hub är externt för dina prenumerationer, eller om du vill välja avancerade alternativ, väljer du **ange IoT Hub inställningar manuellt**.

      I följande tabell beskrivs de egenskaper som krävs för att **ange IoT Hub inställningar manuellt**:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Prenumerations-ID | Prenumerationen på den önskade IoT-hubben tillhör. |
       | Resursgrupp | Resurs grupps namnet där IoT Hub skapades. |
       | Namn på IoT Hub | Namnet på din IoT Hub. När du har skapat din IoT-hubb angav du ett namn för IoT Hub. |
       | Princip namn för IoT Hub | Princip för delad åtkomst. Du kan skapa principen för delad åtkomst på fliken Inställningar i IoT Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. Den delade åtkomst principen för din händelse källa *måste* ha behörighet för **tjänst anslutning** . |
       | Princip nyckel för IoT Hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Azure Service Bus-namnrymden. Ange den primära eller sekundära nyckeln här. |

    * Båda alternativen delar följande konfigurations alternativ:

       | Egenskap | Beskrivning |
       | --- | --- |
       | Konsument grupp för IoT Hub | Konsument gruppen som läser händelser från IoT Hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla. |
       | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelse meddelandena måste vara i detta format eller så kan inga data läsas. |
       | Egenskapsnamnet för tidsstämpeln | För att fastställa det här värdet måste du förstå meddelande formatet för de meddelande data som skickas till IoT Hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |


1. Lägg till det dedikerade Time Series Insights konsument grupp namn som du har lagt till i IoT-hubben.

1. Välj **Skapa**.

1. När du har skapat händelse källan börjar Time Series Insights automatiskt att strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).
