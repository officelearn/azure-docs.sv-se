---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 6944d2e6a8f762e62c14f6f3fa3f600a9b3c333e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195031"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func host start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

I slutet av utdata bör följande rader visas: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Om HttpExample inte visas som visas nedan, kommer du förmodligen igång värden från utsidan av projektets rotmapp. I så fall kan du använda **CTRL** + **C** för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.

Kopiera URL: en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<your-name>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett meddelande som `Hello Functions` :

![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

När du är klar kan du använda **CTRL** + **C** och välja `y` att stoppa funktions värden.
