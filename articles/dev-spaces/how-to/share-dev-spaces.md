---
title: Dela Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 2c85625a4f61b701bc1e8b0a4a06f71dc0989ce0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279981"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev Spaces

Med Azure dev Spaces kan du dela ditt dev-utrymme med andra i din grupp. Varje utvecklare kan arbeta i sitt eget utrymme utan att de behöver dela med sig av andra. Även om du arbetar tillsammans i ett enda utrymme kan du testa kod från slut punkt till slut punkt utan att behöva skapa eller simulera beroenden. Mer information finns i [Lär dig mer om Team utvecklings](../team-development-nodejs.md) guiden.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurera ett dev-utrymme för flera utvecklare

1. Skapa ett dev-utrymme i Azure. Välj [.net Core och vs Code](../get-started-netcore.md), [.net Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node. js och vs Code](../get-started-nodejs.md). Du måste ha ägar-eller deltagar åtkomst till den valda Azure-prenumerationen.
1. Konfigurera **resurs gruppen** för Azure dev Space för att [ge deltagar åtkomst](/azure/active-directory/role-based-access-control-configure) för varje grupp medlem. Du kan kontrol lera resurs gruppen för en dev-rymd genom att köra det här kommandot: `azds list-up`
1. Be team medlemmar att **välja ett dev-utrymme** för att utveckla det.
   * **Kommando rad eller vs Code**: om du vill se befintliga Azure dev-utrymmen som du har åtkomst till: `azds space list`. Så här väljer du ett dev-utrymme: `azds space select`.
   * **Visual Studio IDE**: öppna ett projekt i Visual Studio och välj **Azure dev Spaces** i list rutan Start Inställningar. I dialog rutan som öppnas väljer du ett befintligt kluster.

     ![List rutan Start Inställningar för Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Lär dig mer om team utveckling](../team-development-nodejs.md) .
