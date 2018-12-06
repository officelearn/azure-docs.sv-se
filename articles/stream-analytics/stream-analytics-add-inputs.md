---
title: Förstå indata för Azure Stream Analytics
description: Den här artikeln beskriver begreppet indata i Azure Stream Analytics-jobb, jämföra strömmande indata till referensindata.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 0475318ce983b98951fb9cd5d24a5bc4452b1f1e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970118"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Förstå indata för Azure Stream Analytics

Azure Stream Analytics-jobb att ansluta till en eller flera inmatningar av referensdata. Varje indata definierar en anslutning till en befintlig datakälla. Stream Analytics tar emot inkommande data från flera olika typer av händelsekällor inklusive Event Hubs, IoT Hub och Blob storage. Namn i den strömmande SQL-fråga som du skriver för varje jobb refererar till indata. I frågan, kan du ansluta till flera inmatningar för att blanda data eller jämföra strömmande data med en sökning till referensdata och skicka resultaten till utdata. 

Stream Analytics har förstaklassintegrering från tre typer av resurser som indata:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Dessa indata resurser kan finnas i samma Azure-prenumeration som Stream Analytics-jobb, eller från en annan prenumeration.

Du kan använda den [Azure-portalen](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), och [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)att skapa, redigera och testa indata för Stream Analytics-jobbet.

## <a name="stream-and-reference-inputs"></a>Referens och Stream-indata
Den har används av Stream Analytics-jobb och behandlas i realtid allteftersom data pushas till en datakälla. Indata är indelade i två typer: strömma indata och referera till datainmatningar.

### <a name="data-stream-input"></a>Dataströmmen indata
En dataström är en obundna sekvens av händelser över tid. Stream Analytics-jobb måste innehålla minst en inkommande dataström. Event Hubs, IoT Hub och Blob-lagring stöds som indata datakällor för stream. Event Hubs används för att samla in händelseströmmar från flera enheter och tjänster. Dessa strömmar kan innehålla aktivitet för sociala medier, lagerartiklar trade information eller data från sensorer. IoT-hubbar är optimerade för att samla in data från anslutna enheter i Sakernas Internet (IoT)-scenarier.  BLOB storage kan användas som en indatakälla för att föra in stora mängder data som en dataström som loggfiler.  

Läs mer om inmatningar av strömmande data [Stream data som indata till Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referensindata
Stream Analytics har även stöd för indata som kallas *referensdata*. Referensdata är antingen helt statisk eller ändras långsamt. Det är vanligt att utföra korrelation och sökningar. Du kan till exempel att ansluta till data i strömindata data till data i referensdata, mycket som du utför en SQL-koppling för att leta upp statiska värden. Azure Blob storage är för närvarande endast stöds Indatakällan för referensdata. Källa för referensdatablobar har en gräns på upp till 300 MB i storlek, beroende på frågekomplexiteten och tilldelas enheter för strömning.

Läs mer om inmatningar av referensdata, [använda referensdata för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](stream-analytics-quick-create-portal.md)
