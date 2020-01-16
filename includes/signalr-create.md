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
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021038"
---
1. Om du vill skapa en Azure SignalR service-resurs måste du först logga in på [Azure Portal](https://portal.azure.com). I den övre vänstra sidan av sidan väljer du **+ skapa en resurs**. I text rutan **Sök på Marketplace anger du** **SignalR-tjänsten**.

2. Välj **signal tjänst** i resultaten och välj **skapa**.

3. På sidan nya **signal** inställningar lägger du till följande inställningar för din nya signalerar-resurs:

    | Namn | Rekommenderat värde | Beskrivning |
    | ---- | ----------------- | ----------- |
    | Resursnamn | *testsignalr* | Ange ett unikt resursnamn för SignalR-resursen. Namnet måste vara en sträng med 1 till 63 tecken och får bara innehålla siffror, bokstäver och bindestreck (`-`). Namnet får inte börja eller sluta med bindestrecket, och de efterföljande bindestrecken är inte giltiga.|
    | Prenumeration | Välj din prenumeration |  Välj den Azure-prenumeration du vill använda när du testar SignalR. Om ditt konto bara har en prenumeration väljs den automatiskt och list rutan **prenumeration** visas inte.|
    | Resursgrupp | Skapa en resurs grupp med namnet *SignalRTestResources*| Välj eller skapa en resursgrupp för SignalR-resursen. Den här gruppen är användbar för att organisera flera resurser som du kanske vill ta bort samtidigt genom att ta bort resurs gruppen. Mer information finns i [Hantera Azure-resurser med hjälp av resursgrupper](../articles/azure-resource-manager/management/overview.md). |
    | Location | *Östra USA* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |
    | Prisnivå | *Kostnadsfri* | För närvarande finns det **kostnads fria** och **standard** alternativ. |
    | Fäst vid instrumentpanelen | ✔ | Markera den här rutan om du vill att resursen ska fästa på instrument panelen så att den blir lättare att hitta. |

4. Välj **Skapa**. Det kan ta några minuter att slutföra distributionen.

5. När distributionen är klar väljer du **nycklar** under **Inställningar**. Kopiera anslutnings strängen för den primära nyckeln. Du kommer att använda den här strängen senare för att konfigurera din app att använda Azure SignalR service-resursen.

    Den här anslutningssträngen har följande format:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
