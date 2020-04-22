---
title: Jämförelse av funktionen Azure Stream Analytics
description: I den här artikeln jämförs de funktioner som stöds för Azure Stream Analytics-moln- och IoT Edge-jobb i Azure-portalen, Visual Studio och Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d3b27a50fa86916b71c84b30ecdbf45deb0ec45c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770755"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Jämförelse av funktionen Azure Stream Analytics

Med Azure Stream Analytics kan du skapa strömningslösningar i molnet och på IoT Edge med [Azure Portal,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)och Visual [Studio Code](quick-create-vs-code.md). Tabellerna i den här artikeln visar vilka funktioner som stöds av varje plattform för båda jobbtyperna.

> [!NOTE]
> Visual Studio och Visual Studio Code-verktyg stöder inte jobb i Regionerna China East, China North, Germany Central och Germany NorthEast.

## <a name="cloud-job-features"></a>Jobbfunktioner i molnet


|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Plattformsoberoende     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Skriva författande     |Ja         |Ja         |Ja         |
|Manus Intellisense     |Syntaxmarkering         |Syntaxmarkering</br>Slutförande av kod</br>Felmarkör         |Syntaxmarkering</br>Slutförande av kod</br>Felmarkör         |
|Definiera alla typer av indata, utdata och jobbkonfigurationer     |Ja         |Ja         |Ja         |
|Källkontroll     |Inga         |Ja         |Ja         |
|CI/CD-stöd     |Delvis         |Ja         |Ja         |
|Dela indata och utdata över flera frågor     |Inga         |Ja         |Ja         |
|Frågetestning med en exempelfil     |Ja         |Ja        |Ja         |
|Lokal testning av realtidsdata     |Inga         |Ja       |Ja      |
|Lista jobb och visa jobbentiteter     |Ja         |Ja        |Ja         |
|Exportera ett jobb till ett lokalt projekt     |Inga         |Ja         |Ja         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Ja         |
|Visa jobbmått och diagram     |Ja         |Ja         |Öppna i portalen         |
|Visa jobbkörningsfel     |Ja         |Ja         |Inga         |
|Diagnostikloggar     |Ja         |Inga         |Inga         |
|Egenskaper för anpassade meddelanden     |Ja         |Ja         |Inga       |
|C# anpassad kodfunktion och Deserializer|Skrivskyddat läge|Ja|Inga|
|JavaScript UDF och UDA     |Ja         |Ja         |Endast Windows         |
|Machine Learning Service     |Ja        |Ja         |Inga         |
|Machine Learning Studio     |Ja, men frågan kan inte testas        |Ja |Inga         |
|Efterlevnadsnivå     |1.0</br>1.1</br>1.2 (standard)         |1.0</br>1.1</br>1.2 (standard)           |1.0</br>1.1</br>1.2 (standard)           |
|Inbyggda ML-baserade avvikelseidentifieringsfunktioner     |Ja         |Ja         |Ja         |
|Inbyggda geospatiala funktioner     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>Jobbfunktioner för IoT Edge

|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Jobb författande     |Ja         |Ja         |Inga         |
|Källkontroll     |Inga         |Ja         |Inga         |
|Exportera ett jobb till ett lokalt projekt     |Inga         |Ja         |Inga         |
|Frågetestning med en exempelfil     |Ja         |Ja         |Inga         |
|Dela indata och utdata över flera frågor     |Inga         |Ja         |Inga         |
|UDF för C#     |Inga         |Ja         |Inga         |
|Skicka jobb     |Ja         |Ja         |Inga         |
|Lista jobb och visa jobbentiteter     |Ja         |Ja         |Inga         |
|Visa jobbmått och diagram     |Ja         |Delvis         |Inga         |
|Visa jobbkörningsfel     |Ja         |Delvis         |Inga         |
|CI/CD-stöd     |Inga         |Inga         |Inga         |


## <a name="next-steps"></a>Nästa steg

* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [Självstudiekurs: Skriv en C# användardefinierad funktion för Azure Stream Analytics IoT Edge-jobb (förhandsversion)](stream-analytics-edge-csharp-udf.md)
* [Utveckla Stream Analytics IoT Edge-jobb med Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Utforska Azure Stream Analytics med Visual Studio-kod (förhandsversion)](visual-studio-code-explore-jobs.md)


