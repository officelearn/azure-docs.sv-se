---
title: Utveckla och köra Azure functions lokalt | Microsoft Docs
description: Lär dig mer om att koda och testa Azure functions lokalt på datorn innan du kör dem på Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/27/2018
ms.author: glenga
ms.openlocfilehash: e82981471306c9a9d09751da15256e85b770eb1a
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125047"
---
# <a name="code-and-test-azure-functions-locally"></a>Koda och testa Azure Functions lokalt

När du använder kan du utveckla och testa Azure Functions i den [Azure-portalen], många utvecklare som föredrar en lokal utvecklingsmiljö. Functions gör det enkelt att använda dina favoritverktyg kod redigeraren och utveckling verktyg för att skapa och testa functions lokalt på datorn. Din lokala funktioner kan ansluta levande Azure-tjänster och du kan felsöka dem på din lokala dator med hjälp av den fullständiga Functions-körningen.

## <a name="local-development-environments"></a>Lokal utvecklingsmiljö

Hur som du utvecklar funktioner på din lokala dator beror på din [språk](supported-languages.md) och verktygsinställningar. Miljöer i tabellen nedan har stöd för lokal utveckling:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
| [Kommandotolk eller terminal](functions-run-local.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md), [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [Java](functions-reference-java.md) | [Azure Functions Core Tools] ger core runtime och mallar för att skapa funktioner, vilket gör det möjligt för lokal utveckling. Version 2.x stöder utveckling på Linux, MacOS och Windows. Alla miljöer som förlitar sig på Core Tools för lokal Functions-körningen. |
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | Den [Azure Functions-tillägg för VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till funktioner stödjer att VS Code. Kräver de viktigaste verktygen. Har stöd för utveckling på Linux, MacOS och Windows, när du använder version 2.x av de viktigaste verktygen. Mer information finns i [distribuera till Azure med Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md) | Azure Functions-verktygen som ingår i den **Azure development** arbetsbelastningen [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/) och senare versioner. Kan du kompilera funktioner i en klassbiblioteket och publicera DLL-filen till Azure. Innehåller de viktigaste verktygen för lokal testning. Mer information finns i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md). |
| [Maven 3.](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Integreras med Core verktyg för utveckling av Java-funktioner. Version 2.x stöder utveckling på Linux, MacOS och Windows. Mer information finns i [skapa din första funktion med Java och Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av dessa miljöer för lokal utveckling kan du skapa funktionen app-projekt och använder fördefinierade Functions-mallar för att skapa nya funktioner. Var och en använder de viktigaste verktygen så att du kan testa och felsöka dina funktioner mot verkliga Functions-körningen på din egen dator precis som andra appar. Du kan också publicera du funktionsappsprojekt från någon av dessa miljöer till Azure.  

## <a name="next-steps"></a>Nästa steg

+ Läs mer om lokal utveckling av kompilerade C#-funktioner som använder Visual Studio 2017 i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).
+ Läs mer om lokal utveckling av funktioner som använder VS Code på en Mac, Linux eller Windows-dator i den [VS Code-dokumentation för Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Mer information om hur du utvecklar funktioner från Kommandotolken eller terminal finns [arbeta med Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portalen]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
