---
title: Uppgradera Azure Dev blanksteg verktyg
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
ms.technology: azds-kubernetes
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: b56c6069c255984e480b3e6b0e673d89e6c0ee3d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194139"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Uppgradera Azure Dev blanksteg verktyg

Om det finns en ny version och du redan använder Azure Dev blanksteg, kan du behöva uppgradera din Azure Dev blanksteg klientverktyg.

## <a name="update-the-azure-cli"></a>Uppdatera Azure CLI

När du uppdaterar senaste Azure CLI kan få du också den senaste versionen av Dev blanksteg CLI-tillägg.

Du behöver inte avinstallera den tidigare versionen, bara hitta lämpliga ned från [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Uppdatera Dev blanksteg CLI-tillägg och kommandoradsverktyg

Kör följande kommando:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Uppdatera VS Code-tillägg

När du har installerat uppdateras tillägget automatiskt. Du kan behöva läsa in tillägget för att använda de nya funktionerna. I VS Code, öppna den **tillägg** fönstret väljer du den **Azure Dev blanksteg** tillägg, och välj **Reload**.

## <a name="update-the-visual-studio-extension"></a>Uppdatera Visual Studio-tillägget

Som med andra tillägg och uppdateringar, meddelar Visual Studio dig när en uppdatering är tillgänglig för Visual Studio Tools för Kubernetes, som innehåller Azure Dev blanksteg. Leta efter en flaggikon uppe till höger på skärmen.

Om du vill uppdatera verktygen i Visual Studio, Välj den **Verktyg > tillägg och uppdateringar** objekt och till vänster, Välj **uppdaterar**. Hitta **Visual Studio Tools for Kubernetes** och välj den **uppdatering** knappen.

## <a name="next-steps"></a>Nästa steg

Testa de nya verktygen genom att skapa ett nytt kluster. Försök snabbstarter och självstudier på [Azure Dev blanksteg](/azure/dev-spaces).

> [!WARNING]
> Azure Dev utrymmen på befintliga kluster kommer inte omedelbart korrigeras, så om du vill vara säker på att du använder den senaste versionen på alla dina Azure-distributioner, skapa ett nytt kluster när du har uppgraderat verktygen.
