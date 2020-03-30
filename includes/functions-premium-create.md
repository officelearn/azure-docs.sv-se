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
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021277"
---
1. Öppna Azure-portalen från[https://portal.azure.com](https://portal.azure.com)

1. Välj knappen **Skapa en resurs**

    ![Skapa resurs](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Välj**Appen Beräkningsfunktion** **Compute** > .

    ![Skapa en funktionsapp i Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Grundläggande inställningar](./media/functions-premium-create/function-app-create-basics.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Namn på funktionsapp** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken `a-z` är `0-9`(skiftlägesokänsliga), `-`och .  |
    |**Publicera**| Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**Regionen**| Önskad region | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    Välj knappen **Nästa: Hosting >.**

1. Ange följande värdinställningar.

    ![Värd](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../articles/storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagringskonto](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativsystem | Ett operativsystem är förvalt för dig baserat på din runtime stack val, men du kan ändra inställningen om det behövs. |
    | **[Plan](../articles/azure-functions/functions-scale.md)** | Premium | För Plantyp väljer du **Premium (Förhandsgranska)** och väljer standardvärden för *valen Windows-abonnemang* och *Sku och storlek.* |

    Välj knappen **Nästa: Övervakning >.**

1. Ange följande övervakningsinställningar.

    ![Övervakning](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Default | Skapar en Application Insights-resurs med samma *appnamn* i närmaste region som stöds. Genom att expandera den här inställningen kan du ändra namnet på den **nya resursen** eller välja en annan **plats** i en [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

    Välj **Granska + Skapa** om du vill granska appkonfigurationsvalen.

1. Välj **Skapa** för att etablera och distribuera funktionsappen.