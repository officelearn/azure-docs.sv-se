---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 08/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f0bcb2e7f99689d31be610f1523d2c57e332deb7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049733"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

2. Välj **Azure-funktionsapp**, välj **Skapa ny** och välj sedan **Publicera**.

    ![Välj ett publiceringsmål](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

    När du aktiverar **Kör från ZIP** körs funktionsappen i Azure direkt från distributionspaketet. Mer information finns i [Kör dina Azure-funktioner från en paketfil](../articles/azure-functions/run-functions-from-deployment-package.md).

3. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto**.

4. I dialogrutan **Skapa App Service** använder du **värdinställningarna** som anges i tabellen under bilden:

    ![Dialogrutan Skapa App Service](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[App Service-plan](../articles/azure-functions/functions-scale.md)** | Förbrukningsplan | Välj **Förbrukning** under **Storlek** efter att du klickat på **Nytt** för att skapa en serverlös plan. Välj också en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som används av dina funktioner. När du kör i en plan som inte är **Förbrukning** behöver du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |
    | **[Lagringskonto](../articles/storage/common/storage-quickstart-create-account.md)** | Lagringskonto för generell användning | Azure Functions-körmiljön kräver ett Azure Storage-konto. Klicka på **Nytt** om du vill skapa ett lagringskonto för generell användning. Du kan också använda ett befintligt konto som uppfyller [kraven för ett lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Klicka på **Skapa** för att skapa en funktionsapp och relaterade resurser i Azure med dessa inställningar och distribuera koden för funktionsprojektet. 

6. När distributionen är klar skriver du ned värdet för **Plats-URL**. Det här är funktionsappens adress i Azure.

    ![Meddelande som anger att publiceringen lyckades](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
