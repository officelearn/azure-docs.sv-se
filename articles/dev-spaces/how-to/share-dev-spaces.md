---
title: Dela Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder Azure dev Spaces för att dela ett dev-utrymme i Azure Kubernetes service med andra i din grupp
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79474415"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev Spaces

Med Azure dev Spaces kan du dela ditt dev-utrymme med andra i din grupp. Varje utvecklare kan arbeta i sitt eget utrymme utan att de behöver dela med sig av andra. Även om du arbetar tillsammans i ett enda utrymme kan du testa kod från slut punkt till slut punkt utan att behöva skapa eller simulera beroenden. Mer information finns i [Lär dig mer om Team utvecklings](../team-development-nodejs.md) guiden.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurera ett dev-utrymme för flera utvecklare

1. Skapa ett dev-utrymme i Azure. Välj [.net Core och vs Code](../get-started-netcore.md), [.net Core och Visual Studio](../get-started-netcore-visualstudio.md), eller [Node.js och vs Code](../get-started-nodejs.md). Du måste ha ägar-eller deltagar åtkomst till den valda Azure-prenumerationen.
1. Se till att varje grupp medlem har de [behörigheter som krävs för att komma åt Azure dev Spaces-kontrollanten](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Du kan till exempel konfigurera Azure dev Spaces **resurs grupp** för att [ge deltagar åtkomst](/azure/active-directory/role-based-access-control-configure) för varje grupp medlem. Du kan kontrol lera resurs gruppen för en dev-rymd genom att köra det här kommandot:`azds show-context`
1. Be team medlemmar att **välja ett dev-utrymme** för att utveckla det.
   * **Kommando rad eller vs Code**: för att se befintliga Azure dev-Spaces-platser har du till gång till: `azds space list` . Så här väljer du ett dev-utrymme: `azds space select` .
   * **Visual Studio IDE**: öppna ett projekt i Visual Studio och välj **Azure dev Spaces** i list rutan Start Inställningar. I dialog rutan som öppnas väljer du ett befintligt kluster.

     ![List rutan Start Inställningar för Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Lär dig mer om team utveckling](../team-development-nodejs.md) .
