---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673175"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion (HttpExample) och `--template` argumentet anger funktionens utlösare (http). 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new` skapar en HttpExample.cs-kod fil.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new` skapar en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *function.jspå*.
::: zone-end