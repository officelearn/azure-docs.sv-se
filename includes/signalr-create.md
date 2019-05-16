---
title: ta med fil
description: ta med fil
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560711"
---
1. Om du vill skapa en Azure SignalR Service-resurs, loggar du först in den [Azure-portalen](https://portal.azure.com). I den övre vänstra sidan, väljer **+ skapa en resurs**. I den **Sök på Marketplace** text anger **SignalR Service**.

2. Välj **SignalR Service** i resultaten och välj **skapa**.

3. På den nya **SignalR** inställningssidan, Lägg till följande inställningar för din nya SignalR-resurs:

    | Namn | Rekommenderat värde | Beskrivning |
    | ---- | ----------------- | ----------- |
    | Resursnamn | *testsignalr* | Ange ett unikt resursnamn för SignalR-resursen. Namnet måste vara en sträng på 1 till 63 tecken och innehålla endast siffror, bokstäver och bindestreck (`-`) tecken. Namnet får inte börja eller sluta med bindestreck och bindestreck i följd är inte giltiga.|
    | Prenumeration | Välj din prenumeration |  Välj den Azure-prenumeration du vill använda när du testar SignalR. Om ditt konto bara har en prenumeration, det väljs automatiskt och **prenumeration** inte visas i listrutan.|
    | Resursgrupp | Skapa en resursgrupp med namnet *SignalRTestResources*| Välj eller skapa en resursgrupp för SignalR-resursen. Den här gruppen är användbart för att ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Hantera Azure-resurser med hjälp av resursgrupper](../articles/azure-resource-manager/resource-group-overview.md). |
    | Location | *Östra USA* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |
    | Prisnivå | *Kostnadsfri* | För närvarande **kostnadsfri** och **Standard** alternativ är tillgängliga. |
    | Fäst på instrumentpanelen | ✔ | Välj den här rutan ha fästas på instrumentpanelen så att det är lättare att hitta resursen. |

4. Välj **Skapa**. Distributionen kan ta några minuter att slutföra.

5. När distributionen är klar, väljer **nycklar** under **inställningar**. Kopiera anslutningssträngen för den primära nyckeln. Du kommer att använda den här strängen senare för att konfigurera din app för att använda Azure SignalR Service-resurs.

    Den här anslutningssträngen har följande format:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
