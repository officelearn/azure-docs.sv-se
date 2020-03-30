---
title: Så här delar du Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder Azure Dev Spaces för att dela ett utvecklingsutrymme i Azure Kubernetes-tjänsten med andra i ditt team
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474415"
---
# <a name="share-azure-dev-spaces"></a>Dela Azure Dev Spaces

Med Azure Dev Spaces kan du dela ditt utvecklingsutrymme med andra i ditt team. Varje utvecklare kan arbeta i sitt eget utrymme utan rädsla för att bryta andra. Genom att arbeta tillsammans i ett blanksteg kan du också testa kod från på nytt utan att behöva skapa utkast eller simulera beroenden. Mer information finns i guiden [Läs mer om teamutveckling.](../team-development-nodejs.md)

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurera ett utvecklingsutrymme för flera utvecklare

1. Skapa ett utvecklingsutrymme i Azure. Välj [.NET Core och VS-kod](../get-started-netcore.md), [.NET Core och Visual Studio](../get-started-netcore-visualstudio.md)eller [Node.js och VS-kod](../get-started-nodejs.md). Du måste ha ägar- eller deltagaråtkomst till den valda Azure-prenumerationen.
1. Se till att varje gruppmedlem har [rätt behörighet för att komma åt Azure Dev Spaces-styrenheten](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Du kan till exempel konfigurera Azure Dev Space **resursgrupp** för att [ge contributor-åtkomst](/azure/active-directory/role-based-access-control-configure) för varje gruppmedlem. Du kan kontrollera resursgruppen för dev-utrymmet genom att köra det här kommandot:`azds show-context`
1. Be gruppmedlemmarna att **välja dev-utrymme** för att utvecklas i det.
   * **Kommandorad eller VS-kod:** Om du vill visa `azds space list`befintliga Azure Dev Spaces har du åtkomst till: . Så här väljer `azds space select`du ett utvecklingsutrymme: .
   * **Visual Studio IDE:** Öppna ett projekt i Visual Studio, välj **Azure Dev Spaces** i listrutan startinställningar. Välj ett befintligt kluster i dialogrutan som öppnas.

     ![Listrutan Visual Studio-startinställningar](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Nästa steg

Mer information [finns i Läs mer om teamutveckling.](../team-development-nodejs.md)
