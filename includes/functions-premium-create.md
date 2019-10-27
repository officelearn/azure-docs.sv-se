---
title: ta med fil
description: ta med fil
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 911db285d1ca885142e8a80345926ce76379e3bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958796"
---
1. Öppna Azure Portal från [https://portal.azure.com](https://portal.azure.com)

1. Välj knappen **skapa en resurs**

    ![Skapa resurs](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Välj **compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Grundläggande inställningar](./media/functions-premium-create/function-app-create-basics.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsapp namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (Skift läges okänsligt), `0-9`och `-`.  |
    |**Publicera**| Programmera | Alternativ för att publicera kod filer eller en Docker-behållare. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**Region**| Önskad region | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    Välj **Nästa: Hosting >** -knappen.

1. Ange följande värd inställningar.

    ![Värd](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../articles/storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **[Projektplan](../articles/azure-functions/functions-scale.md)** | Premium | För typ av abonnemang väljer du **Premium (för hands version)** och väljer standardinställningar för valen *Windows-plan* och *SKU och storlek* . |

    Välj **Nästa: monitoring >** -knappen.

1. Ange följande övervaknings inställningar.

    ![Övervakning](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen kan du ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

    Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. Välj **Skapa** för att etablera och distribuera funktionsappen.