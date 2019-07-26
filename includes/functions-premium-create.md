---
title: ta med fil
description: ta med fil
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443964"
---
1. Gå till [Azure-portalen](https://portal.azure.com).

1. Välj **+ skapa en resurs** till vänster och välj sedan **Function app**.

1. För **värd plan**väljer du **App Service plan**och väljer sedan **App Service plan/plats**.

    ![Skapa en funktionsapp](./media/functions-premium-create/create-function-app-resource.png)

1. Välj **Skapa ny**, ange ett **App Service plan** namn, Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som du har åtkomst till och välj sedan **pris nivå**.

    ![Skapa apptjänstplan](./media/functions-premium-create/new-app-service-plan.png)

1. Välj **EP1** -planen (elastisk Premium) och välj sedan **Använd**.

    ![Välj Premium-plan](./media/functions-premium-create/hosting-plan.png) 

1. Välj **OK** för att skapa planen och Använd sedan de återstående funktionerna i appen som anges i tabellen nedanför bilden. 

    ![Slut på App Service-plan](./media/functions-premium-create/create-function-app.png)  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. Du kan också använda det föreslagna värdet. |
    | **OS** | Önskat OS | Både Linux och Windows stöds i Premium planen. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. Endast språk som stöds på det valda **operativ systemet** visas. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen kan du ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

1. När inställningarna har verifierats väljer du **skapa**.

1. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-premium-create/function-app-create-notification.png)

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.