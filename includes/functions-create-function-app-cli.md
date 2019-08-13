---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949945"
---
## <a name="create-the-local-function-app-project"></a>Skapa det lokala funktionsapprojektet

Kör följande kommando från kommandoraden för att skapa ett funktionsapprojekt i mappen `MyFunctionProj` i den aktuella lokala katalogen. En GitHub-lagringsplats skapas också i `MyFunctionProj`.

```command
func init MyFunctionProj
```

När du uppmanas väljer du en worker-körtid från följande språkval:

+ `dotnet`: skapar ett .NET-klassbiblioteksprojekt (.csproj).
+ `node`: skapar ett Node. js-baserat projekt. Välj antingen `javascript` eller `typescript`. 
+ `python`: för ett python-projekt ska du i stället slutföra [skapa en HTTP-utlöst funktion i Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: för ett PowerShell-projekt fyller du i i stället på [skapa din första PowerShell-funktion i Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


När projektet har skapats använder du följande kommando för att navigera till den nya `MyFunctionProj` projektmappen.

```command
cd MyFunctionProj
```
