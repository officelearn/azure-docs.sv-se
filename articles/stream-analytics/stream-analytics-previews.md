---
title: Azure Stream Analytics-förhandsversionsfunktioner
description: Den här artikeln visar de Azure Stream Analytics-funktioner som finns för närvarande i förhandsversion.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969620"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics-förhandsversionsfunktioner

Den här artikeln beskriver alla funktioner för närvarande i förhandsversion för Azure Stream Analytics. Använda förhandsversionsfunktioner i en produktionsmiljö rekommenderas inte.

## <a name="public-previews"></a>Offentliga förhandsversioner

Följande funktioner finns i offentlig förhandsversion. Du kan dra nytta av funktionerna i dag, men använda inte dem i din produktionsmiljö.

### <a name="online-scaling"></a>Online-skalning

Med online-skalning behöver du inte avbryta jobbet om du behöver ändra SU-fördelningen. Du kan öka eller minska SU-kapaciteten för ett pågående jobb utan att behöva stoppa det. Detta bygger på kund löftet av långvariga verksamhets kritiska pipelines som Stream Analytics erbjuder idag. Mer information finns i [konfigurera Azure Stream Analytics strömnings enheter](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#anpassad deserialiserare
Utvecklare kan dra nytta av kraften i Azure Stream Analytics att bearbeta data i protobuf, XML eller något anpassat format. Du kan implementera [anpassade deserialiserare](custom-deserializer-examples.md) i C#, som sedan kan användas för deserialisering av händelser som tagits emot av Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Utökning C# med anpassad kod

Utvecklare som skapar Stream Analytics moduler i molnet eller på IoT Edge kan skriva eller återanvända anpassade C# funktioner och anropa dem direkt i frågan via [användardefinierade funktioner](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Felsöka fråge steg i Visual Studio

Du kan enkelt förhandsgranska den mellanliggande rad uppsättningen i ett data diagram när du gör en lokal testning i Azure Stream Analytics verktyg för Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokal testning med real tids data i Visual Studio Code

Du kan testa dina frågor mot Real tids data på den lokala datorn innan du skickar jobbet till Azure. Varje test upprepning tar mindre än två till tre sekunder i genomsnitt, vilket resulterar i en mycket effektiv utvecklings process.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code för Azure Stream Analytics

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se kursen för att [komma igång med vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics-jobb med Machine Learning (ML)-funktioner. Om du vill veta mer om hur du kan använda ML-funktioner i Stream Analytics-jobbet kan du gå [till skala ditt Stream Analytics-jobb med Azure Machine Learning funktioner](stream-analytics-scale-with-machine-learning-functions.md). Ta en titt på ett verkligt scenario med att [utföra sentiment-analys med hjälp av Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio tools för Azure Stream Analytics förbättra lokal testning funktionen som gör att du kan testa frågor mot live händelseströmmar från molnkällor, till exempel Händelsehubb eller IoT hub. Lär dig hur du [testar Live data lokalt med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-funktioner på IoT Edge

Du kan köra .NET Standard kod med .NET standard användardefinierade funktioner, som en del av din pipeline för dataströmning. Du kan skapa enkla C# klasserna eller Importera projekt och bibliotek. Fullständig skapar och felsöker upplevelsen stöds i Visual Studio. Mer information finns [i utveckla .net standard-användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andra för hands versionerna

Följande funktioner är också tillgängliga i för hands version på begäran.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Resultat av hög prestanda i real tid med anpassade ML-modeller som hanteras av Azure Machine Learning

Azure Stream Analytics stöder real tids resultat i real tid genom att använda anpassade Machine Learning modeller som hanteras av Azure Machine Learning och som finns i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI), med hjälp av ett arbets flöde Det kräver inte att du skriver kod. [Registrera dig](https://aka.ms/asapreview1) för för hands version

### <a name="support-for-azure-stack"></a>Stöd för Azure Stack
Den här funktionen är aktive rad på Azure IoT Edge Runtime och utnyttjar anpassade Azure Stack funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel Event Hubs, IoT Hub, Blob Storage). Med den här nya integrationen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna.
Med den här funktionen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna. Du måste [Registrera](https://aka.ms/asapreview1) dig för den här för hands versionen.
