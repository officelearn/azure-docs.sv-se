---
title: Aks-poddar för automatisk skalning med Azure Application Gateway-mått
description: Den här artikeln innehåller instruktioner om hur du skalar dina AKS-backend-poddar med hjälp av mätvärden för Application Gateway och Azure Kubernetes Metric Adapter
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239453"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Skala aks-poddar automatiskt med hjälp av Beta (Application Gateway Metrics)

När inkommande trafik ökar blir det viktigt att skala upp dina program baserat på efterfrågan.

I följande självstudiekurs förklarar vi hur du `AvgRequestCountPerHealthyHost` kan använda Application Gateways mått för att skala upp ditt program. `AvgRequestCountPerHealthyHost`mäter genomsnittliga begäranden som skickas till en viss serverd-pool och serverda HTTP-inställningskombination.

Vi kommer att använda följande två komponenter:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Vi använder måttkortet för att exponera mätvärden för Application Gateway via måttservern. Azure Kubernetes Måttkort är ett projekt med öppen källkod under Azure, liknande programgateway-ingress-styrenheten. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Vi kommer att använda HPA för att använda Application Gateway-mått och rikta in dig på en distribution för skalning.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Konfigurera Azure Kubernetes-mätkort

1. Vi skapar först ett Azure AAD-tjänsthuvudnamn och tilldelar det `Monitoring Reader` åtkomst via Application Gateways resursgrupp. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Nu kommer vi [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) att distribuera med hjälp av AAD-tjänstens huvudnamn som skapats ovan.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Vi skapar `ExternalMetric` en resurs `appgw-request-count-metric`med namn . Den här resursen instruerar `AvgRequestCountPerHealthyHost` måttkortet att visa mått för `myApplicationGateway` resurs i `myResourceGroup` resursgruppen. Du kan `filter` använda fältet för att rikta in dig på en viss backend-pool och HTTP-inställning för bakåtsträvande i programgatewayen.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Du kan nu göra en begäran till måttservern för att se om vårt nya mått blir exponerat:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Använda det nya måttet för att skala upp distributionen

När vi har `appgw-request-count-metric` möjlighet att exponera via den [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) metriska servern är vi redo att använda för att skala upp vår måldistribution.

I följande exempel kommer vi `aspnet`att inrikta en exempeldistribution . Vi kommer att skala `appgw-request-count-metric` upp Pods när > 200 `10` per Pod upp till max pods.

Ersätt ditt måldistributionsnamn och tillämpa följande konfiguration för automatisk skalning:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Testa din inställning med hjälp av ett verktyg belastning test som apache bänk:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Nästa steg
- [**Felsöka problem med ingressstyrenheten:**](ingress-controller-troubleshoot.md)Felsök eventuella problem med ingressstyrenheten.