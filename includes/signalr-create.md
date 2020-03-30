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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021038"
---
1. Om du vill skapa en Azure SignalR-tjänstresurs loggar du först in på [Azure-portalen](https://portal.azure.com). Välj **+ Skapa en resurs**längst upp till vänster på sidan . Ange **SignalR-tjänst**i textrutan Sök på **Marketplace** .

2. Välj **SignalR-tjänst** i resultatet och välj **Skapa**.

3. På den nya sidan **SignalR-inställningar** lägger du till följande inställningar för den nya SignalR-resursen:

    | Namn | Rekommenderat värde | Beskrivning |
    | ---- | ----------------- | ----------- |
    | Resursnamn | *testsignalr* | Ange ett unikt resursnamn för SignalR-resursen. Namnet måste vara en sträng med 1 till 63 tecken och endast`-`innehålla siffror, bokstäver och bindestreck ( ). Namnet kan inte börja eller sluta med bindestrecket och på varandra följande bindestreckstecken är ogiltiga.|
    | Prenumeration | Välj din prenumeration |  Välj den Azure-prenumeration du vill använda när du testar SignalR. Om ditt konto bara har en prenumeration väljs det automatiskt och listrutan **Prenumeration** visas inte.|
    | Resursgrupp | Skapa en resursgrupp med namnet *SignalRTestResources*| Välj eller skapa en resursgrupp för SignalR-resursen. Den här gruppen är användbar när du vill ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Using resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md) (Hantera dina Azure-resurser med hjälp av resursgrupper). |
    | Location | *USA, östra* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |
    | Prisnivå | *Gratis* | För närvarande är **gratis-** och **standardalternativ** tillgängliga. |
    | Fäst vid instrumentpanelen | ✔ | Markera den här rutan om du vill att resursen ska fästas på instrumentpanelen så att den blir lättare att hitta. |

4. Välj **Skapa**. Distributionen kan ta några minuter att slutföra.

5. När distributionen är klar väljer du **Nycklar** under **INSTÄLLNINGAR**. Kopiera anslutningssträngen för primärnyckeln. Du använder den här strängen senare för att konfigurera din app för att använda Azure SignalR Service-resursen.

    Den här anslutningssträngen har följande format:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
