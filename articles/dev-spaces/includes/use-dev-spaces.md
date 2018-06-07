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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823188"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera AKS klustret om du vill använda Azure Dev blanksteg

Öppna ett kommandofönster och ange följande i Azure CLI med hjälp av resursgruppen som innehåller klustret AKS och AKS klusternamnet. Kommandot konfigurerar ditt kluster med stöd för Azure Dev blanksteg.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

