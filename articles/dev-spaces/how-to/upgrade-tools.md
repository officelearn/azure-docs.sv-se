---
title: Uppgradera Azure dev Spaces-verktyg
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 6ad28a5da05cf77cfaa834ed9752daefeb5f4fdb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279977"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Uppgradera Azure dev Spaces-verktyg

Om det finns en ny version och du redan använder Azure dev Spaces kan du behöva uppgradera dina klient verktyg för Azure dev Spaces.

## <a name="update-the-azure-cli"></a>Uppdatera Azure CLI

När du uppdaterar den senaste versionen av Azure CLI får du också den senaste versionen av dev Spaces CLI-tillägget.

Du behöver inte avinstallera den tidigare versionen. du hittar bara lämplig nedladdning på [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Uppdatera CLI-tillägget för dev Spaces och kommando rads verktygen

Kör följande kommando:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Uppdatera VS Code-tillägget

När du har installerat uppdateras tillägget automatiskt. Du kan behöva läsa in tillägget igen för att använda de nya funktionerna. I VS Code öppnar du fönstret **tillägg** , väljer tillägget **Azure dev Spaces** och väljer **Läs in igen**.

## <a name="update-the-visual-studio-extension"></a>Uppdatera Visual Studio-tillägget

Precis som med andra tillägg och uppdateringar meddelar Visual Studio dig när det finns en tillgänglig uppdatering för Visual Studio-verktygen för Kubernetes, som innehåller Azure dev Spaces. Leta efter en flagg ikon längst upp till höger på skärmen.

Om du vill uppdatera verktygen i Visual Studio väljer du meny alternativet **verktyg > tillägg och uppdateringar** och väljer sedan **uppdateringar**på vänster sida. Hitta **Visual Studio Tools för Kubernetes** och klicka på knappen **Uppdatera** .

## <a name="next-steps"></a>Nästa steg

Testa de nya verktygen genom att skapa ett nytt kluster. Prova snabb starter och självstudier på [Azure dev Spaces](/azure/dev-spaces).