---
title: Förhandsgranskningsfunktioner i Azure Stream Analytics
description: I den här artikeln visas de Azure Stream Analytics-funktioner som för närvarande förhandsgranskas.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969620"
---
# <a name="azure-stream-analytics-preview-features"></a>Förhandsgranskningsfunktioner i Azure Stream Analytics

Den här artikeln sammanfattar alla funktioner som för närvarande förhandsgranskas för Azure Stream Analytics. Det rekommenderas inte att använda förhandsgranskningsfunktioner i en produktionsmiljö.

## <a name="public-previews"></a>Allmänt tillgängliga förhandsversioner

Följande funktioner är i offentlig förhandsversion. Du kan dra nytta av dessa funktioner idag, men använd dem inte i din produktionsmiljö.

### <a name="online-scaling"></a>Skalning online

Med onlineskalning behöver du inte stoppa jobbet om du behöver ändra SU-allokeringen. Du kan öka eller minska SU-kapaciteten för ett jobb som körs utan att behöva stoppa det. Detta bygger på kundens löfte om långvariga verksamhetskritiska pipelines som Stream Analytics erbjuder idag. Mer information finns i [Konfigurera Azure Stream Analytics streamingenheter](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C# anpassade de-serialiserare
Utvecklare kan utnyttja kraften i Azure Stream Analytics för att bearbeta data i Protobuf, XML eller något anpassat format. Du kan implementera [anpassade de-serialiserare](custom-deserializer-examples.md) i C#, som sedan kan användas för att av serialisera händelser som tas emot av Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Utökningsbarhet med C# anpassad kod

Utvecklare som skapar Stream Analytics-moduler i molnet eller på IoT Edge kan skriva eller återanvända anpassade C#-funktioner och anropa dem direkt i frågan via [användardefinierade funktioner](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Felsökningsfrågesteg i Visual Studio

Du kan enkelt förhandsgranska den mellanliggande raduppsättningen i ett datadiagram när du gör lokala tester i Azure Stream Analytics-verktyg för Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokal testning med livedata i Visual Studio-kod

Du kan testa dina frågor mot livedata på din lokala dator innan du skickar jobbet till Azure. Varje testiteration tar mindre än två till tre sekunder i genomsnitt, vilket resulterar i en mycket effektiv utvecklingsprocess.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio-kod för Azure Stream Analytics

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se vår [VS-kod komma igång handledning](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics-jobb med machine learning-funktioner (MACHINE Learning). Om du vill veta mer om hur du kan använda ML-funktioner i ditt Stream Analytics-jobb besöker du [Skala ditt Stream Analytics-jobb med Azure Machine Learning-funktioner](stream-analytics-scale-with-machine-learning-functions.md). Kolla in ett verkligt scenario med [Utföra sentimentanalys med hjälp av Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Testning av livedata i Visual Studio

Visual Studio-verktyg för Azure Stream Analytics förbättrar den lokala testfunktionen som gör att du kan testa frågor mot livehändelseströmmar från molnkällor som Event Hub eller IoT hub. Lär dig hur du [testar livedata lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET användardefinierade funktioner på IoT Edge

Med .NET-standardanvändardefinierade funktioner kan du köra .NET Standard-kod som en del av din strömningspipeline. Du kan skapa enkla C#-klasser eller importera hela projekt och bibliotek. Fullständig redigerings- och felsökningsupplevelse stöds i Visual Studio. Mer information finns i [Utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andra förhandsgranskningar

Följande funktioner är också tillgängliga i förhandsversion på begäran.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Prestandabedömning i realtid med anpassade ML-modeller som hanteras av Azure Machine Learning

Azure Stream Analytics stöder högpresterande realtidsbedömning genom att utnyttja anpassade förutbildade Machine Learning-modeller som hanteras av Azure Machine Learning och finns i Azure Kubernetes Service (AKS) eller Azure Container Instances (ACI), med hjälp av ett arbetsflöde som inte kräver att du skriver kod. [Registrera dig](https://aka.ms/asapreview1) för förhandsgranskning

### <a name="support-for-azure-stack"></a>Stöd för Azure Stack
Den här funktionen aktiveras på Azure IoT Edge-körningen, utnyttjar anpassade Azure Stack-funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel eventhubbar, IoT Hub, Blob Storage). Med den här nya integreringen kan du skapa hybridarkitekturer som kan analysera dina data nära där de genereras, sänka svarstiden och maximera insikter.
Med den här funktionen kan du skapa hybridarkitekturer som kan analysera dina data nära där de genereras, sänka svarstiden och maximera insikter. Du måste [registrera dig](https://aka.ms/asapreview1) för den här förhandsversionen.
