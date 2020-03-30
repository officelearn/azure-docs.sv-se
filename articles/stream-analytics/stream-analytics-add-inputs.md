---
title: Förstå indata för Azure Stream Analytics
description: I den här artikeln beskrivs begreppet indata i ett Azure Stream Analytics-jobb och du jämför direktuppspelningsindata med referensdataindata.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426438"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Förstå indata för Azure Stream Analytics

Azure Stream Analytics-jobb ansluter till en eller flera dataindata. Varje indata definierar en anslutning till en befintlig datakälla. Stream Analytics accepterar data som inkommandes från flera typer av händelsekällor, inklusive eventhubbar, IoT Hub och Blob-lagring. Ingångarna refereras efter namn i den strömmande SQL-fråga som du skriver för varje jobb. I frågan kan du koppla flera indata för att blanda data eller jämföra strömmande data med en sökning till referensdata och skicka resultaten till utdata. 

Stream Analytics har förstklassig integrering med tre typer av resurser som indata:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-lagring](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indataresurser kan visas i samma Azure-prenumeration som ditt Stream Analytics-jobb eller från en annan prenumeration.

Du kan använda [Azure-portalen,](stream-analytics-quick-create-portal.md#configure-job-input) [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)och [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) för att skapa, redigera och testa Stream Analytics-jobbindata.

## <a name="stream-and-reference-inputs"></a>Strömma och referera till ingångar
När data överförs till en datakälla förbrukas de av Stream Analytics-jobbet och bearbetas i realtid. Indata är uppdelade i två typer: inkommande dataströmmar och inkommande referensdata.

### <a name="data-stream-input"></a>Indataström
En dataström är en obegränsad händelsesekvens över tid. Stream Analytics-jobb måste innehålla minst en inkommande dataström. Event Hubs, IoT Hub och Blob Storage stöds som inkommande dataströmmar. Event Hubs används för att samla in händelseströmmar från flera enheter och tjänster. Dessa strömmar kan omfatta aktivitetsflöden för sociala medier, lagerhandelsinformation eller data från sensorer. IoT Hubs är optimerade för att samla in data från anslutna enheter i IoT-scenarier (Internet of Things).  Blob-lagring kan användas som en indatakälla för inmatning av massdata som en ström, till exempel loggfiler.  

Mer information om direktuppspelning av dataindata finns [i Strömma data som indata i Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Indata för referensdata
Stream Analytics stöder även indata som kallas *referensdata*. Referensdata är antingen helt statiska eller ändras långsamt. Det används vanligtvis för att utföra korrelation och sökning. Du kan till exempel koppla data i dataströmmen till data i referensdata, ungefär som du utför en SQL-koppling för att slå upp statiska värden. Azure Blob storage och Azure SQL Database stöds för närvarande som indatakällor för referensdata. Referensdatakällblobar har en gräns på upp till 300 MB i storlek, beroende på frågekomplexiteten och allokerade strömningsenheter (se avsnittet [Storleksbegränsning](stream-analytics-use-reference-data.md#size-limitation) i referensdatadokumentationen för mer information).

Mer information om referensdataindata finns i [Använda referensdata för uppslag i Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
