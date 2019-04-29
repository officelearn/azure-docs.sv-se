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
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690322"
---
1. Om du vill skapa en ny Azure SignalR Service-resurs loggar du först in på [Azure Portal](https://portal.azure.com). Klicka på **+ Skapa en resurs** uppe till vänster på sidan. I textrutan **Sök på marknadsplatsen** skriver du **SignalR Service** och trycker på **Enter**.

2. Klicka på **SignalR Service** i resultatet och klicka på **Skapa**.

3. På den nya inställningssidan för **SignalR** lägger du till följande inställningar för din nya SignalR-resurs:

    | Namn | Rekommenderat värde | Beskrivning |
    | ---- | ----------------- | ----------- |
    | Resursnamn | *testsignalr* | Ange ett unikt resursnamn för SignalR-resursen. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.|
    | Prenumeration | Välj din prenumeration |  Välj den Azure-prenumeration du vill använda när du testar SignalR. Om ditt konto bara har en prenumeration väljs den automatiskt och listrutan **Prenumeration** visas inte.|
    | Resursgrupp | Skapa en ny resursgrupp med namnet *SignalRTestResources*.| Välj eller skapa en resursgrupp för SignalR-resursen. Den här gruppen är användbar när du vill ta bort flera resurser samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../articles/azure-resource-manager/resource-group-overview.md). |
    | Location | *Östra USA* | Använd **Plats** till att ange den geografiska plats där SignalR-resursen finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |
    | Prisnivå | *Kostnadsfri* | För närvarande är alternativen **Kostnadsfri** och **Standard** tillgängliga. |
    | Fäst vid instrumentpanelen | ✔ | Markera den här kryssrutan om du vill fästa resursen på instrumentpanelen så att du enkelt kan hitta den. |

4. Klicka på **Skapa**. Det kan ta några minuter att slutföra distributionen.

5. När distributionen är färdig klickar du på **Nycklar** under **INSTÄLLNINGAR**. Kopiera anslutningssträngen för din primära nyckel. Du kommer att använda den senare när du ska konfigurera appen så att Azure SignalR Service-resursen används.

    Den här anslutningssträngen har följande format:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
