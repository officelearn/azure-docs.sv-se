---
title: Uppgradera Azure Dev Spaces-verktyg
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Lär dig hur du uppgraderar kommandoradsverktygen för Azure Dev Spaces, tillägget Visual Studio-kod och Visual Studio-tillägget
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265238"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Uppgradera Azure Dev Spaces-verktyg

Om det finns en ny version och du redan använder Azure Dev Spaces kan du behöva uppgradera dina Azure Dev Spaces-klientverktyg.

## <a name="update-the-azure-cli"></a>Uppdatera Azure CLI

När du uppdaterar den senaste Azure CLI får du även den senaste versionen av Dev Spaces CLI-tillägget.

Du behöver inte avinstallera den tidigare versionen, bara hitta lämplig nedladdning på [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Uppdatera dev spaces CLI-tillägg och kommandoradsverktyg

Kör följande kommando:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Uppdatera tillägget VS-kod

När tillägget har installerats uppdateras det automatiskt. Du kan behöva läsa in tillägget igen för att kunna använda de nya funktionerna. Öppna fönstret **Tillägg** i VS-kod, välj Azure **Dev Spaces-tillägg** och välj **Ladda om**.

## <a name="update-the-visual-studio-extension"></a>Uppdatera Visual Studio-tillägget

Precis som med andra tillägg och uppdateringar meddelar Visual Studio dig när det finns en uppdatering tillgänglig för Visual Studio Tools for Kubernetes, som innehåller Azure Dev Spaces. Leta efter en flaggikon längst upp till höger på skärmen.

Om du vill uppdatera verktygen i Visual Studio väljer du menyalternativet **Verktyg > tillägg och uppdateringar** och väljer **Uppdateringar**till vänster . Hitta **Visual Studio-verktyg för Kubernetes** och välj knappen **Uppdatera.**

## <a name="next-steps"></a>Nästa steg

Testa de nya verktygen genom att skapa ett nytt kluster. Prova snabbstarter och självstudier på [Azure Dev Spaces](/azure/dev-spaces).
