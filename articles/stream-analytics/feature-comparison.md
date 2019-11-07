---
title: Jämförelse av Azure Stream Analyticss funktion
description: I den här artikeln jämförs de funktioner som stöds för Azure Stream Analytics moln-och IoT Edges jobb i Azure Portal, Visual Studio och Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580908"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Jämförelse av Azure Stream Analyticss funktion

Med Azure Stream Analytics kan du skapa strömmande lösningar i molnet och på IoT Edge med hjälp av [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)och [Visual Studio Code](quick-create-vs-code.md). Tabellerna i den här artikeln visar vilka funktioner som stöds av varje plattform för båda jobb typerna.

## <a name="cloud-job-features"></a>Funktioner i moln jobb


|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Plattforms oberoende     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Skript redigering     |Ja         |Ja         |Ja         |
|IntelliSense för skript     |Markering av syntax         |Markering av syntax</br>Kod komplettering</br>Fel markör         |Markering av syntax</br>Kod komplettering</br>Fel markör         |
|Definiera indata, utdata och jobb konfiguration     |Ja         |Ja         |Ja         |
|Partitionering av BLOB-utdata     |Ja         |Ja         |Ja         |
|Power BI som utdata     |Ja         |Ja         |Nej         |
|Referens data för SQL Database     |Ja         |Ja         |Ja         |
|Egenskaper för anpassat meddelande     |Ja         |Nej         |Nej         |
|Dela indata och utdata över flera frågor     |Nej         |Ja         |Ja         |
|JavaScript UDF och UDA     |Ja         |Ja         |Endast Windows         |
|Machine Learning bild texter     |Ja, men det går inte att testa frågan        |Ja, men kan inte testas lokalt         |Nej         |
|Efterlevnadsnivå     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Inbyggda ML-baserade avvikelse identifierings funktioner     |Ja         |Ja         |Ja         |
|Inbyggda GeoSpatiala funktioner     |Ja         |Ja         |Ja         |
|Fråga testning med en exempel fil     |Ja         |Ja         |Ja         |
|Lokal testning av Live data     |Nej         |Ja         |Nej         |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja         |Ja         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Ja         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Ja         |
|Källkontroll     |Nej         |Ja         |Ja         |
|CI/CD-stöd     |Delvis         |Ja         |Ja         |
|Visa jobb mått och diagram     |Ja         |Ja         |Öppna i portalen         |
|Visa jobb körnings fel     |Ja         |Ja         |Nej         |
|Diagnostikloggar     |Ja         |Nej         |Nej         |


## <a name="iot-edge-job-features"></a>IoT Edge jobb funktioner

|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Jobb redigering     |Ja         |Ja         |Nej         |
|Källkontroll     |Nej         |Ja         |Nej         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Nej         |
|Fråga testning med en exempel fil     |Ja         |Ja         |Nej         |
|Dela indata och utdata över flera frågor     |Nej         |Ja         |Nej         |
|C#UDF     |Nej         |Ja         |Nej         |
|Skicka jobb     |Ja         |Ja         |Nej         |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja         |Nej         |
|Visa jobb mått och diagram     |Ja         |Delvis         |Nej         |
|Visa jobb körnings fel     |Ja         |Delvis         |Nej         |
|CI/CD-stöd     |Nej         |Nej         |Nej         |


## <a name="next-steps"></a>Nästa steg

* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [Självstudie: skriva en C# användardefinierad funktion för Azure Stream Analytics IoT Edge jobb (förhands granskning)](stream-analytics-edge-csharp-udf.md)
* [Utveckla Stream Analytics IoT Edge jobb med Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (för hands version)](vscode-explore-jobs.md)


