---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201950"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Med undantag för HTTP- och timerutlösare implementeras bindningar som tilläggspaket. Kör följande [dotnet add-paketkommando](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att lägga till paketet för lagringstillägg i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nu kan du lägga till lagringsutdatabindningen i projektet.  
::: zone-end  