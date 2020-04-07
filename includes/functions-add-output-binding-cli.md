---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673356"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Lägga till en utdatabindningsdefinition i funktionen

Även om en funktion bara kan ha en utlösare kan den ha flera indata- och utdatabindningar, vilket gör att du kan ansluta till andra Azure-tjänster och resurser utan att skriva anpassad integrationskod. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Du deklarerar dessa bindningar i *filen function.json* i funktionsmappen. Från föregående snabbstart innehåller *filen function.json* i mappen *HttpExample* två `bindings` bindningar i samlingen:  
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
Varje bindning har minst en typ, en riktning och ett namn. I exemplet ovan är den första `httpTrigger` bindningen `in`av typen med riktningen . För `in` riktningen `name` anger namnet på en indataparameter som skickas till funktionen när den anropas av utlösaren.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Den andra bindningen i `res`samlingen heter . Den `http` här bindningen`out`är en utdatabindning ( ) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Den andra bindningen i `http` samlingen är `out`av typen riktning `name` `$return` , i vilket fall specialvärdet anger att denna bindning använder funktionens returvärde i stället för att tillhandahålla en indataparameter.

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Den andra bindningen i `res`samlingen heter . Den `http` här bindningen`out`är en utdatabindning ( ) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
I det `msg` här fallet ges till funktionen som ett utdataargument. För `queue` en typ måste du också ange `queueName` namnet på kön i och ange *namnet* på Azure Storage-anslutningen (från *local.settings.json)* i `connection`. 
::: zone-end  
