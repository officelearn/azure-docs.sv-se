---
title: (FÖRÅLDRAD) Övervaka Azure DC/OS-kluster - Operations Management
description: Övervaka ett DC/OS-kluster för Azure-behållartjänsten med Logganalys.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277325"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(FÖRÅLDRAD) Övervaka ett DC/OS-kluster för Azure-behållartjänsten med Logganalys

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur.Container Solution är en lösning i Log Analytics, som hjälper dig att visa behållarinventet, prestanda och loggar på en enda plats. Du kan granska, felsöka behållare genom att visa loggarna på centraliserad plats och hitta bullriga förbrukar överskott behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om behållarlösning finns i [Container Solution Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Konfigurera Logganalys från DC/OS-universumet


Den här artikeln förutsätter att du har konfigurerat en DOMÄNKONTROLLANT/OPERATIVSYSTEM och har distribuerat enkla webbbehållareprogram i klustret.

### <a name="pre-requisite"></a>Förhandskrav
- [Microsoft Azure-prenumeration](https://azure.microsoft.com/free/) – Du kan få en prenumeration gratis.  
- Inställningar för logganalysarbetsyta – se "Steg 3" nedan
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) installerat.

1. I DC/ OS-instrumentpanelen klickar du på Universum och söker efter "OMS" enligt nedan.

   >[!NOTE]
   >OMS kallas nu Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klicka på **Installera**. Du kommer att se ett popup-fönster med versionsinformationen och knappen **Installationspaket** eller **Avancerad installation.** När du klickar på **Avancerad installation**, som leder dig till **OMS specifika konfigurationsegenskaper** sidan.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Här blir du ombedd att `wsid` ange (Log Analytics-arbetsyte-ID) och `wskey` (den primära nyckeln för arbetsytans ID). För att `wsid` `wskey` få båda och du <https://mms.microsoft.com>måste skapa ett konto på .
   Följ stegen för att skapa ett konto. När du är klar med att skapa `wsid` `wskey` kontot måste du skaffa ditt och genom att klicka på **Inställningar**, sedan **Anslutna källor**och sedan **Linux-servrar**, som visas nedan.

   ![](media/container-service-monitoring-oms/image5.png)

4. Välj hur många instanser du vill använda och klicka på knappen "Granska och installera". Vanligtvis vill du ha antalet instanser som är lika med antalet virtuella datorer som du har i agentklustret. Log Analytics-agent för Linux installeras som enskilda behållare på varje virtuell dator som den vill samla in information för övervakning och loggningsinformation.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Konfigurera en enkel Log Analytics-instrumentpanel

När du har installerat Log Analytics-agenten för Linux på de virtuella datorerna är nästa steg att konfigurera Log Analytics-instrumentpanelen. Du kan konfigurera instrumentpanelen via Azure-portalen.

### <a name="azure-portal"></a>Azure Portal 

Logga in på <https://portal.microsoft.com/>Azure Portal på . Gå till **Marketplace,** välj **Övervakning + hantering** och klicka på Visa **alla**. Skriv `containers` sedan i sökningen. Du kommer att se "behållare" i sökresultaten. Välj **Behållare** och klicka på **Skapa**.

![](media/container-service-monitoring-oms/image9.png)

När du klickar på **Skapa**kommer det att be dig om din arbetsyta. Välj arbetsytan eller om du inte har någon, skapa en ny arbetsyta.

![](media/container-service-monitoring-oms/image10.PNG)

När du har valt arbetsytan klickar du på **Skapa**.

![](media/container-service-monitoring-oms/image11.png)

Mer information om log analytics-behållarlösningen finns i [Container Solution Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Så här skalar du Log Analytics-agent med ACS DC/OS 

Om du behöver ha installerat Log Analytics-agenten kort efter det faktiska antalet noder eller om du skalar `msoms` upp den virtuella datorns skalninguppsättning genom att lägga till mer virtuell dator, kan du göra det genom att skala tjänsten.

Du kan antingen gå till Marathon eller fliken DC/OS UI Services och skala upp antalet nod.

![](media/container-service-monitoring-oms/image12.PNG)

Detta distribueras till andra noder som ännu inte har distribuerat Log Analytics-agenten.

## <a name="uninstall-ms-oms"></a>Avinstallera MS OMS

Så här avinstallerar du MS OMS och anger följande kommando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Låt oss veta!!!
Vad fungerar? Vad saknas? Vad mer behöver du för att detta ska vara användbart för dig? Låt oss veta på <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Nästa steg

 Nu när du har konfigurerat Log Analytics för att övervaka dina behållare[läser du instrumentpanelen för behållaren](../../azure-monitor/insights/containers.md).
