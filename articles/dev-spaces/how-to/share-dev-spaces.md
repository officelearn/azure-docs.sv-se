---
title: Dela Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder Azure dev Spaces för att dela ett dev-utrymme i Azure Kubernetes service med andra i din grupp
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 3cef7cd6a5113401cb1df3dd6c76e4dea3a7524d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229303"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev Spaces

Med Azure dev Spaces kan du dela ditt dev-utrymme med andra i din grupp. Varje utvecklare kan arbeta i sitt eget utrymme utan att de behöver dela med sig av andra. Även om du arbetar tillsammans i ett enda utrymme kan du testa kod från slut punkt till slut punkt utan att behöva skapa eller simulera beroenden. Mer information finns i [Lär dig mer om Team utvecklings](../team-development-nodejs.md) guiden.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurera ett dev-utrymme för flera utvecklare

1. Skapa ett dev-utrymme i Azure. Välj [.net Core och vs Code](../get-started-netcore.md), [.net Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node.js och vs Code](../get-started-nodejs.md). Du måste ha ägar-eller deltagar åtkomst till den valda Azure-prenumerationen.
1. Se till att varje grupp medlem har de [behörigheter som krävs för att komma åt Azure dev Spaces-kontrollanten](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Du kan till exempel konfigurera Azure dev Spaces **resurs grupp** för att [ge deltagar åtkomst](../../role-based-access-control/role-assignments-portal.md) för varje grupp medlem. Du kan kontrol lera resurs gruppen för en dev-rymd genom att köra det här kommandot:`azds show-context`
1. Be team medlemmar att **välja ett dev-utrymme** för att utveckla det.
   * **Kommando rad eller vs Code**: för att se befintliga Azure dev-Spaces-platser har du till gång till: `azds space list` . Så här väljer du ett dev-utrymme: `azds space select` .
   * **Visual Studio IDE**: öppna ett projekt i Visual Studio och välj **Azure dev Spaces** i list rutan Start Inställningar. I dialog rutan som öppnas väljer du ett befintligt kluster.

     ![List rutan Start Inställningar för Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Lär dig mer om team utveckling](../team-development-nodejs.md) .
