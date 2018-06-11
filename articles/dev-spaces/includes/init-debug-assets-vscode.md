---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 0d0ec1520c91c0c1dcdac4660130917bb63065cc
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825568"
---
### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initiera felsökningstillgångar med VS Code-tillägget
Du måste först konfigurera kodprojektet så att VS Code kommunicerar med vår utvecklarmiljö i Azure. VS Code-tillägget för Azure Dev Spaces har ett hjälpkommando för att konfigurera felsökningskonfigurationen. 

Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Create configuration files for connected development`. 

Då läggs felsökningskonfigurationen för Azure Dev Spaces till under mappen `.vscode`.

![](../media/common/command-palette.png)
