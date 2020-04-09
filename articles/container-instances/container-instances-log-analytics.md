---
title: Samla in & analysera resursloggar
description: Lär dig hur du skickar resursloggar och händelsedata från behållargrupper i Azure Container Instances till Azure Monitor-loggar
ms.topic: article
ms.date: 04/07/2020
ms.author: danlep
ms.openlocfilehash: bd21a511641d5ea027c18bedb4dce47749110bcb
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892401"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Behållargrupp och instansloggning med Azure Monitor-loggar

Log Analytics-arbetsytor ger en centraliserad plats för lagring och fråga loggdata inte bara från Azure-resurser, men också lokala resurser och resurser i andra moln. Azure Container Instances innehåller inbyggt stöd för att skicka loggar och händelsedata till Azure Monitor-loggar.

Om du vill skicka logg- och händelsedata för behållargrupper till Azure Monitor-loggar anger du ett befintligt logganalysarbetsyte-ID och arbetsytenyckel när du konfigurerar en behållargrupp. 

I följande avsnitt beskrivs hur du skapar en loggningsaktiverad behållargrupp och hur du frågar efter loggar. Du kan också [uppdatera en behållargrupp](container-instances-update.md) med ett arbetsyte-ID och arbetsytenyckel för att aktivera loggning.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> För närvarande kan du bara skicka händelsedata från Linux-behållarinstanser till Log Analytics.

## <a name="prerequisites"></a>Krav

Om du vill aktivera loggning i dina containerinstanser, behöver du följande:

* [Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (eller [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Hämta Log Analytics-autentiseringsuppgifter

Azure Container Instances behöver behörighet för att skicka data till din Log Analytics-arbetsyta. Om du vill ge behörighet och aktivera loggning, måste du ange ID för Log Analytics-arbetsytan och en av dess nycklar (primär eller sekundär) när du skapar containergrupp.

Gör följande för att hämta ID och den primära nyckeln för Log Analytics-arbetsytan:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Inställningar**väljer du **Avancerade inställningar**
1. Välj **Anslutna källor** > **Windows-servrar** (eller **Linux-servrar**- ID och nycklar är desamma för båda)
1. Anteckna:
   * **Arbetsplats-ID**
   * **Primär nyckel**

## <a name="create-container-group"></a>Skapa containergrupp

Nu när du har Log Analytics-arbetsytans ID och primärnyckel är du redo att skapa en grupp för loggningsaktiverad containergrupp.

Följande exempel visar två sätt att skapa en behållargrupp som består av en enda [flytande][fluentd] behållare: Azure CLI och Azure CLI med en YAML-mall. Den flytande behållaren producerar flera rader med utdata i standardkonfigurationen. Eftersom dessa utdata skickas till din Log Analytics-arbetsyta fungerar det bra för att demonstrera visning och frågning av loggar.

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Om du vill distribuera med Azure CLI anger du värden för parametrarna `--log-analytics-workspace` och `--log-analytics-workspace-key` i kommandot [az container create][az-container-create]. Ersätt de två arbetsytevärdena med de värden du hämtade i föregående steg (och uppdatera resursgruppens namn) innan du kör följande kommando.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Distribuera med YAML

Använd den här metoden om du föredrar att distribuera containergrupper med YAML. Följande YAML definierar en containergrupp med en enda container. Kopiera YAML-koden till en ny fil och ersätt `LOG_ANALYTICS_WORKSPACE_ID` och `LOG_ANALYTICS_WORKSPACE_KEY` med värdena du hämtade i föregående steg. Spara filen som **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
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

Kör sedan följande kommando för att distribuera behållargruppen. Ersätt `myResourceGroup` med en resursgrupp i prenumerationen (eller skapa först en resursgrupp med namnet "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Du bör få ett svar från Azure som innehåller distributionsinformation strax efter kommandot utfärdats.

## <a name="view-logs"></a>Visa loggar

När du har distribuerat containergruppen, kan det ta flera minuter (upp till 10) för de första loggposterna att visas i Azure-portalen. Så här visar du behållargruppens loggar i `ContainerInstanceLog_CL` tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt**väljer du **Loggar**  
1. Skriv följande fråga:`ContainerInstanceLog_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du först inte ser några resultat väntar du några minuter och väljer sedan knappen **Kör** för att köra frågan igen. Som standard visas loggposter i **tabellformat.** Du kan därefter expandera en rad för att visa innehållet i en enskild loggpost.

![Logga sökresultat i Azure-portalen][log-search-01]

## <a name="view-events"></a>Visa händelser

Du kan också visa händelser för behållarinstanser i Azure-portalen. Händelser inkluderar den tid instansen skapas och när den startas. Så här visar du `ContainerEvent_CL` händelsedata i tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt**väljer du **Loggar**  
1. Skriv följande fråga:`ContainerEvent_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du först inte ser några resultat väntar du några minuter och väljer sedan knappen **Kör** för att köra frågan igen. Som standard visas poster i **tabellformat.** Du kan sedan expandera en rad för att se innehållet i en enskild post.

![Resultat för händelsesökning i Azure-portalen][log-search-02]

## <a name="query-container-logs"></a>Fråga containerloggar

Azure Monitor-loggar innehåller ett omfattande [frågespråk][query_lang] för att hämta information från potentiellt tusentals rader med loggutdata.

Den grundläggande strukturen för en fråga är källtabellen (i den här artikeln, `ContainerInstanceLog_CL` eller `ContainerEvent_CL``|`) följt av en serie operatorer åtskilda av pipe-tecknet ( ). Du kan länka flera operatorer för att förfina resultatet och utför avancerade funktioner.

Om du vill visa exempel på frågeresultat klistrar du in följande fråga i frågetextrutan och väljer knappen **Kör** för att köra frågan. Den här frågan visar alla loggposter vars Meddelande-fält innehåller ordet varning:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Mer komplexa frågor stöds också. Den här frågan visar till exempel bara de loggposter för behållargruppen mycontainergroup001 som skapats den senaste timmen:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Nästa steg

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Mer information om att köra frågor mot loggar och konfigurera aviseringar i Azure Monitor-loggar finns i:

* [Förstå loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-search.md)
* [Enhetliga aviseringar i Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Övervaka containerns CPU och minne

Information om övervakning av containerinstansens CPU- och minnesresurser finns i:

* [Övervaka behållarens resurser i Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create