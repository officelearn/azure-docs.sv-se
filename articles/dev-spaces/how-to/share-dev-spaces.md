---
title: Hur du delar Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Snabb utveckling av Kubernetes med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev blanksteg

Du kan dela din dev sida med andra i din grupp med Azure Dev blanksteg. Varje utvecklare kan arbeta i sina egna utrymme utan fruktan för att dela med andra. Dessutom kan arbetar tillsammans i ett steg du testa koden slutpunkt till slutpunkt utan att behöva skapa mocks eller simulera beroenden. Finns det [Lär dig mer om utveckling i grupp](../get-started-nodejs.md#learn-about-team-development) mer information.

Konfigurera en miljö för flera utvecklare:
1. Skapa ett Dev utrymme i Azure. Välj [.NET Core och VS kod](../get-started-netcore.md), [.NET Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node.js och VS kod](../get-started-nodejs.md). Du måste ha ägare eller deltagare åtkomst till den valda Azure-prenumerationen.
1. Konfigurera Azure Dev utrymme **resursgruppen** till [deltagare åtkomstbehörighet](/azure/active-directory/role-based-access-control-configure) för varje medlem i gruppen. Du kan kontrollera resursgrupp i en miljö genom att köra det här kommandot: `azds resource list`
1. Uppmana gruppmedlemmarna att **Välj miljö** för att utveckla i den.
     * **Kommandorad eller VS kod**: se befintliga Azure Dev Spacess du har åtkomst till: `azds resource list`. Att välja en miljö: `azds resource select`.
     * **Visual Studio IDE**: öppna ett projekt i Visual Studio, markera **Azure Dev blanksteg** från Starta inställningar listrutan. I den dialogruta som öppnas väljer du en befintlig utvecklingsmiljö.

![Visual Studio starta inställningar nedrullningsbara](../media/get-started-netcore-visualstudio/LaunchSettings.png)