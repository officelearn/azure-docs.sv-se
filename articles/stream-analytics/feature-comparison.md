---
title: Jämförelse av Azure Stream Analyticss funktion
description: I den här artikeln jämförs de funktioner som stöds för Azure Stream Analytics moln-och IoT Edges jobb i Azure Portal, Visual Studio och Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8cdba27ac949584e1fa96e3f7b0874f4fc0d4212
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443665"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Jämförelse av Azure Stream Analyticss funktion

Med Azure Stream Analytics kan du skapa strömmande lösningar i molnet och på IoT Edge med hjälp av [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)och [Visual Studio Code](quick-create-vs-code.md). Tabellerna i den här artikeln visar vilka funktioner som stöds av varje plattform för båda jobb typerna.

## <a name="cloud-job-features"></a>Funktioner i moln jobb


|Funktion  |Portalen  |Visual Studio  |Visual Studio-kod  |
|---------|---------|---------|---------|
|Plattforms oberoende     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Skript redigering     |Ja         |Ja         |Ja         |
|IntelliSense för skript     |Markering av syntax         |Markering av syntax</br>Kod komplettering</br>Fel markör         |Markering av syntax</br>Kod komplettering</br>Fel markör         |
|Definiera alla typer av indata, utdata och jobb konfiguration     |Ja         |Ja         |Ja         |
|Källkontroll     |Inga         |Ja         |Ja         |
|CI/CD-stöd     |Delvis         |Ja         |Ja         |
|Dela indata och utdata över flera frågor     |Inga         |Ja         |Ja         |
|Fråga testning med en exempel fil     |Ja         |Ja        |Ja         |
|Lokal testning av Live data     |Inga         |Ja       |Ja      |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja        |Ja         |
|Exportera ett jobb till ett lokalt projekt     |Inga         |Ja         |Ja         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Ja         |
|Visa jobb mått och diagram     |Ja         |Ja         |Öppna i portalen         |
|Visa jobb körnings fel     |Ja         |Ja         |Inga         |
|Diagnostikloggar     |Ja         |Inga         |Inga         |
|Egenskaper för anpassat meddelande     |Ja         |Ja         |Inga       |
|C#anpassad kod funktion och deserialiserare|Skrivskyddat läge|Ja|Inga|
|JavaScript UDF och UDA     |Ja         |Ja         |Endast Windows         |
|Machine Learning Service     |Ja, men det går inte att testa frågan        |Ja         |Inga         |
|Machine Learning Studio     |Ja, men det går inte att testa frågan        |Ja |Inga         |
|Efterlevnadsnivå     |1.0</br>1.1</br>1,2 (standard)         |1.0</br>1.1</br>1,2 (standard)           |1.0</br>1.1</br>1,2 (standard)           |
|Inbyggda ML-baserade avvikelse identifierings funktioner     |Ja         |Ja         |Ja         |
|Inbyggda GeoSpatiala funktioner     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>IoT Edge jobb funktioner

|Funktion  |Portalen  |Visual Studio  |Visual Studio-kod  |
|---------|---------|---------|---------|
|Jobb redigering     |Ja         |Ja         |Inga         |
|Källkontroll     |Inga         |Ja         |Inga         |
|Exportera ett jobb till ett lokalt projekt     |Inga         |Ja         |Inga         |
|Fråga testning med en exempel fil     |Ja         |Ja         |Inga         |
|Dela indata och utdata över flera frågor     |Inga         |Ja         |Inga         |
|C# UDF     |Inga         |Ja         |Inga         |
|Skicka jobb     |Ja         |Ja         |Inga         |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja         |Inga         |
|Visa jobb mått och diagram     |Ja         |Delvis         |Inga         |
|Visa jobb körnings fel     |Ja         |Delvis         |Inga         |
|CI/CD-stöd     |Inga         |Inga         |Inga         |


## <a name="next-steps"></a>Nästa steg

* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [Självstudie: skriva en C# användardefinierad funktion för Azure Stream Analytics IoT Edge jobb (förhands granskning)](stream-analytics-edge-csharp-udf.md)
* [Utveckla Stream Analytics IoT Edge jobb med Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (för hands version)](visual-studio-code-explore-jobs.md)


