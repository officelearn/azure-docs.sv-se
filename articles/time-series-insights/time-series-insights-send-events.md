---
title: Hur man skickar händelser till en Azure Time Series Insights-miljö | Microsoft Docs
description: Den här självstudien beskrivs hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser som ska visas i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 64fb9f72cfd7edef18d56f15cbcce726dd33b50d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847277"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av Event Hub

Den här artikeln förklarar hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser. Om du har en befintlig händelsehubb med händelser i JSON-format, hoppa över den här självstudien och visa din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Om du vill skapa en Event Hub följer du instruktionerna från Event Hub [dokumentation](https://docs.microsoft.com/azure/event-hubs/).
1. Söka efter Event Hub i sökfältet. Klicka på **Händelsehubbar** i den returnerade listan.
1. Välj din Event Hub genom att klicka på dess namn.
1. Under **entiteter** i fönstret i mitten konfiguration klickar du på **Händelsehubbar** igen.
1. Välj namnet på Händelsehubben för att konfigurera den.

    ![Consumer-group][1]

1. Under **entiteter**väljer **konsumentgrupper**.
1. Se till att skapa en konsumentgrupp som enbart används av din TSI-händelsekälla.

> [!IMPORTANT]
> Kontrollera att den här konsumentgruppen inte används av någon annan tjänst (till exempel Stream Analytics-jobb eller en annan TSI-miljö). Om konsumentgruppen används av andra påverkas tjänster, Läsåtgärd negativt för den här miljön och andra tjänster. Om du använder `$Default` som konsumentgrupp, det kan leda till eventuell återanvändning av andra läsare.

1. Under den **inställningar** väljer **åtkomstprinciper för filresursen**.
1. Skapa på event hub **MySendPolicy** som används för att skicka händelser i den C# exemplet.

    ![delade = åtkomst-ett][2]

    ![delad åtkomst-två][3]

## <a name="add-time-series-insights-instances"></a>Lägga till Time Series Insights-instanser

TSI-uppdateringen använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data är ansluten på frågan med en **Time Series-ID**. Den **Time Series-ID** för exemplet windmills projektet är `Id`. Mer information om Time Series-instanser och **Time Series-ID: N**bör du läsa [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Skapa händelsekälla för Time Series Insights

1. Om du inte har skapat en händelsekälla följer du dessa instruktioner för att skapa en händelsekälla.
1. Ange den `timeSeriesId` – avser [Time Series modeller](./time-series-insights-update-tsm.md) mer information om **Time Series-ID: N**.

### <a name="push-events-sample-windmills"></a>Push-händelser (exempel windmills)

1. Sök efter händelsehubb i sökfältet. Klicka på **Händelsehubbar** i den returnerade listan.
1. Välj din event hub genom att klicka på dess namn.
1. Gå till **delade åtkomstprinciper** och sedan **RootManageSharedAccessKey**. Kopiera den **anslutning förekomster av textsträngen primär nyckel**

   ![connection-string][4]

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. Den här lösningen körs windmill simulerade enheter.
1. Klistra in anslutningssträngen som du kopierade från steg tre i den **Händelsehubbens anslutningssträng**
1. Klicka på **Klicka på Start**
1. Gå tillbaka till din Event Hub. Du bör se de nya händelser som tas emot av hubben:

   ![telemetri][5]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa din miljö i Time Series Insights Explorer](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/consumer-group.png
[2]: media/send-events/shared-access-policy.png
[3]: media/send-events/shared-access-policy-2.png
[4]: media/send-events/sample-code-connection-string.png
[5]: media/send-events/telemetry.png