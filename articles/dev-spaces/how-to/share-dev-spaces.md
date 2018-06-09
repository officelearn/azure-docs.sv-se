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
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: ca644cc412ad0c8f0e2a0781d9419fba58ed8d12
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247712"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev blanksteg

Du kan dela din dev sida med andra i din grupp med Azure Dev blanksteg. Varje utvecklare kan arbeta i sina egna utrymme utan fruktan för att dela med andra. Dessutom kan arbetar tillsammans i ett steg du testa koden slutpunkt till slutpunkt utan att behöva skapa mocks eller simulera beroenden. Finns det [Lär dig mer om utveckling i grupp](../get-started-nodejs.md#learn-about-team-development) mer information.

Konfigurera ett dev utrymme för flera utvecklare:
1. Skapa ett Dev utrymme i Azure. Välj [.NET Core och VS kod](../get-started-netcore.md), [.NET Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node.js och VS kod](../get-started-nodejs.md). Du måste ha ägare eller deltagare åtkomst till den valda Azure-prenumerationen.
1. Konfigurera Azure Dev utrymme **resursgruppen** till [deltagare åtkomstbehörighet](/azure/active-directory/role-based-access-control-configure) för varje medlem i gruppen. Du kan kontrollera dev kan resursgruppen genom att köra det här kommandot: `azds list`
1. Uppmana gruppmedlemmarna att **Markera dev utrymme** för att utveckla i den.
     * **Kommandorad eller VS kod**: befintliga Azure Dev utrymmen du har åtkomst till: `azds space list`. Att välja ett dev utrymme: `azds space select`.
     * **Visual Studio IDE**: öppna ett projekt i Visual Studio, markera **Azure Dev blanksteg** från Starta inställningar listrutan. I den dialogruta som öppnas väljer du ett befintligt kluster.

![Visual Studio starta inställningar nedrullningsbara](../media/get-started-netcore-visualstudio/LaunchSettings.png)