---
title: Konfigurera PremiumV2-nivå
description: Lär dig hur du bättre kan prestanda för din webb-, mobil- och API-app i Azure App Service genom att skala till den nya PremiumV2-prisnivån.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672213"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurera PremiumV2-nivå för Azure App Service

Den nya **PremiumV2-prisnivån** ger dig snabbare processorer, SSD-lagring och dubblar förhållandet mellan minne och kärna för de befintliga prisnivåerna. Med prestandafördelen kan du spara pengar genom att köra dina appar på färre instanser. I den här artikeln får du lära dig hur du skapar en app på **PremiumV2-nivå** eller skalar upp en app till **PremiumV2-nivå.**

## <a name="prerequisites"></a>Krav

Om du vill skala upp en app till **PremiumV2**måste du ha en Azure App Service-app som körs på en prisnivå som är lägre än **PremiumV2**och appen måste köras i en App Service-distribution som stöder PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 tillgänglighet

**PremiumV2-nivån** är tillgänglig för App Service på både _Windows_ och _Linux_.

**PremiumV2** är tillgängligt i de flesta Azure-regioner. Om du vill se om den är tillgänglig i din region kör du följande Azure CLI-kommando i [Azure Cloud Shell:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Skapa en app på PremiumV2-nivå

Prisnivån för en App Service-app definieras i [apptjänstplanen](overview-hosting-plans.md) som den körs på. Du kan skapa en apptjänstplan av sig själv eller som en del av appskapande.

När du konfigurerar App Service-planen i <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>väljer du **Prisnivå**. 

Välj **Produktion**och välj sedan **P1V2**, **P2V2**eller **P3V2**och klicka sedan på **Använd**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Om du inte ser **P1V2,** **P2V2**och **P3V2** som alternativ, eller om alternativen är nedtonade, är **PremiumV2** sannolikt inte tillgängligt i den underliggande App Service-distributionen som innehåller App Service-planen. Mer information finns i [Skala upp från en resursgrupp och regionkombination som inte stöds.](#unsupported)

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skala upp en befintlig app till PremiumV2-nivå

Innan du skalar en befintlig app till **PremiumV2-nivå** kontrollerar du att **PremiumV2** är tillgängligt. Mer information finns i [PremiumV2-tillgänglighet](#availability). Om den inte är tillgänglig läser du [Skala upp från en resursgrupp och regionkombination som inte stöds.](#unsupported)

Beroende på din värdmiljö kan uppskalning kräva extra steg. 

Öppna appsidan för App Service i <a href="https://portal.azure.com" target="_blank">Azure-portalen.</a>

I den vänstra navigeringen på appsidan för App Service väljer du **Skala upp (App Service-plan).**

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj **Produktion**och välj sedan **P1V2**, **P2V2**eller **P3V2**och klicka sedan på **Använd**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden har slutförts visar appens översiktssida att den nu är på en **PremiumV2-nivå.**

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett felmeddelande

Vissa App Service-abonnemang kan inte skalas upp till PremiumV2-nivån om den underliggande App Service-distributionen inte stöder PremiumV2.  Mer information finns i [Skala upp från en resursgrupp och regionkombination som inte stöds.](#unsupported)

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skala upp från en resursgrupp och regionkombination som inte stöds

Om din app körs i en App Service-distribution där **PremiumV2** inte är tillgängligt, eller om din app körs i en region som för närvarande inte stöder **PremiumV2,** måste du distribuera appen igen för att dra nytta av **PremiumV2**.  Du kan välja mellan två alternativ:

- Skapa en **ny** resursgrupp och skapa sedan en **ny** app- och App Service-plan i den **nya** resursgruppen och välja önskad Azure-region under skapandeprocessen.  Du **måste** välja **PremiumV2-planen** när den nya apptjänstplanen skapas.  Detta säkerställer att kombinationen av resursgrupp, App Service-plan och Azure-region resulterar i att App Service-planen skapas i en App Service-distribution som stöder **PremiumV2**.  Distribuera sedan om programkoden till den nyskapade app- och apptjänstplanen. Om så önskas kan du skala ner App Service-planen från **PremiumV2** för att spara kostnader, och du kommer fortfarande att kunna skala upp igen i framtiden med **PremiumV2**.
- Om din app redan körs på en befintlig **Premium-nivå** kan du klona appen med alla appinställningar, anslutningssträngar och distributionskonfiguration till en ny apptjänstplan som använder **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    På **sidan Klona app** kan du skapa en App Service-plan med **PremiumV2** i den region du vill ha och ange vilka appinställningar och den konfiguration som du vill klona.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera appgenereringen på **PremiumV2-nivån** med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en apptjänstplan i _P1V2_. Du kan köra den i Cloud Shell. Alternativen för `--sku` är P1V2, _P2V2_och _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando skapar en apptjänstplan i _P1V2_. Alternativen för `-WorkerSize` är _Små,_ _Medelstora_och _Stora_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Fler resurser

[Skala upp en app i Azure](manage-scale-up.md)  
[Skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md)
