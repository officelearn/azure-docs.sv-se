---
title: FÖRÅLDRAD Övervaka Azure DC/OS-kluster – drift hantering
description: Övervaka ett Azure Container Service DC/OS-kluster med Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277325"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>FÖRÅLDRAD Övervaka ett Azure Container Service DC/OS-kluster med Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics är Microsofts molnbaserade IT-hanterings lösning som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur.Container lösning är en lösning i Log Analytics, som hjälper dig att Visa behållar inventering, prestanda och loggar på en enda plats. Du kan granska, felsöka behållare genom att visa loggarna på den centrala platsen och hitta störningar som konsumerar överskotts behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om container lösning finns i [behållar lösningen Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Konfigurera Log Analytics från universum för DC/OS


Den här artikeln förutsätter att du har konfigurerat en DC/OS och har distribuerat enkla webb behållar program i klustret.

### <a name="pre-requisite"></a>Förhandskrav
- [Microsoft Azure prenumeration](https://azure.microsoft.com/free/) – du kan få en prenumeration kostnads fritt.  
- Log Analytics arbets ytans konfiguration – se "steg 3" nedan
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) installerat.

1. Klicka på universum på DC/OS-instrumentpanelen och Sök efter ' OMS ' som visas nedan.

   >[!NOTE]
   >OMS kallas nu Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klicka på **Installera**. Ett popup-fönster med versions information och ett **installations paket** eller en **Avancerad installations** knapp visas. När du klickar på **Avancerad installation**som leder dig till sidan **OMS-speciella konfigurations egenskaper** .

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Här uppmanas du att ange `wsid` (Log Analytics arbetsyte-ID) och `wskey` (den primära nyckeln för arbetsyte-ID). För att få `wsid` både `wskey` och måste du skapa ett konto på <https://mms.microsoft.com>.
   Följ stegen för att skapa ett konto. När du har skapat kontot `wsid` måste du skaffa ditt och `wskey` genom att klicka på **Inställningar**, sedan **anslutna källor**och sedan Linux- **servrar**, som du ser nedan.

   ![](media/container-service-monitoring-oms/image5.png)

4. Välj det antal instanser som du vill ha och klicka på knappen granska och installera. Vanligt vis vill du ha antalet instanser som motsvarar antalet virtuella datorer som du har i agent klustret. Log Analytics agent för Linux installeras som enskilda behållare på varje virtuell dator som den vill samla in information om övervakning och loggning.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Konfigurera en enkel Log Analytics-instrumentpanel

När du har installerat Log Analytics agent för Linux på de virtuella datorerna är nästa steg att konfigurera Log Analytics instrument panelen. Du kan ställa in instrument panelen via Azure Portal.

### <a name="azure-portal"></a>Azure Portal 

Logga in på Azure Portal på <https://portal.microsoft.com/>. Gå till **Marketplace**, Välj **övervakning + hantering** och klicka på **Visa alla**. Skriv `containers` sedan Sök. Du kommer att se "behållare" i Sök resultaten. Välj **behållare** och klicka på **skapa**.

![](media/container-service-monitoring-oms/image9.png)

När du klickar på **skapa**uppmanas du att ange din arbets yta. Välj din arbets yta eller skapa en ny arbets yta om du inte har en.

![](media/container-service-monitoring-oms/image10.PNG)

När du har valt din arbets yta klickar du på **skapa**.

![](media/container-service-monitoring-oms/image11.png)

Mer information om Log Analytics container-lösningen finns i [behållar lösningen Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Så här skalar du Log Analytics agent med ACS DC/OS 

Om du behöver ha installerat Log Analytics-agenten kort av det faktiska antalet noder eller om du skalar upp en virtuell dators skalnings uppsättning genom att lägga till fler virtuella datorer kan `msoms` du göra det genom att skala tjänsten.

Du kan antingen gå till Marathon eller fliken DC/OS UI Services och skala upp antalet noder.

![](media/container-service-monitoring-oms/image12.PNG)

Detta kommer att distribueras till andra noder som ännu inte har distribuerat Log Analytics agenten.

## <a name="uninstall-ms-oms"></a>Avinstallera MS OMS

Avinstallera MS OMS genom att ange följande kommando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Berätta för oss!!!
Vad fungerar? Vad saknas? Vad behöver du göra mer för att det ska vara användbart för dig? Berätta för oss på <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Nästa steg

 Nu när du har konfigurerat Log Analytics för att övervaka dina behållare,[se din behållar instrument panel](../../azure-monitor/insights/containers.md).
