---
title: "Azure Service Fabric-behållare övervakning och diagnostik | Microsoft Docs"
description: "Lär dig mer om att övervaka och diagnostisera behållare med OMSS behållare lösning för samordnade på Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 319ee2c0f7492389bc1767aa2669dd273f8cfa1b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Övervakning av Windows Server-behållare med OMS

## <a name="oms-containers-solution"></a>OMS behållare lösning

Operations Management Suite (OMS) Log Analytics har en behållare lösning som kan användas för att övervaka behållare. Den här lösningen är ett bra verktyg för att övervaka distributioner av behållaren styrd på Service Fabric tillsammans med Service Fabric-lösningen. Här är ett enkelt exempel på hur instrumentpanelen i lösningen ser ut:

![Grundläggande OMS-instrumentpanelen](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Den samlar även in olika typer av loggar som kan efterfrågas i verktyget OMS logganalys och kan användas för att visualisera alla mått eller händelser som genereras. Log-typer som samlas in är:

1. ContainerInventory: Visar information om behållarens plats, namn och bilder
2. ContainerImageInventory: information om distribuerade bilder, inklusive ID eller storlekar
3. ContainerLog: specifika felloggar, docker-loggar (stdout osv.) och andra transaktioner
4. ContainerServiceLog: docker daemon kommandon som har körts
5. Prestanda: prestandaräknare inklusive behållaren cpu, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna

Den här artikeln beskriver de steg som krävs för att ställa in behållaren övervakning för klustret. Mer information om OMSS behållare lösning checka ut sina [dokumentationen](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Konfigurera ett Service Fabric-kluster

Skapa ett kluster med Azure Resource Manager-mallen finns [här](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows). Se till att lägga till ett unikt namn för OMS-arbetsytan. Som standard också den här mallen till att distribuera ett kluster i förhandsversionen av Service Fabric (v255.255), vilket innebär att den inte kan användas i produktion och kan inte uppgraderas till en annan version av Service Fabric. Om du vill använda den här mallen för långsiktig eller använda, ändrar du versionen till en stabil versionsnummer.

När klustret har konfigurerats, bekräfta att du har installerat lämpligt certifikat och se till att du ska kunna ansluta till klustret.

Kontrollera att resursgruppen har ställts in korrekt av rubriken till den [Azure-portalen](https://portal.azure.com/) och söka efter distributionen. Resursgruppen bör innehålla alla Service Fabric-resurser och även ha en Log Analytics-lösning som en Service Fabric-lösning.

För att ändra ett befintligt Service Fabric-kluster:
* Bekräfta att diagnostik är aktiverat (om inte, kan du aktivera det via [uppdatering av virtuella datorns skaluppsättning](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Lägg till en OMS-arbetsyta genom att skapa en ”Service Fabric Analytics”-lösning via Azure Marketplace
* Redigera datakällor av Service Fabric-lösningen för att hämta data från lämplig Azure Storage-tabeller (ställa in av BOMULLSTUSS) i den resursgrupp som klustret är i
* Lägg till agenten som en [tillägget till virtuella datorns skaluppsättning](/powershell/module/azurerm.compute/add-azurermvmssextension) via PowerShell eller genom att uppdatera den virtuella datorn skaluppsättning (samma länk som ovan för att ändra Resource Manager-mallen)

## <a name="2-deploy-a-container"></a>2. Distribuera en behållare

När klustret är klar och du har bekräftat att du har åtkomst till den, kan du distribuera en behållare till den. Om du väljer att använda förhandsversionen som angetts i mallen kan du även utforska nya Service Fabric-docker compose funktioner. Tänk dessutom på att den första gången som en behållare avbildning distribueras till ett kluster, det tar flera minuter att ladda ned avbildningen beroende på storleken.

## <a name="3-add-the-containers-solution"></a>3. Lägg till behållare lösning

Skapa en resurs för behållare i Azure-portalen (under övervakning + Management kategori) via Azure Marketplace. 

![Lägga till behållare lösning](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

I steget Skapa begär en OMS-arbetsyta. Markera det som har skapats med distributionen ovan. Det här steget lägger till en behållare lösning OMS-arbetsyta och identifieras automatiskt av OMS-agenten distribueras av mallen. Agenten börjar samla in data på behållare processer i klustret och 10 – 15 minuter bör visas i lösningen lätt in med data som en avbildning av instrumentpanelen ovan.

## <a name="4-next-steps"></a>4. Nästa steg

OMS erbjuder olika verktyg i arbetsytan för att se om det är mer användbar för dig. Läs följande alternativ för att anpassa lösningen för dina behov:
- Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
- Konfigurera OMS-agent för specifika prestandaräknare (Gå till arbetsytan Hem > Inställningar > Data > Windows prestandaräknare)
- Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik