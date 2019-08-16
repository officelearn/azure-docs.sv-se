---
title: Konfigurera Azure Monitor för behållare agent data insamling | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare agent för att styra logg insamling för STDOUT/stderr och miljövariabler.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 2b601825a58fe5739a43df607067acc8d629c5f4
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558892"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurera agent data insamling för Azure Monitor för behållare

Azure Monitor för behållare samlar in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS) från behållarens agent. Den här agenten kan även samla in tids serie data (kallas även mått) från Prometheus med hjälp av agenten för behållare utan att behöva konfigurera och hantera en Prometheus-Server och-databas. Du kan konfigurera inställningar för data insamling för agenter genom att skapa en anpassad Kubernetes-ConfigMaps för att styra den här upplevelsen. 

Den här artikeln visar hur du skapar ConfigMap och konfigurerar data insamling utifrån dina krav.

>[!NOTE]
>Stöd för Prometheus är en funktion i offentlig för hands version för tillfället.
>

## <a name="configmap-file-settings-overview"></a>Översikt över ConfigMap-fil inställningar

En mall ConfigMap-fil tillhandahålls som gör att du enkelt kan redigera den med dina anpassningar utan att behöva skapa den från grunden. Innan du börjar bör du gå igenom Kubernetes-dokumentationen om [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) och bekanta dig med hur du skapar, konfigurerar och distribuerar ConfigMaps. Detta gör att du kan filtrera stderr och STDOUT per namnrymd eller över hela klustret, och miljövariabler för alla behållare som körs i alla poddar/noder i klustret.

>[!IMPORTANT]
>Den lägsta agent version som stöds för att samla in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar är ciprod06142019 eller senare. Den lägsta agent version som stöds för att kassera Prometheus-mått är ciprod07092019 eller senare. Om du vill verifiera agent versionen går du till fliken **Node** och väljer en nod. i rutan Egenskaper noterar du värdet för egenskapen för **agentens avbildnings tag** .  

### <a name="data-collection-settings"></a>Inställningar för data insamling

Följande är de inställningar som kan konfigureras för att styra data insamling.

|Nyckel |Datatyp |Value |Beskrivning |
|----|----------|------|------------|
|`schema-version` |Sträng (Skift läges känslig) |v1 |Det här är den schema version som används av agenten vid parsning av den här ConfigMap. Schema version som stöds för närvarande är v1. Det finns inte stöd för att ändra det här värdet och kommer att avvisas när ConfigMap utvärderas.|
|`config-version` |Sträng | | Stöder möjlighet att hålla koll på den här konfigurations filens version i käll kontroll systemet/lagrings platsen. Maximalt antal tillåtna tecken är 10 och alla andra tecken trunkeras. |
|`[log_collection_settings.stdout] enabled =` |Boolean | Sant eller falskt | Kontrollerar om STDOUT container logg samling är aktive rad. När värdet är `true` inställt på och inga namn områden utesluts för`log_collection_settings.stdout.exclude_namespaces` STDOUT logg samling (inställningen nedan) samlas STDOUT-loggar in från alla behållare i alla poddar/noder i klustret. Om inget värde anges i ConfigMaps är `enabled = true`standardvärdet. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Sträng | Kommaavgränsad matris |Matris med Kubernetes-namnområden som StdOut-loggar inte ska samlas in för. Den här inställningen gäller endast om `log_collection_settings.stdout.enabled` har angetts till `true`. Om inget värde anges i ConfigMap är `exclude_namespaces = ["kube-system"]`standardvärdet.|
|`[log_collection_settings.stderr] enabled =` |Boolean | Sant eller falskt |Anger om stderr container logg samling är aktive rad. När värdet är `true` inställt på och inga namn områden utesluts för`log_collection_settings.stderr.exclude_namespaces` STDOUT logg insamling (inställning) samlas stderr-loggar in från alla behållare i alla poddar/noder i klustret. Om inget värde anges i ConfigMaps är `enabled = true`standardvärdet. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Sträng |Kommaavgränsad matris |Matris med Kubernetes-namnområden som stderr-loggar inte ska samlas in för. Den här inställningen gäller endast om `log_collection_settings.stdout.enabled` har angetts till `true`. Om inget värde anges i ConfigMap är `exclude_namespaces = ["kube-system"]`standardvärdet. |
| `[log_collection_settings.env_var] enabled =` |Boolesk | Sant eller falskt | Detta kontrollerar om en miljö variabel samling är aktive rad. När det är `false`inställt på, samlas inga miljövariabler in för någon behållare som körs i alla poddar/noder i klustret. Om inget värde anges i ConfigMap är `enabled = true`standardvärdet. |

