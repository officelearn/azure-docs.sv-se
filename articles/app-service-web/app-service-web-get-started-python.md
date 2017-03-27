---
title: "Skapa din första Python-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera en Python-exempelapp och se hur enkelt det är att köra webbappar i App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 53d936366de30d3d149b170c4ef52574c65dcbc8
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>Skapa din första Python-webbapp i Azure på fem minuter
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Den här snabbstarten hjälper dig att distribuera din första Python-webbapp via [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter.

Kontrollera att Azure CLI har installerats innan du börjar. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure genom att köra `az login` och följ anvisningarna på skärmen.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Skapa en resursgrupp   
Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md). Här placerar du alla Azure-resurser som du vill hantera tillsammans, t.ex. webbappen och dess serverdel för SQL Database.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Om du vill se vilka värden som kan användas för `---location` använder du Azure CLI-kommandot `az appservice list-locations`.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan
Skapa en ”kostnadsfri” [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Skapa en webbapp
Skapa en webbapp med ett unikt namn i `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>Distribuera exempelprogram
Distribuera en Python-exempelapp från GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Bläddra till webbappen
Kör det här kommandot om du vill köra din app live i Azure.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Grattis, din första Python-webbapp körs live i Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [CLI-skript för webbappar](app-service-cli-samples.md).

