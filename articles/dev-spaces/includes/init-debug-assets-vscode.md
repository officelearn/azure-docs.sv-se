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
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initiera debug tillgångar med filnamnstillägget VS-kod
Du måste först konfigurera projektet kod så att VS koden kommer att kommunicera med vår utvecklingsmiljö i Azure. VS kod tillägget för Azure Dev blanksteg innehåller ett helper-kommando för att konfigurera debug-konfiguration. 

Öppna den **kommandot paletten** (med hjälp av den **visa | Kommandot paletten** menyn), och använder automatisk komplettering Skriv och väljer det här kommandot: `Azure Dev Spaces: Create configuration files for connected development`. 

Detta lägger till debug-konfigurationen för Azure Dev blanksteg under den `.vscode` mapp.

![](../media/common/command-palette.png)

> [!Important]
> På grund av ett programfel Stäng och öppna VS koden igen innan du fortsätter.