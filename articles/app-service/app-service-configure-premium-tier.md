---
title: Konfigurera PremiumV2-nivå
description: Lär dig hur du får bättre prestanda för din webb-, mobil-och API-app i Azure App Service genom att skala till den nya pris nivån för PremiumV2.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: 4db7c6bf29d0874b5441a8a0eb90f7d1ada33d9c
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962442"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurera PremiumV2-nivån för Azure App Service

Den nya pris nivån för **PremiumV2** ger dig snabbare processorer, SSD-lagring och dubblar förhållandet mellan minne och kärna för de befintliga pris nivåerna. Med prestanda fördelen kan du spara pengar genom att köra dina appar på färre instanser. I den här artikeln får du lära dig hur du skapar en app i **PremiumV2** -nivån eller skalar upp en app till **PremiumV2** -nivån.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skala upp en app till **PremiumV2**måste du ha en Azure App Service-app som körs på en pris nivå som är lägre än **PremiumV2**och appen måste köras i en app service-distribution som stöder PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 tillgänglighet

**PremiumV2** -nivån är tillgänglig för App Service både på _Windows_ och _Linux_.

**PremiumV2** finns i de flesta Azure-regioner. Om du vill se om den är tillgänglig i din region kör du följande Azure CLI-kommando i [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Skapa en app i PremiumV2-nivån

Pris nivån för en App Service-app definieras i [App Service plan](overview-hosting-plans.md) som den körs på. Du kan skapa en App Service plan separat eller som en del av att skapa appar.

När du konfigurerar App Service plan i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer du **pris nivå**. 

Välj **produktion**, välj sedan **P1V2**, **P2V2**eller **P3V2**och klicka sedan på **Verkställ**.

![Skärm bild som visar de rekommenderade pris nivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Om du inte ser alternativen **P1V2**, **P2V2**och **P3V2** som alternativ, eller om alternativen är nedtonade, är **PremiumV2** troligen inte tillgängligt i den underliggande app service distribution som innehåller App Service plan. Se [skala upp från en resurs grupp och en region kombination som inte stöds](#unsupported) för mer information.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skala upp en befintlig app till PremiumV2-nivån

Innan du skalar en befintlig app till **PremiumV2** -nivån ser du till att **PremiumV2** är tillgängligt. Mer information finns i [PremiumV2-tillgänglighet](#availability). Om den inte är tillgänglig kan du läsa mer i [skala upp från en kombination av resurs grupp och region som inte stöds](#unsupported).

Beroende på din värd miljö kan det krävas ytterligare steg vid skalningen. 

Öppna din App Service app-sida i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

I det vänstra navigerings fönstret på App Service app-sidan väljer du **skala upp (App Service plan)**.

![Skärm bild som visar hur du skalar upp din app service-plan.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj **produktion**, välj sedan **P1V2**, **P2V2**eller **P3V2**och klicka sedan på **Verkställ**.

![Skärm bild som visar de rekommenderade pris nivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden slutförs, visar appens översikts sida att den nu finns på en **PremiumV2** -nivå.

![Skärm bild som visar pris nivån PremiumV2 på appens översikts sida.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett fel meddelande

Vissa App Services planer kan inte skala upp till PremiumV2-nivån om den underliggande App Service distributionen inte stöder PremiumV2.  Se [skala upp från en resurs grupp och en region kombination som inte stöds](#unsupported) för mer information.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skala upp från en kombination av resurs grupp och region som inte stöds

Om din app körs i en App Service distribution där **PremiumV2** inte är tillgänglig, eller om din app körs i en region som för närvarande inte stöder **PremiumV2**, måste du distribuera om din app för att kunna dra nytta av **PremiumV2**.  Du kan välja mellan två alternativ:

- Skapa en **ny** resurs grupp och skapa sedan en **ny** app och App Service plan i den **nya** resurs gruppen och välj önskad Azure-region under skapande processen.  Du **måste** välja **PremiumV2** -planen vid den tidpunkt då den nya app service-planen skapas.  Detta garanterar att kombinationen av resurs grupp, App Service plan och Azure-region leder till att App Service plan skapas i en App Service-distribution som stöder **PremiumV2**.  Distribuera sedan program koden till den nyligen skapade appen och App Service-planen. Om du vill kan du skala App Service plan nedåt från **PremiumV2** för att spara kostnaderna, och du kan fortfarande skala upp igen i framtiden med **PremiumV2**.
- Om din app redan körs på en befintlig **Premium** -nivå kan du klona din app med alla appinställningar, anslutnings strängar och distributions konfiguration till en ny app service-plan som använder **PremiumV2**.

    ![Skärm bild som visar hur du klonar din app.](media/app-service-configure-premium-tier/clone-app.png)

    På sidan **klon app** kan du skapa en app service plan med **PremiumV2** i den region som du vill använda och ange de appinställningar och den konfiguration som du vill klona.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera skapandet av appar i **PremiumV2** -nivån med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en App Service plan i _P1V2_. Du kan köra den i Cloud Shell. Alternativen för `--sku` är P1V2, _P2V2_och _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando skapar en App Service plan i _P1V2_. Alternativen för `-WorkerSize` är _små_, _medel stora_och _stora_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Fler resurser

[Skala upp en app i Azure](manage-scale-up.md)  
[Skala instansantalet manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md)