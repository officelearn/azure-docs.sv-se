---
title: Azure Stream Analytics-förhandsversionsfunktioner
description: Den här artikeln visar de Azure Stream Analytics-funktioner som finns för närvarande i förhandsversion.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561139"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics-förhandsversionsfunktioner

Den här artikeln beskriver alla funktioner för närvarande i förhandsversion för Azure Stream Analytics. Använda förhandsversionsfunktioner i en produktionsmiljö rekommenderas inte.

## <a name="public-previews"></a>Offentliga förhandsversioner

Följande funktioner finns i offentlig förhandsversion. Du kan dra nytta av funktionerna i dag, men använda inte dem i din produktionsmiljö.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code för Azure Stream Analytics (utgiven maj 2019)

Azure Stream Analytics-jobb kan skapas i Visual Studio Code. Se våra [VS Code komma igång-självstudiekurs](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Stream Analytics introducerar nya machine learning-modeller med stöd för *topp* och *dalar* identifiering förutom dubbelriktad, långsam positiva och långsamma negativa trender identifiering. För mer information, besök [avvikelseidentifiering i Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Referensdata för SQL-databas

Azure Stream Analytics har stöd för Azure SQL Database som en källa av indata för referensdata. Du kan använda SQL-databas som referensdata för ditt Stream Analytics-jobb i Azure-portalen och i Visual Studio med Stream Analytics-verktyg. Mer information, besök, [Använd referensdata från en SQL-databas till Azure Stream Analytics-jobb](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics-jobb med Machine Learning (ML)-funktioner. Läs mer om hur du kan använda ML-funktioner i ditt Stream Analytics-jobb [skala ditt Stream Analytics-jobb med Azure Machine Learning-funktioner](stream-analytics-scale-with-machine-learning-functions.md). Kolla in ett verkligt scenario med [utföra attitydanalyser genom att använda Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>JavaScript användardefinierad samling

Azure Stream Analytics stöder användardefinierade aggregeringar (UDA) skriven i JavaScript, vilket gör att du kan implementera komplex tillståndskänsliga affärslogik. Lär dig hur du använder UDA: er från den [Azure Stream Analytics JavaScript-användardefinierade aggregeringar](stream-analytics-javascript-user-defined-aggregates.md) dokumentation. 

### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio tools för Azure Stream Analytics förbättra lokal testning funktionen som gör att du kan testa frågor mot live händelseströmmar från molnkällor, till exempel Händelsehubb eller IoT hub. Lär dig hur du [testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-funktioner på IoT Edge

Du kan köra .NET Standard kod med .NET standard användardefinierade funktioner, som en del av din pipeline för dataströmning. Du kan skapa enkla C# klasserna eller Importera projekt och bibliotek. Fullständig skapar och felsöker upplevelsen stöds i Visual Studio. För mer information, besök [utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Andra förhandsversioner

Följande funktioner är också tillgängliga i förhandsversionen.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#anpassade deserialiserare för Azure Stream Analytics på IoT Edge och molnet (Announced maj 2019)

Utvecklare kan implementera anpassade deserializers i C# rekonstrukci händelser som tagits emot av Azure Stream Analytics. Exempel på format som kan deserialiseras är Parquet, Protobuf, XML eller alla binära dataformat. Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Parquet-utdata (meddelade maj 2019)
Parquet är ett kolumnformat som aktiverar effektiv bearbetning av stordata. Genom att skicka ut data i Parquet-format i en datasjö du dra nytta av Azure Stream Analytics att power stor skala streaming ETL och köra batch-bearbetning, träna machine learning-algoritmer eller köra interaktiva frågor på dina historiska data. Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Ett klick integrering med Event Hubs (Announced maj 2019) 
Med den här integreringen kommer du nu att kunna visualisera inkommande data och börja skriva en Stream Analytics-fråga med ett enda klick från Event Hub-portalen. När din fråga är klar, kommer du att kunna productize med några klick och börja få insikter i realtid. Detta minskar avsevärt tiden och kostnaderna för att utveckla lösningar för realtidsanalys. Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Stöd för Azure Stack (meddelade maj 2019)
Den här funktionen aktiverad på Azure IoT Edge-körningen utnyttjar anpassade Azure Stack-funktioner, till exempel inbyggt stöd för lokala indata och utdata som körs på Azure Stack (till exempel Händelsehubbar, IoT Hub, Blob Storage). Den här nya integrationen kan du skapa hybridarkitekturer som kan analysera dina data nära var den genereras, vilket minskar svarstiden och maximera insikter.
Registrera dig för den här förhandsversionen [här](https://aka.ms/asapreview1).

