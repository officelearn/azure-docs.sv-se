---
title: Utveckla och köra Azure-funktioner lokalt
description: Lär dig hur du kodar och testar Azure-funktioner på din lokala dator innan du kör dem på Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230634"
---
# <a name="code-and-test-azure-functions-locally"></a>Koda och testa Azure Functions lokalt

Medan du kan utveckla och testa Azure-funktioner i [Azure-portalen]föredrar många utvecklare en lokal utvecklingsupplevelse. Funktioner gör det enkelt att använda din favorit kodredigerare och utvecklingsverktyg för att skapa och testa funktioner på din lokala dator. Dina lokala funktioner kan ansluta till live Azure-tjänster och du kan felsöka dem på din lokala dator med hjälp av den fullständiga funktionerna.

## <a name="local-development-environments"></a>Lokala utvecklingsmiljöer

Hur du utvecklar funktioner på den lokala datorn beror på [språk-](supported-languages.md) och verktygsinställningarna. Miljöerna i följande tabell stöder lokal utveckling:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
|[Visual Studio-kod](functions-develop-vs-code.md)| [C# (klassbibliotek),](functions-dotnet-class-library.md) [C#-skript (.csx),](functions-reference-csharp.md) [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [Azure Functions-tillägget för VS-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till funktionsstöd i VS-kod. Kräver core tools. Stöder utveckling på Linux, MacOS och Windows när du använder version 2.x av Core Tools. Mer information finns i [Skapa din första funktion med Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Kommandotolken eller terminalen](functions-run-local.md) | [C# (klassbibliotek),](functions-dotnet-class-library.md) [C#-skript (.csx),](functions-reference-csharp.md) [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] tillhandahåller grundläggande körning och mallar för att skapa funktioner som möjliggör lokal utveckling. Version 2.x stöder utveckling på Linux, MacOS och Windows. Alla miljöer är beroende av Core Tools för den lokala funktioner runtime. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md) | Azure Functions-verktygen ingår i **Azure-utvecklingsarbetsbelastningen** i [Visual Studio 2019](https://www.visualstudio.com/vs/) och senare versioner. Gör att du kan kompilera funktioner i ett klassbibliotek och publicera DLL till Azure. Innehåller de grundläggande verktygen för lokala tester. Mer information finns i [Utveckla Azure-funktioner med Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (olika) | [Java](functions-reference-java.md) | Integrerar med Core Tools för att möjliggöra utveckling av Java-funktioner. Version 2.x stöder utveckling på Linux, MacOS och Windows. Mer information finns i [Skapa din första funktion med Java och Maven](functions-create-first-java-maven.md). Stöder också utveckling med [Eclipse](functions-create-maven-eclipse.md) och [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av dessa lokala utvecklingsmiljöer kan du skapa funktion app projekt och använda fördefinierade funktioner mallar för att skapa nya funktioner. Var och en använder Core Tools så att du kan testa och felsöka dina funktioner mot den verkliga funktioner runtime på din egen maskin precis som du skulle någon annan app. Du kan också publicera ditt funktionsappprojekt från någon av dessa miljöer till Azure.  

## <a name="next-steps"></a>Nästa steg

+ Mer information om lokal utveckling av kompilerade C#-funktioner med Visual Studio 2019 finns i [Utveckla Azure-funktioner med Visual Studio](functions-develop-vs.md).
+ Mer information om lokal utveckling av funktioner som använder VS-kod på en Mac-, Linux- eller Windows-dator finns i [Distribuera Azure-funktioner från VS-kod](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Mer information om hur du utvecklar funktioner från kommandotolken eller terminalen finns i [Arbeta med Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Grundläggande verktyg för Azure-funktioner]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
