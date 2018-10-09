---
title: Så här skapar du ett Kubernetes-kluster som har aktiverats för Azure Dev lagringsutrymmen med hjälp av Azure Cloud Shell | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 10/04/2018
ms.topic: article
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster som har aktiverats för Azure Dev blanksteg direkt från din webbläsare utan att installera något.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: f10a84a602ce152d5c428525aa50f678b50c8b41
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871863"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Skapa ett Kubernetes-kluster med Azure Cloud Shell

Du kan använda [Azure Cloud Shell](/azure/cloud-shell) att skapa ett kluster för Azure Dev lagringsutrymmen med hjälp av den **prova** knappen från den här sidan. Om du inte är inloggad, följ anvisningarna för att logga in med ett Azure-konto, så Skriv kommandon i Azure Cloud Shell-prompten när den visas.

## <a name="create-the-cluster"></a>Skapa klustret

Först skapa resursgruppen. Använd någon av regionerna som stöds för närvarande (EastUS, CentralUS, västra USA 2, Europavästra, Kanadacentrala eller Kanadaöstra).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
```

Det tar några minuter att skapa klustret.  När du är klar visas utdata i JSON-format. Leta efter `provisioningState` och kontrollera att den har `Succeeded`.

## <a name="next-steps"></a>Nästa steg

Se [Azure Dev blanksteg](/azure/dev-spaces/) länkar till fullständig självstudier.