---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1cecafb013b5d22557961e98935fc9a2fee2a79b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199518"
---
1. Välj den **skapa en resurs** knappen hittades på det övre vänstra hörnet i Azure-portalen, sedan väljer **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Definiera nya funktionsappinställningar](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **OS** | Windows | Serverlösa värdtjänster i Linux är för närvarande i förhandsversion. Mer information finns i [i den här artikeln för att tänka på](https://aka.ms/funclinux).|
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** | Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. För den här typen av [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) värdtjänster betalar du bara för den tid som dina funktioner körs. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |
    | **Plats** | Västra Europa | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights-resurs av samma *appnamn* i den närmaste region som stöds. Genom att expandera den här inställningen kan du ändra den **nytt resursnamn** eller välj en annan **plats** i en [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

3. Välj **Skapa** för att etablera och distribuera funktionsappen.

4. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Välj **Gå till resurs** att visa den nya funktionsappen.
