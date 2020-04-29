---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673356"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Lägg till en definition för utgående bindning i funktionen

Även om en funktion bara kan ha en utlösare, kan den ha flera indata-och utgående bindningar, vilket gör att du kan ansluta till andra Azure-tjänster och-resurser utan att skriva anpassad integrerings kod. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Du deklarerar dessa bindningar i *Function. JSON* -filen i mappen function. I den tidigare snabb starten innehåller din *Function. JSON* -fil i mappen *HttpExample* två bindningar i `bindings` samlingen:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Varje bindning har minst en typ, en riktning och ett namn. I exemplet ovan är den första bindningen av typen `httpTrigger` med riktningen. `in` I `in` riktningen `name` anger du namnet på en indataparameter som skickas till funktionen när den anropas av utlösaren.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Den andra bindningen i samlingen heter `res`. Den `http` här bindningen är en utgående`out`bindning () som används för att skriva http-svaret. 

Om du vill skriva till en Azure Storage kö från den här funktionen `out` lägger du till `queue` en bindning av `msg`typen med namnet, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Den andra bindningen i samlingen är av typen `http` med riktningen `out`, i så fall är den särskilda `name` av `$return` anger att den här bindningen använder funktionens retur värde istället för att ange en indataparameter.

Om du vill skriva till en Azure Storage kö från den här funktionen `out` lägger du till `queue` en bindning av `msg`typen med namnet, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Den andra bindningen i samlingen heter `res`. Den `http` här bindningen är en utgående`out`bindning () som används för att skriva http-svaret. 

Om du vill skriva till en Azure Storage kö från den här funktionen `out` lägger du till `queue` en bindning av `msg`typen med namnet, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
I det här fallet `msg` anges funktionen som ett argument för utdata. För en `queue` typ måste du också ange namnet på kön i `queueName` och ange *namnet* på Azure Storage anslutning (från *Local. Settings. JSON*) i `connection`. 
::: zone-end  
