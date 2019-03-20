---
title: Hur du delar Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
ms.openlocfilehash: 62d4affa5ef49de7600f9ccc800ea6bf83e4bd49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099584"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev blanksteg

Med Azure Dev blanksteg, kan du dela ditt dev-adressutrymme med andra i din grupp. Varje utvecklare kan arbeta i deras utrymme utan att behöva betala större andra. Dessutom kan arbetar tillsammans i ett steg du testa koden slutpunkt till slutpunkt utan att behöva skapa mocks eller simulera beroenden. Se den [Lär dig mer om utveckling i grupp](../team-development-nodejs.md) mer information.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurera ett dev-utrymme för flera utvecklare

1. Skapa ett utrymme för utveckling i Azure. Välj [.NET Core och VS Code](../get-started-netcore.md), [.NET Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node.js och VS Code](../get-started-nodejs.md). Du måste ha ägare eller deltagare åtkomst till den valda Azure-prenumerationen.
1. Konfigurera Azure Dev utrymme **resursgrupp** till [bevilja deltagaråtkomst](/azure/active-directory/role-based-access-control-configure) för varje medlem i gruppen. Du kan kontrollera en dev-utrymme resursgrupp genom att köra det här kommandot: `azds list-up`
1. Be teammedlemmar möjlighet att **väljer området dev** för att utveckla i den.
   * **Från kommandoraden eller i VS Code**: Befintliga Azure Dev utrymmen du har åtkomst till: `azds space list`. Att välja en dev-utrymme: `azds space select`.
   * **Visual Studio IDE**: Öppna ett projekt i Visual Studio, markera **Azure Dev blanksteg** från Start Inställningar listrutan. I dialogrutan som öppnas väljer du ett befintligt kluster.

     ![Visual Studio starta inställningar listrutan](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Nästa steg

Se [Lär dig mer om utveckling i grupp](../team-development-nodejs.md) för mer information.
