---
title: (INAKTUELL) Övervaka Azure DC/OS-kluster – åtgärdshantering
description: Övervaka ett Azure Container Service DC/OS-kluster med Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810098"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(INAKTUELL) Övervaka ett Azure Container Service DC/OS-kluster med Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Behållarlösningen är en lösning i Log Analytics, som hjälper dig att visa behållaren inventering, prestanda och loggar på en enda plats. Du kan granska, Felsök behållare genom att visa loggarna på central plats och ta bort störande förbrukar överflödiga behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om Behållarlösningen finns i den [behållare lösning Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Ställa in Log Analytics från DC/OS universe


Den här artikeln förutsätter att du har konfigurerat ett DC/OS och har distribuerat program med enkel behållare i klustret.

### <a name="pre-requisite"></a>Förhandskrav
- [Microsoft Azure-prenumeration](https://azure.microsoft.com/free/) – du kan skaffa en prenumeration utan kostnad.  
- Logga Analytics arbetsyta-installation - Se ”steg 3” nedan
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) installerad.

1. I DC/OS-instrumentpanelen klickar du på Universe och Sök efter ”OMS” enligt nedan.

   >[!NOTE]
   >OMS kallas nu för Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klicka på **Installera**. Visas ett popup-fönster med versionsinformationen och en **installera paket** eller **avancerade installationen** knappen. När du klickar på **avancerade Installation**, vilket innebär att du den **OMS specifika konfigurationsegenskaper** sidan.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Här kan du bli ombedd att ange den `wsid` (Log Analytics arbetsyte-ID) och `wskey` (primärnyckel för arbetsyte-ID). Att hämta båda `wsid` och `wskey` måste du skapa ett konto på <https://mms.microsoft.com>.
   Följ stegen för att skapa ett konto. När du är klar skapar du behöver hämta din `wsid` och `wskey` genom att klicka på **inställningar**, sedan **anslutna källor**, och sedan **Linux-servrar**, enligt nedan.

   ![](media/container-service-monitoring-oms/image5.png)

4. Välj antalet instanser som du vill och klicka på knappen ”Granska och installera”. Normalt vill du ha så många instanser som är lika med antalet Virtuella datorer du har i klustret för agenten. Log Analytics-agenten för Linux installerar som enskilda behållare på varje virtuell dator som du vill samla in information för övervakning och loggning.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Konfigurera en enkel Log Analytics-instrumentpanel

När du har installerat Log Analytics-agenten för Linux på de virtuella datorerna, är nästa steg att ställa in Log Analytics-instrumentpanelen. Du kan ställa in instrumentpanelen via Azure-portalen.

### <a name="azure-portal"></a>Azure Portal 

Logga in på Azure portal på <https://portal.microsoft.com/>. Gå till **Marketplace**väljer **övervakning + hantering** och klicka på **finns i alla**. Skriv sedan `containers` i sökningen. Du ser ”behållare” i sökresultatet. Välj **behållare** och klicka på **skapa**.

![](media/container-service-monitoring-oms/image9.png)

När du klickar på **skapa**, den ber dig om din arbetsyta. Välj din arbetsyta eller om du inte har någon kan du skapa en ny arbetsyta.

![](media/container-service-monitoring-oms/image10.PNG)

När du har valt din arbetsyta, klickar du på **skapa**.

![](media/container-service-monitoring-oms/image11.png)

Mer information om Behållarlösningen för Log Analytics finns i den [behållare lösning Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Så här skalar du Log Analytics-agenten med ACS DC/OS 

Om du har installerat Log Analytics-agenten ont om antalet faktiska noder eller skalningsuppsättning genom att lägga till mer på virtuella datorer proportionellt, kan du göra det genom att skala den `msoms` service.

Du kan gå till Marathon eller på fliken tjänster för DC/OS-Användargränssnittet och skala upp din antal noder.

![](media/container-service-monitoring-oms/image12.PNG)

Det innebär att distribueras till andra noder som inte har distribuerat Log Analytics-agenten.

## <a name="uninstall-ms-oms"></a>Avinstallera MS OMS

Om du vill avinstallera anger MS OMS du följande kommando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Berätta för oss!
Vad fungerar? Vad är saknas? Vad behöver du för detta ska vara användbar för dig? Berätta för oss på <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Nästa steg

 Nu när du har ställt in Log Analytics för att övervaka din behållare[finns i behållaren instrumentpanelen](../../azure-monitor/insights/containers.md).
