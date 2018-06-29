---
title: Utveckla och kör lokalt Azure functions | Microsoft Docs
description: Lär dig hur du code och testa Azure functions lokalt på datorn innan du kör dem på Azure Functions.
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
ms.openlocfilehash: 89f94be4cf624914183480362ae23c62c363622f
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088668"
---
# <a name="code-and-test-azure-functions-locally"></a>Platskod och testa Azure Functions lokalt

När du befinner dig att utveckla och testa Azure Functions i den [Azure Portal], många utvecklare föredrar en lokal utveckling upplevelse. Funktioner som gör det enkelt att använda din favorit kod redigeraren och utveckling verktyg för att skapa och testa funktioner på den lokala datorn. Din lokala funktioner kan ansluta till live Azure-tjänster och du kan felsöka dem lokalt på datorn med hjälp av fullständig Functions-runtime.

## <a name="local-development-environments"></a>Lokala utvecklingsmiljöer

Hur som du utvecklar funktioner på din lokala dator beror på din [språk](supported-languages.md) och tooling inställningar. Har stöd för lokal utveckling miljöer i följande tabell:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
| [Kommandotolken eller terminal](functions-run-local.md) | [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Azure Functions grundläggande verktyg] ger core runtime och mallar för att skapa funktioner, vilket gör att lokal utveckling. Version 2.x utveckla på Linux, MacOS och Windows. Alla miljöer förlitar sig på grundläggande verktyg för lokala Functions-runtime.|
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | Den [Azure Functions-tillägget för VS kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till stöd för funktioner till VS-kod. Kräver grundläggande verktyg. Stöder utveckling på Linux-, MacOS- och Windows, när du använder version 2.x Core-verktyg. Läs mer i [distribuera till Azure med Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (klassbiblioteket)](functions-dotnet-class-library.md) | Azure Functions verktyg ingår i den **Azure-utveckling** arbetsbelastning [Visual Studio 2017 version 15,5](https://www.visualstudio.com/vs/) och senare versioner. Kan du kompilera funktioner i en klassbiblioteket och publicera DLL-filen till Azure. Innehåller grundläggande verktyg för lokal testning. Läs mer i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md). |
| [Maven 3.](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Integreras med Core verktyg för att aktivera utvecklingen av Java-funktioner. Version 2.x stöder utveckling på Linux, MacOS och Windows. Läs mer i [skapa din första funktion med Java och Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av dessa lokala utvecklingsmiljöer kan du skapa funktionen app-projekt och använda fördefinierade mallar för funktioner för att skapa nya funktioner. Var används den grundläggande verktyg så att du kan testa och felsöka dina funktioner mot verkliga Functions-runtime på den egna datorn precis som alla andra appar. Du kan också publicera du funktionen app-projekt från någon av dessa miljöer till Azure.  

## <a name="next-steps"></a>Nästa steg

+ Läs mer om lokala utvecklingen av kompilerade C#-funktioner som använder Visual Studio 2017 i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).
+ Läs mer om lokala utvecklingen av funktioner med VS-kod på en Mac, Linux eller Windows-dator i den [VS dokumentationen för Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Mer information om hur du utvecklar funktioner från Kommandotolken eller terminal finns [arbeta med Azure Functions grundläggande verktyg](functions-run-local.md).

<!-- LINKS -->

[Azure Functions grundläggande verktyg]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
