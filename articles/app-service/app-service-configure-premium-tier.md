---
title: "Konfigurera PremiumV2 nivå för Azure App Service | Microsoft Docs"
description: "Lär dig hur du bättre prestanda för webb-, mobil- och API-app i Azure App Service genom att skala till nya PremiumV2 prisnivån."
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 92cc8d8b0f67dde95ea2e3fc2f0f083bd8ac8aab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurera PremiumV2 nivå för Azure App Service

Den nya **PremiumV2** prisnivån ger [Dv2-serien VMs](../virtual-machines/windows/sizes-general.md#dv2-series) med snabba processorer, SSD-lagringen och dubbla kärnor till minne jämfört med **Standard** nivå. I den här artikeln får du lära dig hur du skapar en app i **PremiumV2** tjänstnivån eller skala upp en app på **PremiumV2** nivå.

## <a name="prerequisites"></a>Krav

Att skala upp en webbapp till **PremiumV2**, måste du ha en Webbapp i Azure App Service som körs i en prisnivå som är lägre än **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 tillgänglighet

PremiumV2 nivån är tillgängligt för användning på _Windows_ endast. Linux-behållare stöds inte ännu.

PremiumV2 är redan tillgängliga i de flesta Azure-regioner och växande. För att se om den är tillgänglig i din region, kör du följande Azure CLI-kommandot i den [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Om du får ett fel under generering av appen eller App Service-plan skapas sedan **PremiumV2** sannolikt inte tillgängligt för din region val.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Skapa en app i PremiumV2 nivå

Prisnivån för App Service-appen definieras i den [programtjänstplanen](azure-web-sites-web-hosting-plans-in-depth-overview.md) som körs. Du kan skapa en apptjänstplan eller som en del av skapandet av Web App.

När du konfigurerar App Service-plan i den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>väljer **prisnivå**. 

Välj något av de **PremiumV2** alternativ och klickar på **Välj**.

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> Om du inte ser **P1V2**, **P2V2**, och **P3V2** som alternativ, antingen **PremiumV2** är inte tillgänglig i din region väljer, eller så är Konfigurera en Linux App Service-plan som inte stöder **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skala upp en befintlig app PremiumV2-nivån

Innan du skala en befintlig app som **PremiumV2** tjänstnivån, se till att **PremiumV2** är tillgängliga i din region. Mer information finns i [PremiumV2 tillgänglighet](#availability). Om det inte är tillgängligt i din region finns [skala upp från ett område som stöds inte](#unsupported).

Beroende på din värdmiljö kräver skala upp extra steg. 

I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna sidan Apptjänst-app.

I det vänstra navigeringsfönstret på Apptjänst appsidan Välj **skala upp (apptjänstplan)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj något av de **PremiumV2** storlekar och klicka sedan på **Välj**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden slutförs översiktssidan för din app visar att det finns i en **PremiumV2** nivå.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett felmeddelande

Vissa App Service-planer går inte att skala upp PremiumV2-nivån. Om åtgärden skala upp får du ett fel, måste en ny App Service-plan för din app.

Skapa en _Windows_ App Service-plan i gruppen samma region och resurs som din befintliga Apptjänst-app. Följ stegen i [skapa en app i PremiumV2 nivå](#create) ska anges till **PremiumV2** nivå. Om du vill använda samma skalbara konfiguration som din befintliga programtjänstplan (antal instanser, Autoskala och så vidare).

Öppna sidan Apptjänst appen igen. I det vänstra navigeringsfönstret för din Apptjänst väljer **ändra programtjänstplanen**.

![](media/app-service-configure-premium-tier/change-plan.png)

Välj App Service-plan som du nyss skapade.

![](media/app-service-configure-premium-tier/select-plan.png)

När ändringen har slutförts, kör din app **PremiumV2** nivå.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Skala upp från ett område som stöds inte

Om din app körs i en region där **PremiumV2** är ännu inte tillgänglig, kan du flytta din app till en annan region dra nytta av **PremiumV2**. Du kan välja mellan två alternativ:

- Skapa en app i en ny **PremiumV2** planera och distribuera din programkod. Följ stegen i [skapa en app i PremiumV2 nivå](#create) ska anges till **PremiumV2** nivå. Om du vill använda samma skalbara konfiguration som din befintliga programtjänstplan (antal instanser, Autoskala och så vidare).
- Om din app körs redan i en befintlig **Premium** nivån, och sedan kan du klona en app med alla appinställningar anslutningssträngar och distributionskonfiguration.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    I den **klona app** kan du skapa en ny programtjänstplan i den region som du vill ha och ange inställningar som du vill klona.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera genereringen av en app i den **PremiumV2** nivå med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en apptjänstplan i _P1V2_. Du kan köra det i molnet-gränssnittet. Alternativ för `--sku` är P1V2, _P2V2_, och _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

Följande kommando skapar en apptjänstplan i _P1V2_. Alternativ för `-WorkerSize` är _små_, _medel_, och _stor_.

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