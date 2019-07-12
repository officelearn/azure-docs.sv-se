---
title: Azure Stream Analytics-förhandsversionsfunktioner
description: Den här artikeln visar de Azure Stream Analytics-funktioner som finns för närvarande i förhandsversion.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 587304968cdf3a3763e47b9f8b614fe67aebf534
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798041"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics-förhandsversionsfunktioner

Den här artikeln beskriver alla funktioner för närvarande i förhandsversion för Azure Stream Analytics. Använda förhandsversionsfunktioner i en produktionsmiljö rekommenderas inte.

## <a name="public-previews"></a>Offentliga förhandsversioner

Följande funktioner finns i offentlig förhandsversion. Du kan dra nytta av funktionerna i dag, men använda inte dem i din produktionsmiljö.

### <a name="one-click-integration-with-event-hubs"></a>Ett klick integrering med Event Hubs 
Med den här integreringen kommer du nu att kunna visualisera inkommande data och börja skriva en Stream Analytics-fråga med ett enda klick från Event Hub-portalen. När din fråga är klar, kommer du att kunna productize med några klick och börja få insikter i realtid. Detta minskar avsevärt tiden och kostnaderna för att utveckla lösningar för realtidsanalys. Dokumentation finns [här](https://docs.microsoft.com/azure/event-hubs/process-data-azure-stream-analytics).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code för Azure Stream Analytics

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se våra [VS Code komma igång-självstudiekurs](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Stream Analytics introducerar nya machine learning-modeller med stöd för *topp* och *dalar* identifiering förutom dubbelriktad, långsam positiva och långsamma negativa trender identifiering. För mer information, besök [avvikelseidentifiering i Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics-jobb med Machine Learning (ML)-funktioner. Läs mer om hur du kan använda ML-funktioner i ditt Stream Analytics-jobb [skala ditt Stream Analytics-jobb med Azure Machine Learning-funktioner](stream-analytics-scale-with-machine-learning-functions.md). Kolla in ett verkligt scenario med [utföra attitydanalyser genom att använda Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>JavaScript användardefinierad samling

Azure Stream Analytics stöder användardefinierade aggregeringar (UDA) skriven i JavaScript, vilket gör att du kan implementera komplex tillståndskänsliga affärslogik. Lär dig hur du använder UDA: er från den [Azure Stream Analytics JavaScript-användardefinierade aggregeringar](stream-analytics-javascript-user-defined-aggregates.md) dokumentation. 

### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio tools för Azure Stream Analytics förbättra lokal testning funktionen som gör att du kan testa frågor mot live händelseströmmar från molnkällor, till exempel Händelsehubb eller IoT hub. Lär dig hur du [testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-funktioner på IoT Edge

Du kan köra .NET Standard kod med .NET standard användardefinierade funktioner, som en del av din pipeline för dataströmning. Du kan skapa enkla C# klasserna eller Importera projekt och bibliotek. Fullständig skapar och felsöker upplevelsen stöds i Visual Studio. För mer information, besök [utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andra förhandsversioner

Följande funktioner är också tillgängliga i förhandsversionen av begäran.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud"></a>C#anpassade deserialiserare för Azure Stream Analytics på IoT Edge och molnet

Utvecklare kan implementera anpassade deserializers i C# rekonstrukci händelser som tagits emot av Azure Stream Analytics. Exempel på format som kan deserialiseras är Parquet, Protobuf, XML eller alla binära dataformat. Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack"></a>Stöd för Azure Stack
Den här funktionen aktiverad på Azure IoT Edge-körningen utnyttjar anpassade Azure Stack-funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel Händelsehubbar, IoT Hub, Blob Storage). Den här nya integrationen kan du skapa hybridarkitekturer som kan analysera dina data nära var den genereras, vilket minskar svarstiden och maximera insikter.
Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

