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
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera AKS klustret om du vill använda Azure Dev blanksteg

Öppna ett kommandofönster och ange följande Azure CLI-kommandona, med hjälp av resursgruppen som innehåller klustret AKS och klusternamnet AKS:

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
Det första kommandot installerar ett tillägg till Azure CLI för att lägga till stöd för Azure Dev blanksteg och andra konfigurerar ditt kluster med stöd för Azure Dev blanksteg.
