---
title: "Skapa en statisk HTML-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera en exempelapp och se hur enkelt det är att köra webbappar i App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ba9b9b780da74c44f6314fa289f1d6b8c231dd30
ms.lasthandoff: 05/03/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Skapa en statisk HTML-webbapp i Azure på fem minuter
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Den här snabbstarten hjälper dig att distribuera en enkel HTML+CSS-plats till [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter.

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

Om du vill se vilka värden som kan användas för `--location` använder du Azure CLI-kommandot `az appservice list-locations`.


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
Distribuera en HTML-exempelplats från GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-html-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Bläddra till webbappen
Kör det här kommandot om du vill köra din app live i Azure.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Grattis, din första statiska HTML-plats körs live i Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [CLI-skript för webbappar](app-service-cli-samples.md).

