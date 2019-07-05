---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444595"
---
## <a name="create-an-azure-functions-project"></a>Skapa din Functions-projekt med en funktion 

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten. I kommandopaletten, Sök efter och välj `Azure Functions: Create new project...`.

1. Välj en directory-plats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information följer sedan anvisningarna:

    | Uppmaning | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | Välj ett språk för ditt funktionsappsprojekt | C# eller JavaScript | Har stöd för den här artikeln C# och JavaScript. För Python, se [i den här artikeln Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions), och PowerShell, se [den här PowerShell-artikeln](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Välj en mall för ditt projekt första funktion | HTTP-utlösare | Skapa en HTTP-utlöst funktion i den nya funktionsappen. |
    | Ange ett funktionsnamn | HttpTrigger | Tryck på RETUR för att använda standardnamnet. |
    | Ange ett namnområde | My.Functions | (C# endast) C# klassbibliotek måste ha ett namnområde.  |
    | Auktorisationsnivå | Funktion | Kräver en [funktionsnyckel](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) att anropa funktionens HTTP-slutpunkt. |
    | Välj hur du vill öppna ditt projekt | Lägg till arbetsytan | Skapar funktionsappen i den aktuella arbetsytan. |

I Visual Studio Code skapas funktionsappsprojektet på en ny arbetsyta. Det här projektet innehåller konfigurationsfilerna [host.json](../articles/azure-functions/functions-host-json.md) och [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), plus eventuella språkspecifika projektfiler. 

En ny HTTP-utlöst funktion skapas också i mappen HttpTrigger i funktionen app-projekt.