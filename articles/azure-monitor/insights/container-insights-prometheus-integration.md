---
title: Konfigurera Azure Monitor för behållare Prometheus Integration | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare agent för att skrapa mått från Prometheus med kubernetes-klustret.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537380"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Konfigurera skrapning av Prometheus-mått med Azure Monitor för behållare

[Prometheus](https://prometheus.io/) är en populär öppen källkod metriska övervakningslösning och är en del av [Cloud Native Compute Foundation](https://www.cncf.io/). Azure Monitor för behållare ger en sömlös introduktionsupplevelse för att samla in Prometheus-mått. Vanligtvis, för att använda Prometheus, måste du ställa in och hantera en Prometheus server med en butik. Genom att integrera med Azure Monitor krävs ingen Prometheus-server. Du behöver bara exponera Prometheus måttslutpunkt via dina exportörer eller poddar (program), och containerized agent för Azure Monitor för behållare kan skrapa mått för dig. 

![Container övervakning arkitektur för Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Den minsta agentversionen som stöds för att skrapa Prometheus-mått är ciprod07092019 eller senare, `KubeMonAgentEvents` och agentversionen som stöds för att skriva konfigurations- och agentfel i tabellen är ciprod10112019. Mer information om agentversionerna och vad som ingår i varje version finns i [agentreleaseanteckningar](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Om du vill verifiera agentversionen väljer du en nod på fliken **Nod** och i egenskapsfönstrets anteckningsvärde för egenskapen **Agent Image Tag.**

Skrapning av Prometheus-mått stöds med Kubernetes-kluster som finns på:

- Azure Kubernetes Service (AKS)
- Azure Stack eller lokalt
- Azure Red Hat OpenShift

>[!NOTE]
>För Azure Red Hat OpenShift skapas en konfigurationsfil för mall i namnområdet *openshift-azure-loggning.* Den är inte konfigurerad för att aktivt skrapa mått eller datainsamling från agenten.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure Red Hat OpenShift Förutsättningar

Innan du börjar bekräftar du att du är medlem i administratörsrollen För kundkluster i Azure Red Hat OpenShift-klustret för att konfigurera behållaragenten och Prometheus-skrapningsinställningarna. Om du vill kontrollera att du är medlem i gruppen *osa-customer-admins* kör du följande kommando:

``` bash
  oc get groups
```

Utdata kommer att likna följande:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Om du är medlem i *gruppen osa-customer-admins* bör `container-azm-ms-agentconfig` du kunna lista ConfigMap med följande kommando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Utdata kommer att likna följande:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Prometheus skrapning inställningar

Aktiv skrapning av mätvärden från Prometheus utförs från ett av två perspektiv:

* Klusteromfattande - HTTP-URL och identifiera mål från listade slutpunkter för en tjänst. K8s-tjänster som kube-dns och kube-state-metrics och pod-anteckningar som är specifika för ett program. Mått som samlas in i det här sammanhanget kommer att definieras i avsnittet ConfigMap *[Prometheus data_collection_settings.cluster]*.
* Nod hela - HTTP-URL och identifiera mål från listade slutpunkter för en tjänst. Mått som samlas in i det här sammanhanget kommer att definieras i avsnittet ConfigMap *[Prometheus_data_collection_settings.node]*.

| Slutpunkt | Omfång | Exempel |
|----------|-------|---------|
| Pod-anteckning | Hela kluster | Anteckningar: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Tjänsten Kubernetes | Hela kluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/slutpunkt | Per nod och/eller kluster-omfattande | `http://myurl:9101/metrics` |

När en URL har angetts skrapar Azure Monitor för behållare bara slutpunkten. När Kubernetes-tjänsten har angetts matchas tjänstnamnet med klustrets DNS-server för att hämta IP-adressen och sedan skrapas den matchade tjänsten.

|Omfång | Nyckel | Datatyp | Värde | Beskrivning |
|------|-----|-----------|-------|-------------|
| Hela kluster | | | | Ange någon av följande tre metoder för att skrapa slutpunkter för mått. |
| | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (Antingen IP-adress eller giltig URL-sökväg angiven). Till exempel: `urls=[$NODE_IP/metrics]`. ($NODE_IP är en specifik Azure Monitor för behållare parameter och kan användas i stället för nod IP-adress. Måste vara versaler.) |
| | `kubernetes_services` | Sträng | Kommaavgränsad matris | En matris med Kubernetes-tjänster för att skrapa mått från kube-state-metrics. Till exempel`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolesk | sant eller falskt | När azure `true` Monitor för behållare agent är inställd på i klusteromfattande inställningar skrapar kubernetes poddar över hela klustret för följande Prometheus-anteckningar:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolesk | sant eller falskt | Möjliggör skrapning av kapseln. `monitor_kubernetes_pods`måste ställas `true`in på . |
| | `prometheus.io/scheme` | Sträng | http eller https | Standarder till skrotning via HTTP. Om det behövs, ställ in på `https`. | 
| | `prometheus.io/path` | Sträng | Kommaavgränsad matris | HTTP-resurssökvägen som måtten ska hämtas från. Om sökvägen till `/metrics`mått inte är det definierar du den med den här anteckningen. |
| | `prometheus.io/port` | Sträng | 9102 | Ange en port som ska skrapas från. Om porten inte är inställd, kommer den som standard att 9102. |
| | `monitor_kubernetes_pods_namespaces` | Sträng | Kommaavgränsad matris | En tillåt lista över namnområden för att skrapa mått från Kubernetes poddar.<br> Till exempel, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Nod-bred | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (Antingen IP-adress eller giltig URL-sökväg angiven). Till exempel: `urls=[$NODE_IP/metrics]`. ($NODE_IP är en specifik Azure Monitor för behållare parameter och kan användas i stället för nod IP-adress. Måste vara versaler.) |
| Nod hela eller klusteromfattande | `interval` | Sträng | 60s | Standardinställningen för samlingsintervallet är en minut (60 sekunder). Du kan ändra samlingen för antingen *[prometheus_data_collection_settings.node]* och/eller *[prometheus_data_collection_settings.cluster]* till tidsenheter som s, m, h. |
| Nod hela eller klusteromfattande | `fieldpass`<br> `fielddrop`| Sträng | Kommaavgränsad matris | Du kan ange vissa mått som ska samlas in eller inte`fieldpass`från slutpunkten`fielddrop`genom att ange listan tillåt ( ) och inte tillåta ( ). Du måste ange listan tillåt först. |

ConfigMaps är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Du kan inte låta en annan ConfigMaps åsidosätta samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera konfigurationsfilen configmap för Kubernetes-kluster.

1. [Ladda ner](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) filen ConfigMap yaml och spara den som container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Det här steget krävs inte när du arbetar med Azure Red Hat OpenShift eftersom ConfigMap-mallen redan finns i klustret.

2. Redigera ConfigMap yaml-filen med dina anpassningar för att skrapa Prometheus-mått. Om du redigerar ConfigMap yaml-filen för Azure Red `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift kör du först kommandot för att öppna filen i en textredigerare.

    >[!NOTE]
    >Följande anteckning `openshift.io/reconcile-protect: "true"` måste läggas till under metadata för *container-azm-ms-agentconfig* ConfigMap för att förhindra avstämning. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Konfigurera ConfigMap-filen med hjälp av följande exempel om du vill samla in Kubernetes-tjänster i hela klustret.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Om du vill konfigurera skrapning av Prometheus-mått från en specifik URL i klustret konfigurerar du ConfigMap-filen med hjälp av följande exempel.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Om du vill konfigurera skrapning av Prometheus-mått från en agents DaemonSet för varje enskild nod i klustret konfigurerar du följande i ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP är en specifik Azure Monitor för behållare parameter och kan användas i stället för nod IP-adress. Det måste vara versaler. 

    - Så här konfigurerar du skrapning av Prometheus-mått genom att ange en pod-anteckning:

       1. I ConfigMap anger du följande:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Ange följande konfiguration för pod-anteckningar:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Om du vill begränsa övervakningen till specifika namnområden för poddar som har anteckningar, till exempel bara `monitor_kubernetes_pod` `true` inkludera poddar som är dedikerade `monitor_kubernetes_pods_namespaces` för produktionsarbetsbelastningar, ange till i ConfigMap och lägg till namnområdesfiltret som anger de namnområden som ska skrapas från. Till exempel, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. För andra kluster än Azure Red Hat OpenShift kör `kubectl apply -f <configmap_yaml_file.yaml>`du följande kubectl-kommando: .
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Spara ändringarna i redigeraren för Azure Red Hat OpenShift.

Konfigurationsändringen kan ta några minuter att slutföra innan den börjar gälla, och alla omsagent pods i klustret startar om. Omstarten är en rullande omstart för alla omsagent pods, inte alla startas samtidigt. När omstarterna är klara visas ett meddelande som liknar följande och som `configmap "container-azm-ms-agentconfig" created`innehåller resultatet: .

Du kan visa den uppdaterade ConfigMap för Azure Red `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Hat OpenShift genom att köra kommandot . 

## <a name="applying-updated-configmap"></a>Tillämpa uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap till klustret och vill uppdatera den med en nyare konfiguration kan du redigera configmap-filen som du tidigare har använt och sedan använda samma kommandon som tidigare.

För Kubernetes-kluster än Azure Red Hat OpenShift kör du kommandot `kubectl apply -f <configmap_yaml_file.yaml`. 

För Azure Red Hat OpenShift-klustret kör du kommandot för `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` att öppna filen i standardredigeraren för att ändra och sedan spara den.

Konfigurationsändringen kan ta några minuter att slutföra innan den börjar gälla, och alla omsagent pods i klustret startar om. Omstarten är en rullande omstart för alla omsagent pods, inte alla startas samtidigt. När omstarterna är klara visas ett meddelande som liknar följande och som `configmap "container-azm-ms-agentconfig" updated`innehåller resultatet: .

## <a name="verify-configuration"></a>Verifiera konfiguration

Om du vill kontrollera att konfigurationen har tillämpats på ett kluster använder `kubectl logs omsagent-fdf58 -n=kube-system`du följande kommando för att granska loggarna från en agentkapslar: . 

>[!NOTE]
>Det här kommandot gäller inte azure red hat openshift-kluster.
> 

Om det finns konfigurationsfel från omsagentkapslarna visas fel som liknar följande:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel relaterade till att tillämpa konfigurationsändringar är också tillgängliga för granskning. Följande alternativ är tillgängliga för ytterligare felsökning av konfigurationsändringar och skrapning av Prometheus-mått:

- Från en agent pod loggar `kubectl logs` med samma kommando 
    >[!NOTE]
    >Det här kommandot gäller inte azure red hat openshift-kluster.
    > 

- Från Live Data (förhandsgranskning). Live Data (förhandsversion) visar fel som liknar följande:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Från tabellen **KubeMonAgentEvents** i logganalysarbetsytan. Data skickas varje timme med *Varnings* allvarlighetsgrad för skrapfel och *fel* allvarlighetsgrad för konfigurationsfel. Om det inte finns några fel kommer posten i tabellen att ha data med allvarlighetsgrad *info*, som inte rapporterar några fel. Egenskapen **Tags** innehåller mer information om pod- och behållar-ID:t där felet uppstod och även den första förekomsten, den senaste förekomsten och antalet under den senaste timmen.

- För Azure Red Hat OpenShift kontrollerar du omsagentloggarna genom att söka i tabellen **ContainerLog** för att verifiera om loggsamling av openshift-azure-logging är aktiverad.

Fel förhindrar att omsagent tolkar filen, vilket gör att den startar om och använder standardkonfigurationen. NÃ¤r du har korrigerat felen i ConfigMap fÃ¤r klustrade fÃ¤r kÃ¤r än `kubectl apply -f <configmap_yaml_file.yaml`Azure Red Hat OpenShift sparar du yaml-filen och anpÃ¤nder de uppdaterade ConfigMaps genom att kÃ¤gga kommandot: . 

För Azure Red Hat OpenShift redigerar och sparar du de `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`uppdaterade ConfigMaps-kommandona genom att köra kommandot: .

## <a name="query-prometheus-metrics-data"></a>Frågetaletheus-mätdata

Om du vill visa prometheus-mått som skrapats av Azure Monitor och eventuella konfigurations-/skrapningsfel som rapporterats av agenten läser du [Fråge Prometheus-måttdata](container-insights-log-search.md#query-prometheus-metrics-data) och [Frågekonfiguration eller skrapningsfel](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Visa Prometheus-mått i Grafana

Azure Monitor for containers stöder visningsmått som lagras i logganalysarbetsytan i Grafana-instrumentpaneler. Vi har tillhandahållit en mall som du kan ladda ner från Grafanas [instrumentpanelsdatabas](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till dig för att lära dig hur du frågar ytterligare data från dina övervakade kluster för att visualisera i anpassade Grafana-instrumentpaneler. 

## <a name="review-prometheus-data-usage"></a>Granska Prometheus dataanvändning

För att identifiera inmatningsvolymen för varje måttstorlek i GB per dag för att förstå om den är hög, tillhandahålls följande fråga.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
Utdata visar resultat som liknar följande:

![Logga frågeresultat för datainmatningsvolym](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Om du vill uppskatta vilken måttstorlek i GB som är för en månad för att förstå om mängden data som tas emot på arbetsytan är hög, tillhandahålls följande fråga.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Utdata visar resultat som liknar följande:

![Logga frågeresultat för datainmatningsvolym](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Mer information om hur du övervakar dataanvändning och analyserar kostnader finns i [Hantera användning och kostnader med Azure Monitor Logs](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar agentsamlingsinställningarna för stdout-, stderr- och miljövariabler från behållararbetsbelastningar [här](container-insights-agent-config.md). 
