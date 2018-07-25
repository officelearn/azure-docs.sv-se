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
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991035"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera ditt AKS-kluster för att använda Azure Dev Spaces

Öppna ett kommandofönster och ange följande Azure CLI-kommando med hjälp av den resursgrupp som innehåller ditt AKS-kluster och AKS-klusternamnet. Kommandot konfigurerar ditt kluster med stöd för Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

