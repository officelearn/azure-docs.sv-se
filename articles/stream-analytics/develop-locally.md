---
title: Utveckla och felsöka Azure Stream Analytics-jobb lokalt
description: Lär dig hur du utvecklar och testar Azure Stream Analytics-jobb på din lokala dator innan du kör dem i Azure-portalen.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879848"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Utveckla och felsöka Azure Stream Analytics-jobb lokalt

Medan du kan skapa och testa Azure Stream Analytics-jobb i Azure-portalen föredrar många utvecklare en lokal utvecklingsupplevelse. Stream Analytics gör det enkelt att använda din favoritkodredigerare och utvecklingsverktyg för att skapa och testa jobb med livehändelseströmmar från Azure Event Hub, IoT Hub och Blob Storage med en fullständigt fungerad lokal körning av en nod. Du kan också skicka jobb till Azure direkt från din lokala utvecklingsmiljö.

## <a name="local-development-environments"></a>Lokala utvecklingsmiljöer

Hur du utvecklar Stream Analytics-jobb på den lokala datorn beror på dina verktygsinställningar och funktionstillgänglighet. Se [Jämförelsen med Funktionen Azure Stream Analytics](feature-comparison.md) för att se vilka funktioner som stöds för varje utvecklingsmiljö.

Miljöerna i följande tabell stöder lokal utveckling:

|Miljö                              |Beskrivning    |
|-----------------------------------------|------------|
|[Visual Studio-koden](visual-studio-code-explore-jobs.md)| [Azure Stream Analytics Tools-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) för Visual Studio-kod gör att du kan skapa, hantera och testa ditt Stream-analysjobb både lokalt och i molnet med omfattande IntelliSense- och inbyggd källkontroll. Stöder utveckling på Linux, MacOS och Windows. Mer information finns [i Skapa ett Azure Stream Analytics-jobb i Visual Studio Code](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics Tools är en del av Azure-utvecklings- och datalagrings- och bearbetningsarbetsbelastningarna i Visual Studio. Du kan använda Visual Studio för att skriva anpassade C# användardefinierade funktioner och deserializers. Mer information finns i [Skapa ett Azure Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md).|
|[Kommandotolken eller terminalen](stream-analytics-tools-for-visual-studio-cicd.md)|Azure Stream Analytics CI/CD NuGet-paketet innehåller verktyg för Visual Studio-projektversion, lokal testning på en godtycklig dator. Azure Stream Analytics CI/CD npm-paketet innehåller verktyg för Projektversioner av Visual Studio-kod (som genererar en Azure Resource Manager-mall) på en godtycklig dator.|

## <a name="next-steps"></a>Nästa steg

* [Testa Stream Analytics-frågor lokalt med exempeldata med Visual Studio-kod](visual-studio-code-local-run.md)
* [Testa Stream Analytics-frågor lokalt mot livestreamindata med hjälp av Visual Studio-kod](visual-studio-code-local-run-live-input.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-live-data-local-testing.md)
