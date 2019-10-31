---
title: Azure Stream Analytics för hands versions funktioner
description: Den här artikeln innehåller Azure Stream Analytics funktioner som för närvarande finns i för hands version.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: df3e8c1cd91c676c64d15c46c5acdc3d5bcfaa8e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161373"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics för hands versions funktioner

I den här artikeln sammanfattas alla funktioner som för hands version för Azure Stream Analytics. Att använda för hands versions funktionerna i en produktions miljö rekommenderas inte.

## <a name="public-previews"></a>Offentliga för hands versionerna

Följande funktioner finns i offentlig för hands version. Du kan dra nytta av dessa funktioner idag, men Använd dem inte i produktions miljön.

### <a name="online-scaling"></a>Online-skalning

Med online-skalning behöver du inte avbryta jobbet om du behöver ändra SU-fördelningen. Du kan öka eller minska SU-kapaciteten för ett pågående jobb utan att behöva stoppa det. Detta bygger på kund löftet av långvariga verksamhets kritiska pipelines som Stream Analytics erbjuder idag. Mer information finns i [konfigurera Azure Stream Analytics strömnings enheter](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#anpassad deserialiserare
Utvecklare kan dra nytta av kraften i Azure Stream Analytics att bearbeta data i protobuf, XML eller något anpassat format. Du kan implementera [anpassade deserialiserare](custom-deserializer-examples.md) i C#, som sedan kan användas för deserialisering av händelser som tagits emot av Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Utökning C# med anpassad kod

Utvecklare som skapar Stream Analytics moduler i molnet eller på IoT Edge kan skriva eller återanvända anpassade C# funktioner och anropa dem direkt i frågan via [användardefinierade funktioner](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Hanterad identitets autentisering med Power BI

Azure Stream Analytics erbjuder fullständigt stöd för hanterad identitets baserad autentisering med Power BI för en dynamisk instrument panels upplevelse.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Stream Analytics Machine Learning-modeller har stöd för *insamling* och *DIP* -identifiering förutom dubbelriktad, långsam positiv och låg negativ tendens identifiering. Mer information finns [i avvikelse identifiering i Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Felsöka fråge steg i Visual Studio

Du kan enkelt förhandsgranska den mellanliggande rad uppsättningen i ett data diagram när du gör en lokal testning i Azure Stream Analytics verktyg för Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Lokal testning med real tids data i Visual Studio Code

Du kan testa dina frågor mot Real tids data på den lokala datorn innan du skickar jobbet till Azure. Varje test upprepning tar mindre än två till tre sekunder i genomsnitt, vilket resulterar i en mycket effektiv utvecklings process.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code för Azure Stream Analytics

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se kursen för att [komma igång med vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Stream Analytics introducerar nya maskin inlärnings modeller med stöd för *insamling* och *DIP* -identifiering förutom dubbelriktad, långsam positiv och låg negativ tendens identifiering. Mer information finns [i avvikelse identifiering i Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).


### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics jobb med Machine Learning (ML)-funktioner. Om du vill veta mer om hur du kan använda ML-funktioner i Stream Analytics-jobbet kan du gå [till skala ditt Stream Analytics-jobb med Azure Machine Learning funktioner](stream-analytics-scale-with-machine-learning-functions.md). Ta en titt på ett verkligt scenario med att [utföra sentiment-analys med hjälp av Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio-verktyg för Azure Stream Analytics förbättra den lokala testnings funktionen som gör att du kan testa dina frågor mot Live händelse strömmar från moln källor som Händelsehubben eller IoT Hub. Lär dig hur du [testar Live data lokalt med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-användardefinierade funktioner på IoT Edge

Med .NET standard-användardefinierade funktioner kan du köra .NET-standardkod som en del av den strömmande pipelinen. Du kan skapa enkla C# klasser eller importera fullständiga projekt och bibliotek. Fullständig redigering och fel söknings miljö stöds i Visual Studio. Mer information finns [i utveckla .net standard-användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andra för hands versionerna

Följande funktioner är också tillgängliga i för hands version på begäran.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Resultat av hög prestanda i real tid med anpassade ML-modeller som hanteras av Azure Machine Learning

Azure Stream Analytics stöder real tids resultat i real tid genom att använda anpassade Machine Learning modeller som hanteras av Azure Machine Learning och som finns i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI), med hjälp av ett arbets flöde Det kräver inte att du skriver kod. [Registrera dig](https://aka.ms/asapreview1) för för hands version

### <a name="support-for-azure-stack"></a>Stöd för Azure Stack
Den här funktionen är aktive rad på Azure IoT Edge Runtime och utnyttjar anpassade Azure Stack funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel Event Hubs, IoT Hub, Blob Storage). Med den här nya integrationen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna.
Med den här funktionen kan du skapa hybrid arkitekturer som kan analysera dina data nära var de skapas, minska svars tiden och maximera insikterna. Du måste [Registrera](https://aka.ms/asapreview1) dig för den här för hands versionen.
