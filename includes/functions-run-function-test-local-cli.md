---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190895"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

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
> Om HttpExample inte visas som visas nedan, kommer du förmodligen igång värden från mappen *HttpExample* . I så fall kan du använda **Ctrl**+**C** för att stoppa värden, navigera till den överordnade *LocalFunctionProj* -mappen och köra föregående kommando igen.

Kopiera URL: en för din `HttpExample`-funktion från utdata till en webbläsare och Lägg till frågesträngen `?name=<your-name>`, vilket gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions`. Webbläsaren ska visa ett meddelande som `Hello Functions`:

![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminalen där du körde `func start` visar också logg data när du gör förfrågningar.

När du är klar använder du **Ctrl**+**C** och väljer `y` för att stoppa funktions värden.