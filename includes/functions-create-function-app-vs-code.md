---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 79dbee33928fbc7560d0ea27be3af25cc510e996
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641899"
---
## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. Välj Azure-logotypen i Visual Studio Code så att området **Azure: Funktioner** visas och välj sedan ikonen Skapa nytt projekt.

    ![Skapa ett funktionsapprojekt](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. Välj en plats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > Den här artikeln har utformats för att genomföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Välj språk för ditt funktionsappsprojekt. I den här artikeln används JavaScript.
    ![Välj projektspråk](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. Välj **Lägg till på arbetsyta** när du uppmanas att göra det.

I Visual Studio Code skapas funktionsappsprojektet på en ny arbetsyta. Det här projektet innehåller konfigurationsfilerna [host.json](../articles/azure-functions/functions-host-json.md) och [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), plus eventuella språkspecifika projektfiler. Du får också en ny Git-lagringsplats i projektmappen.