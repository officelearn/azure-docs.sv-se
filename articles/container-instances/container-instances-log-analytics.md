---
title: Behållarinstansloggning med Azure Log Analytics
description: Läs hur du skickar behållarutdata (STDOUT och STDERR) till Azure Log Analytics.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 06/06/2018
ms.author: marsma
ms.openlocfilehash: a0772d1009021ca64b448710c5353407a5492fae
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809885"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Behållarinstansloggning med Azure Log Analytics

Log Analytics-arbetsytor erbjuder en central plats för att lagra och hämta loggdata från inte bara Azure-resurser, utan även på lokala resurser och resurser i andra moln. Azure Behållarinstanser innehåller inbyggt stöd för att skicka data till Log Analytics.

Om du vill skicka behållarinstansdata till Log Analytics, måste du skapa en behållargrupp med Azure CLI (eller Cloud Shell) och en YAML-fil. I följande avsnitt beskrivs hur du skapar loggaktiverad behållargrupp och frågning av loggar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill aktivera loggning i dina behållarinstanser, behöver du följande:

* [Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (eller [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Hämta Log Analytics-autentiseringsuppgifter

Azure Behållarinstanser behöver behörighet för att skicka data till din Log Analytics-arbetsyta. Om du vill ge behörighet och aktivera loggning, måste du ange ID för Log Analytics-arbetsytan och en av dess nycklar (primär eller sekundär) när du skapar behållargrupp.

Du kan hämta ID och den primära nyckeln för Log Analytics-arbetsytan genom att:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Inställningar**, väljer du **Avancerade inställningar**
1. Välj **Anslutna källor** > **Windows-servrar** (eller **Linux-servrar**. ID och nycklar är samma för båda)
1. Anteckna:
   * **Arbetsplats-ID**
   * **Primär nyckel**

## <a name="create-container-group"></a>Skapa behållaregrupp

Nu när du har Log Analytics arbetsyte-ID och primärnyckeln, är du redo att skapa en grupp för loggningsaktiverad behållargrupp. Följande exempel skapar en behållargrupp med en enda [fluentd][fluentd]-behållare. Fluentd-behållaren skapar flera rader utdata i sin standardkonfiguration. Eftersom dessa utdata skickas till din Log Analytics-arbetsyta fungerar det bra för att demonstrera visning och frågning av loggar.

Först kopierar du följande YAML, som definierar en behållargrupp med en enskild behållare, till en ny fil. Ersätt `LOG_ANALYTICS_WORKSPACE_ID` och `LOG_ANALYTICS_WORKSPACE_KEY` med värden som du hämtade i föregående steg, spara sedan filen som **deploy-aci.yaml**.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Sedan kör du följande kommando för att distribuera behållargruppen. Ersätt `myResourceGroup` med en resursgrupp i din prenumeration (eller skapa först en resursgrupp med namnet myResourceGroup):

```azurecli-interactive
az container create -g myResourceGroup -n mycontainergroup001 -f deploy-aci.yaml
```

Du bör få ett svar från Azure som innehåller distributionsinformation strax efter kommandot utfärdats.

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

När du har distribuerat behållargruppen, kan det ta flera minuter (upp till 10) för de första loggposterna att visas i Azure-portalen. Om du vill visa behållargruppens loggar, öppnar du din Log Analytics-arbetsyta och:

1. I översikten för **OMS-arbetsytan** , väljer du **Loggsökning**
1. Under **några fler frågor att testa**, väljer du länken **Alla insamlade data**

Du bör se flera resultat som visas av `search *`-frågan. Om du först inte ser något resultat, vänta några minuter och välj sedan knappen **Kör** för att köra frågan igen. Som standard visas loggposter i List-vyn. Välj **Tabell** för att se loggposter i ett mer komprimerat format. Du kan därefter expandera en rad för att visa innehållet i en enskild loggpost.

![Logga sökresultat i Azure-portalen][log-search-01]

## <a name="query-container-logs"></a>Fråga behållarloggar

Log Analytics inkluderar ett omfattande [frågespråk][query_lang] för att ta emot information från potentiellt tusentals rader med loggutdata.

Azure Behållarinstansernas loggningsagent skickar poster till `ContainerInstanceLog_CL`-tabellen i din Log Analytics-arbetsyta. Den grundläggande strukturen i en fråga är källtabellen (`ContainerInstanceLog_CL`) följt av en serie operatorer avgränsade av vertikalstrecket (`|`). Du kan länka flera operatorer för att förfina resultatet och utför avancerade funktioner.

Om du vill se exempel på frågeresultat klistrar du in följande fråga i frågetextrutan (under Visa äldre språkkonverteraren) och välj knappen **Kör** för att köra frågan. Den här frågan visar alla loggposter vars Meddelande-fält innehåller ordet varning:

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Mer komplexa frågor stöds också. Den här frågan visar till exempel bara de loggposter för behållargruppen mycontainergroup001 som skapats den senaste timmen:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Nästa steg

### <a name="log-analytics"></a>Log Analytics

Mer information om att fråga loggar och konfigurera aviseringar i Azure Log Analytics finns i:

* [Förstå loggsökningar i Log Analytics](../log-analytics/log-analytics-log-search.md)
* [Enhetliga aviseringar i Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

### <a name="monitor-container-cpu-and-memory"></a>Övervaka behållarens CPU och minne

Information om övervakning av behållarinstansens CPU- och minnesresurser finns i:

* [Övervaka behållarens resurser i Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://docs.loganalytics.io/

<!-- LINKS - Internal -->