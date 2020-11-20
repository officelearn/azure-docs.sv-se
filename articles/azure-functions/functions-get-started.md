---
title: Komma igång med Azure Functions
description: Ta de första stegen mot att arbeta med Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975029"
---
# <a name="getting-started-with-azure-functions"></a>Komma igång med Azure Functions

## <a name="introduction"></a>Introduktion

Med [Azure Functions](./functions-overview.md) kan du implementera systemets logik i lätt tillgängliga kodblock. Kodblock kallas för "Functions".

Använd följande resurser för att komma igång.

::: zone pivot="programming-language-csharp"

| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Kommandorad](./create-first-function-cli-csharp.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Utforska en interaktiv självstudie**| <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Kör en Azure-funktion med utlösare](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Java/maven-funktion med Terminal/kommando-prompt](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ idé](./functions-create-maven-intellij.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Utforska en interaktiv självstudie**| <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Utveckla en app med hjälp av Maven-plugin-programmet för Azure Functions](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js Terminal/kommando-prompt](./create-first-function-cli-java.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Omstrukturera Node.js Express-API:er till serverlösa API:er med Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs språk referens för [Java Script](./functions-reference-node.md) eller [typescript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | <li>Använda [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Kör en Azure-funktion med utlösare](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [PowerShell-språkreferensen](./functions-reference-powershell.md))|
::: zone-end

::: zone pivot="programming-language-python"
| Action | Resurser |
| --- | --- |
| **Skapa din första funktion** | Använd något av följande verktyg:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminal/kommando-prompt](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Se en funktion som kör** | <li>[Azure samples-webbläsare](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Azure community-bibliotek](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Utforska en interaktiv självstudie** | <li>[Välj den bästa serverlösa teknikerna i Azure för ditt företagsscenario](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Väl utformat ramverk – prestanda effektivitet](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Skapa serverlösa API:er med Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Skapa serverfri logik med Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Se Microsoft Learn för en [fullständig lista över interaktiva självstudier](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions).|
| **Läs mer i djupet** | <li>Lär dig hur funktioner [automatiskt ökar eller minskar](./functions-scale.md) instanser för att matcha efter frågan<li>Utforska de olika [distributions metoder](./functions-deployment-technologies.md) som är tillgängliga<li>Använd inbyggda [övervaknings verktyg](./functions-monitoring.md) för att analysera dina funktioner<li>Läs [språk referens för python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att utveckla ett Azure Functions program](./functions-reference.md)
