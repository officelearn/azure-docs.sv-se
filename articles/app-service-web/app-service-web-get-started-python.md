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
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: dbacf368684bf23f63d997941ac68cf0bc1d9a40
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>Skapa din första Python-webbapp i Azure på fem minuter
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Den här snabbstarten hjälper dig att distribuera din första Python-webbapp via [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter.

Innan du startar den här snabbstarten ser du till att [Azure CLI är installerat](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) på datorn.

## <a name="create-a-python-web-app-in-azure"></a>Skapa en Python-webbapp i Azure
2. Logga in på Azure genom att köra `az login` och följ anvisningarna på skärmen.
   
    ```azurecli
    az login
    ```
   
3. Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md). Här placerar du alla Azure-resurser som du vill hantera tillsammans, t.ex. webbappen och dess serverdel för SQL Database.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Om du vill se vilka värden som kan användas för `---location` använder du Azure CLI-kommandot `az appservice list-locations`.

3. Skapa en ”kostnadsfri” [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Skapa en ny webbapp med ett unikt namn i `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Distribuera en Python-exempelapp från GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
    ```

5. Kör det här kommandot om du vill köra din app live i Azure.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Grattis, din första Python-webbapp körs live i Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [CLI-skript för webbappar](app-service-cli-samples.md).

