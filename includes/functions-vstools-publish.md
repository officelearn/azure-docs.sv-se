---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c513a76537eb5b28e85e6289a610e318ab790d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050737"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

2. Välj **Azure-funktionsapp**, välj **Skapa ny** och välj sedan **Publicera**.

    ![Välj ett publiceringsmål](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    När du klickar på **kör en paketfil (rekommenderas) från**, funktionsappen ska distribueras med [Zip distribuera](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) med [kör från paketet](../articles/azure-functions/run-functions-from-deployment-package.md) läge har aktiverats. Detta är det rekommenderade sättet för att köra dina funktioner och resulterar i bättre prestanda.

    >[!CAUTION]
    >När du väljer **Välj befintliga** skrivs alla filer i den befintliga funktionsappen i Azure över från det lokala projektet. Använd bara det här alternativet när du publicerar igen i en befintlig funktionsapp.

3. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto**.

4. I dialogrutan **Skapa App Service** använder du **värdinställningarna** som anges i tabellen under bilden:

    ![Dialogrutan Skapa App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Skapa en ny resursgrupp genom att välja **Ny**.|
    | **[App Service-plan](../articles/azure-functions/functions-scale.md)** | Förbrukningsplan | Välj **Förbrukning** under **Storlek** efter att du klickat på **Nytt** för att skapa en serverlös plan. Välj också en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som används av dina funktioner. När du kör i en plan som inte är **Förbrukning** behöver du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |
    | **[Lagringskonto](../articles/storage/common/storage-quickstart-create-account.md)** | Allmänt lagringskonto | Azure Functions-körmiljön kräver ett Azure Storage-konto. Klicka på **New** att skapa ett allmänt lagringskonto. Du kan också använda ett befintligt konto som uppfyller [kraven för ett lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Klicka på **Skapa** för att skapa en funktionsapp och relaterade resurser i Azure med dessa inställningar och distribuera koden för funktionsprojektet. 

6. När distributionen är klar skriver du ned värdet för **Plats-URL**. Det här är funktionsappens adress i Azure.

    ![Meddelande som anger att publiceringen lyckades](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
