---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673124"
---
## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller en senare 2.x-version.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 och 3.7 kräver [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller en senare 2.x-version. Python 3.8 kräver [version 3.x](../articles/azure-functions/functions-run-local.md#v2) av core tools.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.0.76 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js,](https://nodejs.org/)Active LTS och Underhåll LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bitars),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bitars),](https://www.python.org/downloads/release/python-375/) [Python 3.6 (64-bitars)](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), version 8.

+ [Apache Maven](https://maven.apache.org), version 3.0 eller högre.

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.
::: zone-end