---
title: Lägga till en IoT hub-händelsekälla till Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en händelsekälla som är ansluten till en IoT-hubb i miljön för Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: ee52cde6feeb69f9140df497a9abee300c93fd71
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692110"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Lägg till en IoT hub-händelsekälla till Time Series Insights-miljön

Den här artikeln beskriver hur du använder Azure-portalen för att lägga till en händelsekälla som läser data från Azure IoT Hub till Azure Time Series Insights-miljön.

> [!NOTE]
> Anvisningarna i den här artikeln gäller både Azure Time Series Insights Allmänt och för förhandsversionen av Time Series Insights-miljöer.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en [Azure Time Series Insights-miljö](time-series-insights-update-create-environment.md).
* Skapa en [IoT-hubben med hjälp av Azure portal](../iot-hub/iot-hub-create-through-portal.md).
* IoT-hubben måste ha aktiva meddelandehändelser som skickas.
* Skapa en dedikerad konsumentgrupp i Time Series Insights-miljö för att använda IoT-hubben. Varje Time Series Insights-händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra konsumenter. Om flera läsare konsumera händelser från samma konsumentgruppen, förmodligen alla läsare ser fel. Mer information finns i [utvecklarhandboken för Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT hub

Program använda konsumentgrupper att hämta data från Azure IoT Hub. Ange en dedikerad konsumentgrupp som bara används av den här Time Series Insights-miljön på ett tillförlitligt sätt läsa data från IoT hub.

Lägga till en ny konsumentgrupp till din IoT hub:

1. Hitta och öppna din IoT-hubb i Azure-portalen.

1. I menyn, under **inställningar**väljer **inbyggda slutpunkter**, och välj sedan den **händelser** slutpunkt.

   ![På sidan inbyggda slutpunkter väljer du knappen händelser][1]

1. Under **konsumentgrupper**, ange ett unikt namn för konsumentgruppen. Använd det här samma namn i din Time Series Insights-miljö när du skapar en ny händelsekälla.

1. Välj **Spara**.

## <a name="add-a-new-event-source"></a>Lägg till en ny händelsekälla

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Alla resurser** i menyn till vänster. Välj Time Series Insights-miljö.

1. Under **miljöns topologi**väljer **händelsekällor**, och välj sedan **Lägg till**.

   ![Välj händelsekällor och välj sedan knappen Lägg till][2]

1. I den **ny händelsekälla** fönstret för **Händelsekällans namn för**, ange ett namn som är unik för den här Time Series Insights-miljön. Ange till exempel **händelseströmmen**.

1. För **källa**väljer **IoT Hub**.

1. Välj ett värde för **importalternativ**:

   * Om du redan har en IoT-hubb i någon av dina prenumerationer, väljer **Använd IoT Hub från tillgängliga prenumerationer**. Det här alternativet är den enklaste metoden.
   * Om IoT-hubben är extern till dina prenumerationer, eller om du vill välja avancerade alternativ väljer **ger IoT Hub-inställningar manuellt**.

   ![Välj alternativ i den nya event källa][3]

1. I följande tabell beskrivs de egenskaper som krävs för den **Använd IoT Hub från tillgängliga prenumerationer** alternativet:

   ![Ny händelse källa fönstret - Egenskaper för att ställa in i Använd IoT Hub från tillgängliga prenumerationer alternativet][4]

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Välj den prenumeration där IoT-hubben har skapats.
   | Namn på IoT Hub | Välj namnet på IoT-hubben.
   | Principnamn för IoT hub | Välj princip för delad åtkomst. Du hittar principen för delad åtkomst på inställningsfliken för IoT hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **tjänsten ansluta** behörigheter.
   | Principnyckel för IoT hub | Nyckeln innehåller redan.
   | IoT hub-konsumentgrupp | Konsumentgruppen som läser händelser från IoT hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till IoT hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |

1. I följande tabell beskrivs de nödvändiga egenskaperna för den **ger IoT Hub-inställningar manuellt**:

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Prenumerations-ID:t | Den prenumeration där IoT-hubben har skapats.
   | Resursgrupp | Resursgruppens namn som IoT-hubben har skapats.
   | Namn på IoT Hub | namnet på din IoT-hubb. När du har skapat din IoT-hubb kan du har angett ett namn för IoT-hubben.
   | Principnamn för IoT hub | Princip för delad åtkomst. Du kan skapa princip för delad åtkomst på inställningsfliken för IoT hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för din händelsekälla *måste* har **tjänsten ansluta** behörigheter.
   | Principnyckel för IoT hub | Den delade åtkomstnyckeln som används för att tillåta åtkomst till Azure Service Bus-namnområdet. Ange den primära eller sekundära nyckeln här.
   | IoT hub-konsumentgrupp | Konsumentgruppen som läser händelser från IoT hub. Vi rekommenderar starkt att du använder en dedikerad konsumentgrupp för din händelsekälla.
   | Händelseserialiseringsformat | JSON är för närvarande endast tillgängligt serialiseringsformatet. Händelsemeddelanden måste vara i formatet eller inga data kan läsas. |
   | Egenskapsnamn för tidsstämpel | För att fastställa det här värdet, måste du förstå meddelandeformat för meddelandedata som skickas till IoT hub. Det här värdet är den **namn** för egenskapen specifik händelse i meddelandedata som du vill använda som tidsstämpel för händelsen. Värdet är skiftlägeskänsligt. Om det lämnas tomt används det **sätta händelsetid** i källan används som tidsstämpel för händelsen. |

1. Lägg till dedikerade Time Series Insights konsument gruppnamnet som du lade till din IoT-hubb.

1. Välj **Skapa**.

   ![Skapa-knappen][5]

1. När du har skapat händelsekällan börjar Time Series Insights automatiskt strömmande data i din miljö.

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda data.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Få tillgång till den i den [Time Series Insights explorer](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png