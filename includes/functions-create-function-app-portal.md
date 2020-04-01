---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057194"
---
1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. Välj > **Beräkningsfunktionsapp**på sidan **Nytt** . **Compute**

1. På sidan Grunderna använder du **funktionsappinställningarna** enligt följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Namn på funktionsapp** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken `a-z` är `0-9`(skiftlägesokänsliga), `-`och .  |
    |**Publicera**| Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET Core** för C# och F#-funktioner. |
    |**Version**| Versionsnummer | Välj version av din installerade körning.  |
    |**Regionen**| Önskad region | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    ![Grundläggande inställningar](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Välj **nästa: Hosting**. Ange följande inställningar på sidan **Värd.**

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../articles/storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och kan endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativsystem | Ett operativsystem är förvalt för dig baserat på din runtime stack val, men du kan ändra inställningen om det behövs. |
    | **[Planera](../articles/azure-functions/functions-scale.md)** | **Förbrukning (serverlös)** | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt enligt dina funktioners krav. I den här [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) värd betalar du bara för den tid dina funktioner körs. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |

    ![Värd](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Välj **Nästa: Övervakning**. Ange följande inställningar på sidan **Övervakning.**

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Default | Skapar en Application Insights-resurs med samma *appnamn* i närmaste region som stöds. Genom att expandera den här inställningen kan du ändra namnet på den **nya resursen** eller välja en annan **plats** i en [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

    ![Övervakning](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Välj **Granska + skapa** om du vill granska appkonfigurationsvalen.

1. På sidan **Granska + skapa** granskar du dina inställningar och väljer sedan **Skapa** för att etablera och distribuera funktionsappen.

1. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst på instrumentpanelen**. Om du fäster kan du återgå till den här funktionsappresursen från instrumentpanelen.

    ![Distributionsmeddelande](./media/functions-create-function-app-portal/function-app-create-notification2.png)