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
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187498"
---
1. Gå till [Azure-portalen](https://portal.azure.com).

1. Välj **+ skapa en resurs** på vänster sida och väljer **funktionsapp**.

1. För **värdplan**, Välj **apptjänstplan**och välj sedan **App Service-plan/plats**.

    ![Skapa en funktionsapp](./media/functions-premium-create/create-function-app-resource.png)

1. Välj **Skapa nytt**, typ en **App Service-plan** namn, Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster dina funktioner få åtkomst till och välj sedan **prisnivå**.

    ![Skapa apptjänstplan](./media/functions-premium-create/new-app-service-plan.png)

1. Välj den **EP1** (elastiska Premium) planera och välj sedan **tillämpa**.

    ![Välj premiumplan](./media/functions-premium-create/hosting-plan.png) 

1. Välj **OK** att skapa planen och sedan använda de återstående funktionsappinställningarna som anges i tabellen nedanför bilden. 

    ![Färdig app service-plan](./media/functions-premium-create/create-function-app.png)  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. Du kan också använda det föreslagna värdet. |
    | **OS** | Windows | Linux stöds inte på Premium-prenumerationen. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. Endast de språk som stöds på vald **OS** visas. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights-resurs av samma *appnamn* i den närmaste region som stöds. Genom att expandera den här inställningen kan du ändra den **nytt resursnamn** eller välj en annan **plats** i en [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

1. När dina inställningar verifieras väljer **skapa**.

1. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/functions-premium-create/function-app-create-notification.png)

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **fäst på instrumentpanelen**. Fästa gör det enklare att återgå till den här funktionen app-resursen från instrumentpanelen.