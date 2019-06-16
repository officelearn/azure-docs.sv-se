---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 894ca0e78dfb75dffc124d3d25aa7a8e72adf627
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065544"
---
## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. I Visual Studio Code, väljer du den Azure logotypen som ska visas i **Azure: Functions** område och välj sedan ikonen Skapa nytt projekt.

    ![Skapa ett funktionsapprojekt](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. Välj en plats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > Den här artikeln har utformats för att genomföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Välj språk för ditt funktionsappsprojekt. I den här artikeln används JavaScript.
    ![Välj projektspråk](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. Välj en mall för den första funktionen för ditt projekt. Ange ett namn för din funktion.
    ![Välj första funktion](./media/functions-create-function-app-vs-code/create-function-app-project-first-function.png)

1. Välj **Lägg till på arbetsyta** när du uppmanas att göra det.

I Visual Studio Code skapas funktionsappsprojektet på en ny arbetsyta. Det här projektet innehåller konfigurationsfilerna [host.json](../articles/azure-functions/functions-host-json.md) och [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), plus eventuella språkspecifika projektfiler. Du får också en ny Git-lagringsplats i projektmappen.