---
title: ta med fil
description: ta med fil
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 8b5be0a438d9c5bb1fd0596368327c53a2d6c31f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396748"
---
> [!NOTE]
> Kommandot `az webapp up` utför följande åtgärder:
>
>- Skapa en standardprincip för [resursgrupp](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Skapa en standardprincip för [apptjänstplan](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Skapa en app](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) med det angivna namnet.
>
>- [Zip-distribuera](https://docs.microsoft.com/azure/app-service/deploy-zip) filer från den aktuella arbetskatalogen till appen.
>
