---
title: Skriptexempel för Azure CLI – Skapa en app med kontinuerlig distribution från GitHub | Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa en app och distribuera kontinuerligt från GitHub
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 02853186a1f28d8c6db6b5421c6ebba0640461cb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "71057886"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Skapa en App Service-app med kontinuerlig distribution från GitHub med CLI

Det här exempelskriptet skapar en app i App Service med dess relaterade resurser och ställer sedan in kontinuerlig distribution från en GitHub-lagringsplats. Mer information om GitHub-distribution utan kontinuerlig distribution finns i [Skapa en app och distribuera kod från GitHub](cli-deploy-github.md). För det här exemplet behöver du följande:

* En GitHub-lagringsplats med programkod som du har administratörsbehörighet för. Om du vill hämta automatiska versioner strukturerar du lagrings platsen enligt tabellen [Förbered din lagrings plats](../deploy-continuous-deployment.md#prepare-your-repository) .
* En [personlig åtkomsttoken (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) för ditt GitHub-konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Skapar en App Service-plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Skapar en App Service-app. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Kopplar en Azure Service-app till en Git- eller Mercurial-lagringsplats. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
