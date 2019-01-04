---
title: Konfigurera PremiumV2 - nivå i Azure App Service | Microsoft Docs
description: Lär dig hur du bättre prestanda för dina webb-, mobil- och API-app i Azure App Service genom att skala till den nya prisnivån PremiumV2.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 991748c33eae607e97b3916a913b111af7b3b75a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714482"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurera PremiumV2-nivå för Azure App Service

Den nya **PremiumV2** prisnivån får du snabbare processorer, SSD-lagring och Double-värden minne-till-kärna-förhållande på befintliga prisnivåer. Du kan spara pengar genom att köra dina appar på färre instanser med fördelen prestanda. I den här artikeln får du lära dig hur du skapar en app i **PremiumV2** nivån eller skala upp en app på **PremiumV2** nivå.

## <a name="prerequisites"></a>Förutsättningar

Skala upp en app på **PremiumV2**, måste du ha en Azure App Service-app som körs i en prisnivå som är lägre än **PremiumV2**, och appen måste köras i en App Service-distribution som har stöd för PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2-tillgänglighet

Den **PremiumV2** nivån är tillgänglig för App Service på både _Windows_ samt _Linux_.

**PremiumV2** är tillgängliga i de flesta Azure-regioner. För att se om den är tillgänglig i din region, kör du följande Azure CLI-kommando den [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Skapa en app i PremiumV2-nivå

Prisnivån för en App Service-app har definierats i den [App Service-plan](overview-hosting-plans.md) som den körs på. Du kan skapa en App Service-plan eller som en del av skapandet av appen.

När du konfigurerar App Service-plan i den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>väljer **prisnivå**. 

Välj **produktion**och välj sedan **P1V2**, **P2V2**, eller **P3V2**, klicka sedan på **tillämpa**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Om du inte ser **P1V2**, **P2V2**, och **P3V2** som alternativ, eller om alternativen är gråmarkerat ut sedan **PremiumV2** förmodligen inte tillgängliga i den underliggande App Service-distribution som innehåller App Service-planen. Se [skala upp från en resurs som inte stöds grupp och region kombination](#unsupported) för mer information.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skala upp en befintlig app som PremiumV2-nivå

Innan du skalar en befintlig app som **PremiumV2** nivå, se till att **PremiumV2** är tillgänglig. Mer information finns i [PremiumV2 tillgänglighet](#availability). Om det inte är tillgänglig, se [skala upp från en resurs som inte stöds grupp och region kombination](#unsupported).

Beroende på din värdmiljö kan det kräva ytterligare åtgärder för att skala upp. 

I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna din App Service-app-sida.

I den vänstra navigeringen i din App Service-app-sida väljer **skala upp (App Service-plan)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj **produktion**och välj sedan **P1V2**, **P2V2**, eller **P3V2**, klicka sedan på **tillämpa**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden har slutförts översiktssidan för din app visar att det finns i en **PremiumV2** nivå.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett felmeddelande

Vissa App Service-planer det går inte att skala upp till PremiumV2-nivå om den underliggande App Service-distributionen inte stöder PremiumV2.  Se [skala upp från en resurs som inte stöds grupp och region kombination](#unsupported) för mer information.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skala upp från en resurs som inte stöds grupp och region kombination

Om din app körs i en App Service-distribution där **PremiumV2** är inte tillgängligt eller om din app körs i en region som för närvarande stöder inte **PremiumV2**, måste du distribuera din app för att ta utnyttja **PremiumV2**.  Du kan välja mellan två alternativ:

- Skapa en **nya** resource gruppen och skapa sedan en **nya** appen och App Service plan i den **nya** resource group välja önskad Azure region under skapandeprocessen.  Du **måste** väljer den **PremiumV2** planen vid den nya app service-planen har skapats.  Detta säkerställer att kombinationen av resursgruppen, App Service-planen och Azure-region leder till App Service-planen som skapas i en App Service-distribution som har stöd för **PremiumV2**.  Omdistribuera sedan din programkod i den nyligen skapade app och app service-plan. Om du vill du därefter skalas App Service-planen från **PremiumV2** att spara kostnader och du kommer fortfarande att kunna har skala tillbaka upp igen i framtiden med **PremiumV2**.
- Om din app körs redan i en befintlig **Premium** tier, och sedan kan du klona din app med alla appinställningar, anslutningssträngar och distributionskonfiguration till en ny app service-plan som använder **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    I den **klonapp** sidan kan du skapa en App Service-plan med **PremiumV2** i regionen du vill och anger app-inställningar och konfigurationer som du vill klona.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera skapa appar i den **PremiumV2** nivå med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en App Service-plan i _P1V2_. Du kan köra den i Cloud Shell. Alternativ för `--sku` är P1V2, _P2V2_, och _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

Följande kommando skapar en App Service-plan i _P1V2_. Alternativ för `-WorkerSize` är _små_, _medel_, och _stor_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Fler resurser

[Skala upp en app i Azure](web-sites-scale.md)  
[Skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md)