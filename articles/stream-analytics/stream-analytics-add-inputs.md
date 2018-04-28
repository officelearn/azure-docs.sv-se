---
title: Förstå indata för Azure Stream Analytics
description: Den här artikeln beskriver begreppet indata i ett Azure Stream Analytics-jobb att jämföra strömmande indata till referensindata.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Förstå indata för Azure Stream Analytics

Azure Stream Analytics-jobb att ansluta till en eller flera indata. Varje indata definierar en anslutning till en befintlig datakälla. Stream Analytics accepterar inkommande data från flera olika typer av händelsekällor inklusive Händelsehubbar, IoT-hubb och Blob storage. Indata som refereras av namnet i strömmande SQL-frågan som du skriver för varje jobb. I frågan, kan du ansluta flera indata för att blanda data eller jämföra strömmande data med ett uppslag till referensdata och skickar resultaten till utdata. 

Stream Analytics har förstklassigt integration från tre typer av resurser som indata:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indata resurser kan finnas kvar i samma Azure-prenumeration som Stream Analytics-jobb, eller från en annan prenumeration.

Du kan använda den [Azure-portalen](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), och [Visual Studio](stream-analytics-tools-for-visual-studio.md)att skapa, redigera och testa Stream Analytics-jobbet indata.

## <a name="stream-and-reference-inputs"></a>Ström och referensdata indata
När data skickas till en datakälla har det används av Stream Analytics-jobbet och bearbetas i realtid. Indata är indelade i två typer: data strömma indata och referera till indata.

### <a name="data-stream-input"></a>Dataströmmen indata
En dataström är en unbounded sekvens av händelser över tid. Stream Analytics-jobb måste innehålla minst en inkommande dataström. Händelsehubbar, IoT-hubb och Blob storage stöds som inkommande datakällor för dataströmmen. Händelsehubbar används för att samla in händelseströmmar från flera enheter och tjänster. Dessa strömmar kan innehålla sociala medier aktivitetsfeeds, lager handel information eller data från sensorer. IoT-hubbar är optimerade för att samla in data från anslutna enheter i scenarier i Sakernas Internet (IoT).  BLOB-lagring kan användas som en Indatakällan för att föra in stora mängder data som en dataström som loggfiler.  

Läs mer om strömning indata [strömma data som indata till Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referensindata
Stream Analytics stöder också indata som kallas *referensdata*. Referensdata är antingen helt statiska eller långsamt ändringar. Det är vanligt att utföra korrelation och sökningar. Exempelvis kan du ansluta till data i inkommande dataström till data i referensdata, ungefär som du vill utföra en SQL-koppling för att leta upp statiska värden. Azure Blob storage är för närvarande endast stöds Indatakällan för referensdata. Referens för datakällan blobbar är begränsade till 100 MB i storlek.

Mer information om referens indata finns [använda referensdata för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)

Den här artikeln är ett steg i den [Stream Analytics-Utbildningsväg](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](stream-analytics-quick-create-portal.md)