### <a name="prometheus-scraping-settings"></a>Prometheus OLE-inställningar

![Arkitektur för övervakning av behållare för Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Azure Monitor for containers är en sömlös upplevelse för att möjliggöra insamling av Prometheus-mått genom flera intagningar genom följande metoder som visas i följande tabell. Måtten samlas in via en uppsättning inställningar som anges i en enda ConfigMap-fil, som är samma fil som används för att konfigurera insamling av STDOUT-, stderr-och miljövariabler från behållar arbets belastningar. 

Aktiv kasse ring av mått från Prometheus utförs från ett av två perspektiv:

* Klustrad HTTP-URL och identifiera mål från listade slut punkter för en tjänst, K8s-tjänster som Kube-DNS och Kube-State-Metric och Pod-anteckningar som är specifika för ett program. Mått som samlas in i den här kontexten definieras i avsnittet ConfigMap *[Prometheus data_collection_settings. Cluster]* .
* Nod-bred HTTP-URL och identifiera mål från listan över slut punkter för en tjänst. Mått som samlas in i den här kontexten definieras i avsnittet ConfigMap *[Prometheus_data_collection_settings. node]* .

| Slutpunkt | Omfång | Exempel |
|----------|-------|---------|
| Pod-anteckning | Hela klustret | anteckningar <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Kubernetes-tjänst | Hela klustret | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/slut punkt | Per nod och/eller över hela klustret | `http://myurl:9101/metrics` |

När en URL anges, kommer Azure Monitor för behållare bara att kassera slut punkten. När Kubernetes-tjänsten anges, matchas tjänst namnet med klustrets DNS-server för att hämta IP-adressen och sedan kasseras den lösta tjänsten.

|Omfång | Nyckel | Datatyp | Value | Beskrivning |
|------|-----|-----------|-------|-------------|
| Hela klustret | | | | Ange en av följande tre metoder för att kassera slut punkter för mått. |
| | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (antingen IP-adress eller giltig URL-sökväg har angetts). Till exempel: `urls=[$NODE_IP/metrics]`. ($NODE _IP är en speciell Azure Monitor för container parameter och kan användas i stället för nodens IP-adress. Måste vara alla versaler.) |
| | `kubernetes_services` | Sträng | Kommaavgränsad matris | En matris med Kubernetes-tjänster för att kassera mått från Kube-State-Metrics. Till exempel`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | Sant eller falskt | När det är `true` inställt på i inställningarna för hela klustret kommer Azure Monitor for containers agent att kassera Kubernetes-poddar över hela klustret för följande Prometheus-anteckningar:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolesk | Sant eller falskt | Aktiverar kassation av pod. `monitor_kubernetes_pods`måste anges till `true`. |
| | `prometheus.io/scheme` | Sträng | http eller https | Används som standard för skrotning över HTTP. Om det behövs anger du `https`till. | 
| | `prometheus.io/path` | Sträng | Kommaavgränsad matris | Den HTTP-resurs Sök väg som måtten ska hämtas från. Om måtten Path inte `/metrics`är det definierar du den med den här anteckningen. |
| | `prometheus.io/port` | Sträng | 9102 | Ange en port att lyssna på. Om porten inte har angetts är den standard 9102. |
| Node-wide | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (antingen IP-adress eller giltig URL-sökväg har angetts). Till exempel: `urls=[$NODE_IP/metrics]`. ($NODE _IP är en speciell Azure Monitor för container parameter och kan användas i stället för nodens IP-adress. Måste vara alla versaler.) |
| Hela noden eller hela klustret | `interval` | Sträng | 60 s | Samlings intervallets standardvärdet är en minut (60 sekunder). Du kan ändra samlingen för antingen *[prometheus_data_collection_settings. node]* och/eller *[prometheus_data_collection_settings. Cluster]* till Time units, t. ex. ns, US (eller Âμs), MS, s, m, h. |
| Hela noden eller hela klustret | `fieldpass`<br> `fielddrop`| Sträng | Kommaavgränsad matris | Du kan ange att vissa mått ska samlas in eller inte från slut punkten genom att ange List rutan`fieldpass`Tillåt () och`fielddrop`neka (). Du måste först ange listan över tillåtna. |

ConfigMap är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Det går inte att ha en annan ConfigMap över samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera och distribuera din ConfigMap-konfigurationsfil till klustret.

1. [Hämta](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) mallen ConfigMap yaml File och spara den som container-AZM-MS-agentconfig. yaml.  
1. Redigera ConfigMap yaml-filen med dina anpassningar.

    - Om du vill undanta vissa namn rymder för STDOUT-logg samling konfigurerar du nyckeln/värdet med hjälp av `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`följande exempel:.
    
    - Om du vill inaktivera en miljö variabel samling för en speciell behållare anger du nyckel `[log_collection_settings.env_var] enabled = true` /värde för att aktivera variabel insamling globalt och följer sedan stegen [här](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) för att slutföra konfigurationen av den angivna behållaren.
    
    - Om du vill inaktivera stderr-logg samling i hela klustret konfigurerar du nyckeln/värdet med hjälp av följande `[log_collection_settings.stderr] enabled = false`exempel:.
    
    - I följande exempel visas hur du konfigurerar ConfigMap-filmåtten från ett globalt URL-kluster, från en agents DameonSet Node-wide och genom att ange en POD-anteckning

        - Kassera Prometheus mått från en viss URL i klustret.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - Kassera Prometheus-mått från en agents DaemonSet som körs i varje nod i klustret.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Kassera Prometheus mått genom att ange en POD-anteckning.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. Skapa ConfigMap genom att köra följande kubectl-kommando `kubectl apply -f <configmap_yaml_file.yaml>`:.
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created`.

Verifiera att konfigurationen har tillämpats genom att använda följande kommando för att granska loggarna från en agent pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Om det finns konfigurations fel från omsagent-poddar visas fel som liknar följande i utdata:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel som rör tillämpning av konfigurations ändringar för Prometheus finns också tillgängliga för granskning.  Antingen från loggarna från en agent Pod med samma `kubectl logs` kommando eller från Live-loggar. Live-loggar visar fel som liknar följande:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Fel förhindrar omsagent från att parsa filen, vilket gör att den startas om och använder standard konfigurationen. När du har korrigerat felen i ConfigMap sparar du yaml-filen och använder den uppdaterade ConfigMaps genom att köra kommandot: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Använder uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap till klustret och du vill uppdatera det med en nyare konfiguration kan du redigera ConfigMap-filen som du tidigare har använt och sedan använda samma kommando som tidigare `kubectl apply -f <configmap_yaml_file.yaml`.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verifierar schema version

Konfigurations schema versioner som stöds är tillgängliga som Pod-anteckning (schema versioner) på omsagent-pod. Du kan se dem med följande kubectl-kommando:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Utdata ser ut ungefär så här med antecknings schema versioner:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Granska Prometheus data användning

Följande fråga tillhandahålls för att identifiera inmatnings volymen för varje mått storlek i GB per dag för att förstå om den är hög.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Resultatet kommer att visa resultat som liknar följande:

![Logga frågeresultaten för data inmatnings volym](./media/container-insights-agent-config/log-query-example-usage-03.png)

För att uppskatta vad varje mått storlek i GB är en månad för att förstå om den inmatade mängden data som tas emot i arbets ytan är hög, så tillhandahålls följande fråga.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Resultatet kommer att visa resultat som liknar följande:

![Logga frågeresultaten för data inmatnings volym](./media/container-insights-agent-config/log-query-example-usage-02.png)

Mer information om hur du övervakar data användning och analys av kostnader finns i [Hantera användning och kostnader med Azure Monitor loggar](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Nästa steg

Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Mer information om hur du skapar rekommenderade aviseringar för hög processor-och minnes användning finns i avsnittet [skapa prestanda aviseringar med Azure Monitor för behållare](container-insights-alerts.md) som stöder DevOps eller operativa processer och procedurer

- Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.