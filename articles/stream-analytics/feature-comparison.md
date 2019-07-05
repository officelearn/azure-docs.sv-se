---
title: Jämför med Azure Stream Analytics-funktioner
description: Den här artikeln jämförs funktioner som stöds för Azure Stream Analytics-moln- och IoT Edge-jobb i Azure portal, Visual Studio och Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509786"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Jämför med Azure Stream Analytics-funktioner

Med Azure Stream Analytics kan du kan skapa direktuppspelade lösningar i molnet och på IoT Edge med [Azure-portalen](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), och [Visual Studio Code](quick-create-vs-code.md). Tabellerna i den här artikeln visar vilka funktioner som stöds av varje plattform för båda typer av jobb.

## <a name="cloud-job-features"></a>Molnfunktioner för jobbet


|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Plattformsoberoende     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Redigering av skript     |Ja         |Ja         |Ja         |
|Skriptet Intellisense     |Syntaxmarkering         |Syntaxmarkering</br>Kodifyllning</br>Fel markör         |Syntaxmarkering</br>Kodifyllning</br>Fel markör         |
|Definiera indata, utdata och jobbkonfigurationer     |Ja         |Ja         |Ja         |
|Partitionering av BLOB-utdata     |Ja         |Ja         |Ja         |
|Powerbi som utdata     |Ja         |Ja         |Nej         |
|Referensdata för SQL-databas     |Ja         |Ja         |Ja         |
|Anpassade meddelandeegenskaper     |Ja         |Nej         |Nej         |
|Dela in- och utdata i flera frågor     |Nej         |Ja         |Ja         |
|JavaScript UDF och UDA     |Ja         |Ja         |Endast Windows         |
|Machine Learning bildtexter     |Ja, men frågan går inte att testa        |Ja, men det går inte att testa lokalt         |Nej         |
|Efterlevnadsnivå     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Inbyggda funktioner för ML-baserad Avvikelseidentifiering     |Ja         |Ja         |Ja         |
|Inbyggda geospatiala funktioner     |Ja         |Ja         |Ja         |
|Frågetestning med en exempelfil     |Ja         |Ja         |Ja         |
|Live data lokal testning     |Nej         |Ja         |Nej         |
|Lista jobb och visa jobb entiteter     |Ja         |Ja         |Ja         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Ja         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Ja         |
|Källkontroll     |Nej         |Ja         |Ja         |
|Stöd för CI/CD     |Delvis         |Ja         |Ja         |
|Visa jobbmått och diagram     |Ja         |Ja         |Öppna i portalen         |
|Visa jobb körningsfel     |Ja         |Ja         |Nej         |
|Diagnostikloggar     |Ja         |Nej         |Nej         |


## <a name="iot-edge-job-features"></a>Funktioner för IoT Edge-jobb

|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Jobbet redigering     |Ja         |Ja         |Nej         |
|Källkontroll     |Nej         |Ja         |Nej         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Nej         |
|Frågetestning med en exempelfil     |Ja         |Ja         |Nej         |
|Dela in- och utdata i flera frågor     |Nej         |Ja         |Nej         |
|C# UDF     |Nej         |Ja         |Nej         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Nej         |
|Lista jobb och visa jobb entiteter     |Ja         |Ja         |Nej         |
|Visa jobbmått och diagram     |Ja         |Delvis         |Nej         |
|Visa jobb körningsfel     |Ja         |Delvis         |Nej         |
|Stöd för CI/CD     |Nej         |Nej         |Nej         |


## <a name="next-steps"></a>Nästa steg

* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [Självstudie: Skriv en C# användardefinierad funktion för Azure Stream Analytics IoT Edge-jobb (förhandsgranskning)](stream-analytics-edge-csharp-udf.md)
* [Utveckla Stream Analytics IoT Edge-jobb med hjälp av Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (förhandsversion)](vscode-explore-jobs.md)


