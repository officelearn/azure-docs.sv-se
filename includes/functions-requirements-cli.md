---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 9e9f9c71701ceb1c76bc162f22e166b4565e731b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86062690"
---
## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller en senare 2. x-version.
::: zone-end  
::: zone pivot="programming-language-python"
+ Den Azure Functions Core Tools version som motsvarar din installerade python-version:

   | Python-version | Core tools-version |
   | -------------- | ------------------ |
   | Python 3,8     | [version 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Version 2.7.1846 eller en senare version](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktiva LTS-och UNDERHÅLLs LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bitars)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.Net Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), version 8.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.
::: zone-end
