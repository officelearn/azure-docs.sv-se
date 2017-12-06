---
title: "Övervaka Azure DC/OS-kluster - Verksamhetsstyrning"
description: "Övervaka ett Azure Container Service DC/OS-kluster med Microsoft Operations Management Suite."
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: a675f0b57ed9e5d515cfa79a3a841e0f133fff6f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-operations-management-suite"></a>Övervaka ett Azure Container Service DC/OS-kluster med Operations Management Suite

Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur. Behållaren lösningen är en lösning i OMS logganalys, vilket gör det möjligt att visa behållaren inventering, prestanda och loggar på en enda plats. Du kan granska, Felsök behållare genom att visa loggarna på central plats och hitta störningar förbrukar överdriven behållare på en värd.

![](media/container-service-monitoring-oms/image1.png)

Mer information om behållaren lösning hittar du den [behållare lösning logganalys](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-oms-from-the-dcos-universe"></a>Konfigurera OMS från DC/OS-universe


Den här artikeln förutsätter att du har skapat ett DC/OS och har distribuerat enkla behållaren webbprogram på klustret.

### <a name="pre-requisite"></a>Förhandskrav
- [Microsoft Azure-prenumeration](https://azure.microsoft.com/free/) -du kan få detta kostnadsfritt.  
- Installationsprogrammet för Microsoft OMS-arbetsytan - finns i ”steg 3” nedan
- [DC/OS CLI](https://dcos.io/docs/1.8/usage/cli/install/) installerad.

1. Klicka på DC/OS-instrumentpanel på Universe och Sök efter ”OMS-enligt nedan.

![](media/container-service-monitoring-oms/image2.png)

2. Klicka på **Installera**. Du ser en pop upp med versionsinformation OMS och en **installera paket** eller **Installation i Avancerat** knappen. När du klickar på **Installation i Avancerat**, vilket innebär att du den **OMS specifika konfigurationsegenskaper** sidan.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Här kan du bli ombedd att ange den `wsid` (OMS arbetsytan ID) och `wskey` (OMS primära nyckeln för ditt arbetsyte-id). Att hämta både `wsid` och `wskey` måste du skapa en OMS-konto på <https://mms.microsoft.com>. Följ stegen för att skapa ett konto. När du är klar att skapa kontot, måste du skaffa din `wsid` och `wskey` genom att klicka på **inställningar**, sedan **anslutna källor**, och sedan **Linux-servrar**, enligt nedan.

 ![](media/container-service-monitoring-oms/image5.png)

4. Välj de nummer du OMS-instanser som du vill använda och klicka på knappen ”Granska och installera”. Normalt kommer du vilja ha antal OMS-instanser som är lika med antalet Virtuella datorer i klustret för agenten. OMS-Agent för Linux är en installation som enskilda behållare på varje virtuell dator som du vill samla in information för övervakning och loggning.

## <a name="setting-up-a-simple-oms-dashboard"></a>Konfigurera en enkel OMS-instrumentpanel

När du har installerat OMS-Agent för Linux på virtuella datorer, är nästa steg att ställa in OMS-instrumentpanelen. Det finns två sätt att göra detta: OMS-portalen och Azure-portalen.

### <a name="oms-portal"></a>OMS-portalen 

Logga in på OMS-portalen (<https://mms.microsoft.com>) och gå till den **lösning galleriet**.

![](media/container-service-monitoring-oms/image6.png)

När du är i den **lösning galleriet**väljer **behållare**.

![](media/container-service-monitoring-oms/image7.png)

När du har valt behållaren lösningen visas panelen på sidan OMS översikt över instrumentpanelen. När de infogade behållardata indexeras visas panelen fylls i med information om lösningen visa paneler.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Logga in på Azure portal på <https://portal.microsoft.com/>. Gå till **Marketplace**väljer **övervakning + management** och på **finns alla**. Skriv `containers` i sökningen. Du ser ”behållare” i sökresultaten. Välj **behållare** och på **skapa**.

![](media/container-service-monitoring-oms/image9.png)

När du klickar på **skapa**, uppmanas du för din arbetsyta. Välj din arbetsyta eller om du inte har någon kan du skapa en ny arbetsyta.

![](media/container-service-monitoring-oms/image10.PNG)

När du har valt din arbetsyta, klickar du på **skapa**.

![](media/container-service-monitoring-oms/image11.png)

Mer information om lösningen i OMS-behållaren finns i den [behållare lösning logganalys](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Så här skalar du OMS-Agent med ACS DC/OS 

Om du har installerat OMS-agent lite antalet faktiska noder eller du proportionellt VMSS genom att lägga till fler Virtuella, kan du göra det genom skalning av `msoms` service.

Du kan gå till Marathon eller fliken DC/OS UI-tjänster och skala upp din antalet noder.

![](media/container-service-monitoring-oms/image12.PNG)

Det här distribuerar till andra noder som ännu inte har distribuerat OMS-agenten.

## <a name="uninstall-ms-oms"></a>Avinstallera MS OMS

Om du vill avinstallera anger MS OMS du följande kommando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Berätta för oss!
Vad fungerar? Vad som saknas? Vad behöver du för att detta ska vara användbara för dig? Berätta för oss på <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Nästa steg

 Nu när du har ställt in OMS att övervaka din behållare[finns i instrumentpanelen behållaren](../../log-analytics/log-analytics-containers.md).
