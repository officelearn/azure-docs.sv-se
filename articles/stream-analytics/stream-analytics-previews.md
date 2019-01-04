---
title: Azure Stream Analytics-förhandsversionsfunktioner
description: Den här artikeln visar de Azure Stream Analytics-funktioner som finns för närvarande i förhandsversion.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 9b721ab614bf1797604fe342de117c78ba703f96
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557605"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics-förhandsversionsfunktioner

Den här artikeln beskriver alla funktioner för närvarande i förhandsversion för Azure Stream Analytics. Använda förhandsversionsfunktioner i en produktionsmiljö rekommenderas inte.

## <a name="public-previews"></a>Offentliga förhandsversioner

Följande funktioner finns i offentlig förhandsversion. Du kan dra nytta av funktionerna i dag, men använda inte dem i din produktionsmiljö.

### <a name="integration-with-azure-machine-learning"></a>Integrering med Azure Machine Learning

Du kan skala Stream Analytics-jobb med Machine Learning (ML)-funktioner. Läs mer om hur du kan använda ML-funktioner i ditt Stream Analytics-jobb [skala ditt Stream Analytics-jobb med Azure Machine Learning-funktioner](stream-analytics-scale-with-machine-learning-functions.md). Kolla in ett verkligt scenario med [utföra attitydanalyser genom att använda Azure Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Sessionen windows

Stream Analytics har inbyggt stöd för fönsterfunktioner, så att utvecklare kan skapa komplexa strömbearbetning jobb med minimal ansträngning. [Sessionen windows](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) gruppera händelser som kommer vid samma tid filtrerar ut tidsperioder där det finns inga data. Läs mer om fönsterfunktioner [introduktion till Stream Analytics fönsterfunktioner](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>BLOB-utdata partitionering av anpassad tid

Azure Stream Analytics kan skickas till Blob-lagring baserat på anpassad tidsattribut. För mer information, besök [anpassad DateTime sökvägsmönster för Azure Stream Analytics blob storage-utdata](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>JavaScript användardefinierad samling

Azure Stream Analytics stöder användardefinierade aggregeringar (UDA) skriven i JavaScript, vilket gör att du kan implementera komplex tillståndskänsliga affärslogik. Lär dig hur du använder UDA: er från den [Azure Stream Analytics JavaScript-användardefinierade aggregeringar](stream-analytics-javascript-user-defined-aggregates.md) dokumentation. 

### <a name="live-data-testing-in-visual-studio"></a>Live data testning i Visual Studio

Visual Studio tools för Azure Stream Analytics förbättra lokal testning funktionen som gör att du kan testa frågor mot live händelseströmmar från molnkällor, till exempel Händelsehubb eller IoT hub. Lär dig hur du [testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET-funktioner på IoT Edge

Du kan köra .NET Standard kod med .NET standard användardefinierade funktioner, som en del av din pipeline för dataströmning. Du kan skapa enkla C# klasserna eller Importera projekt och bibliotek. Fullständig skapar och felsöker upplevelsen stöds i Visual Studio. För mer information, besök [utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Privata förhandsversioner

Följande funktioner är i privat förhandsvisning. Gå till den privata förhandsgranskningen i Azure Stream Analytics för att komma åt dessa förhandsversioner [registrera](https://aka.ms/ASApreview1) sidan.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Stream Analytics introducerar nya machine learning-modeller med stöd för *topp* och *dalar* identifiering förutom dubbelriktad, långsam positiva och långsamma negativa trender identifiering.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# anpassade deserialiserare för Azure Stream Analytics på IoT Edge

Utvecklare kan nu användas för att implementera anpassade deserializers i C# för att deserialisera händelser som tagits emot av Azure Stream Analytics. Exempel på format som kan deserialiseras är Parquet, Protobuf, XML eller alla binära dataformat.

### <a name="blob-output-partitioning-by-custom-attribute"></a>BLOB-utdata partitionering av anpassade attribut

Nu är det möjligt att partitionera dina Azure Stream Analytics-utdata till Blob-lagring baserat på alla kolumner i frågan.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Hanterade identiteter för autentisering av Azure-resurser till Azure Data Lake Storage

Du kan nu utföra åtgärder i realtid pipelines med hanterade identiteter för Azure-resurser baserat autentisering vid skrivning till Azure Data Lake Storage Gen1 så att du kan skapa jobb programmässigt. För ytterligare information, besök [Använd hanterade identiteter för Azure-resurser kan autentisera Azure Stream Analytics-jobb till Azure Data Lake Storage Gen1 utdata](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Nästa steg

* [Åtta nya funktioner i Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [4 nya funktioner finns nu i Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
