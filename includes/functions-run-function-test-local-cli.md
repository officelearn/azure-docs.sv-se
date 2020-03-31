---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190895"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör din funktion genom att starta den lokala Azure Functions-körningsvärden från mappen *LocalFunctionProj:*

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Mot slutet av utdata ska följande rader visas: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Om HttpExample inte visas som visas nedan, du förmodligen startat värden från i *httpexample* mappen. I så fall använder du **Ctrl**+**C** för att stoppa värden, navigera till den överordnade *mappen LocalFunctionProj* och köra föregående kommando igen.

Kopiera URL:en `HttpExample` för din funktion från den här `?name=<your-name>`utdata till en `http://localhost:7071/api/HttpExample?name=Functions`webbläsare och lägg till frågesträngen, vilket gör den fullständiga webbadressen som . Webbläsaren bör visa ett `Hello Functions`meddelande som:

![Resultatet av funktionen som körs lokalt i webbläsaren](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminalen där du `func start` körde visar också loggutdata när du gör begäranden.

När du är klar använder du `y` **Ctrl**+**C** och väljer att stoppa funktionernas värd.