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
ms.openlocfilehash: 7f4dced6f82622ba735b1b059f30d88830347fba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625856"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera AKS klustret om du vill använda Azure Dev blanksteg

Öppna ett kommandofönster och ange följande i Azure CLI med hjälp av resursgruppen som innehåller klustret AKS och AKS klusternamnet. Kommandot konfigurerar ditt kluster med stöd för Azure Dev blanksteg.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

