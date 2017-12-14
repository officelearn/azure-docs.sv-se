---
title: "Så här skalar du ett Azure Container Service-kluster för Machine Learning | Microsoft Docs"
description: Skala en ACS-kluster - Autoskala och statiska skalning; skala antalet noder i klustret
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 44aa167375355433851453010cebe5b49ef56ebd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Skalning av klustret för att hantera web service genomflöde

## <a name="why-scale-the-cluster"></a>Varför skala klustret?

Skala Azure Container Service (ACS)-klustret är ett effektivt sätt att optimera genomflödet service samtidigt som klusterstorleken så mycket som möjligt att minska kostnaden. 

Överväg följande exempel på ett kluster som kör tre webbtjänster för att bättre förstå autoskalning kan:

![Exempel: Tre tjänster i ett kluster](media/how-to-scale-clusters/three-services.png)

Tjänsterna har olika krav för belastning: Service 1 (blå linje) kräver 40 skida på begäran för belastning, Service-2 (orange rad) kräver 38 på belastning och Service 3 (grå rad) kräver 50 på belastning. Om du reservera nödvändiga högsta kapacitet för varje tjänst individuellt detta kluster behöver minst 40 + 38 + 50 = 128 totalt skida.

Men kom ihåg att faktiska baljor användning när som helst i tid, visas med svart streckad linje i diagrammet. I det här fallet den *högsta antalet skida som används vid något tillfälle* är 64, som inträffar vid 20:00 när Service 3 är på belastning. Service 3 använder 50 skida just nu, men Service 2 används bara 9 skida och Service 1 använder endast 5. Kom ihåg att detta är *högsta användningsnivå* för det här klustret. Det innebär att vid något tillfälle klustret använder fler än 64 skida--halva beräknade krav på 128 skida för de tre tjänsterna skalas oberoende för maximal användning.

För att uppfylla aktuella behovet för varje tjänst i stället för att bara kräver tillräckligt med resurser för belastning behovet av alla tjänster genom att tilldela skida i klustret – det vill säga genom rescaling--minska klustrets storlek. I det här enkla exemplet minskar autoskalning antalet skida 128 64, klippa ut krävs klusterstorleken i hälften som krävs.

Skala antalet skida är en relativt snabbt åtgärd som kräver mindre än en minut så tjänstens tillgänglighet inte påverkas allvarligt.

> [!NOTE]
> Skala ett kluster hjälper inte med begäran problem med nätverkssvarstiden. För operationalization, bör skala upp öka antalet lyckade och minska tjänsten inte är tillgänglig. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Så här skalar webbtjänster på ACS-kluster

Alternativet klustret installationen av modellhantering av CLI som standard konfigurerar två agenter och en baljor i din miljö. Den installeras också Kubernetes CLI.

Du kan skala webbtjänster som du har distribuerat till ACS genom att justera:

* Antalet agent noder i klustret
* Antal Kubernetes baljor repliker som körs på agenten noder

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Skala antalet noder i klustret

Följande kommando anger räkningen på agenten noder i klustret:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Detta tar några minuter att slutföra. Mer information om att skala antalet noder i klustret finns [skala agent noder i ett Container Service-kluster](https://docs.microsoft.com/en-us/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Skala antalet Kubernetes baljor repliker i ett kluster
 
Du kan skala antalet baljor repliker som är tilldelade till klustret med hjälp av Azure Machine Learning CLI eller [Kubernetes instrumentpanelen] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Mer information om Kubernetes replik skida finns i [Kubernetes skida](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentation.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Skala ett kluster med Azure Machine Learning CLI

Det finns två sätt att skala ett kluster med hjälp av CLI:

- Automatisk skalning
- Statisk skala

Autoskalningsfunktionen är aktiv som standard när tjänsten har skapats och i de flesta fall är den bästa skalning.

##### <a name="autoscale"></a>Automatisk skalning

Följande kommando aktiverar automatisk skalning och anger lägsta och högsta antalet repliker för tjänsten.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Ange till exempel `autoscale-min-replicas` 5 skapas fem repliker. Ange hur många värden, till exempel 10 ska komma till ett optimalt nummer för webbtjänsten och övervaka antalet 503 felmeddelanden. Justera sedan antalet därefter.


| Parameternamn | Typ | Beskrivning |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Booleskt värde | Anger om Autoskala är aktiverad. Standard: true |
| `autoscale-min-replicas` | heltal | Anger det minsta antalet skida. Måste vara 0 eller större. Standard: 1 |
| `autoscale-max-replicas` | heltal | Anger det maximala antalet skida. Måste vara 1 eller högre. Om Autoskala-max-repliker är mindre än Autoskala-min-repliker kommer Autoskala-max-repliker att ignoreras. Standard: 10 |
| `autoscale-refresh-period-seconds` | heltal | Anger hur länge i sekunder mellan automatiska uppdateringar. Standard: 1 |
| `autoscale-target-utilization` | heltal | Anger procent användning som Autoskala mål, mellan 1 och 100. Standard: 70 |

Autoskala fungerar för att se till att följande villkor:

1. Mål-användning är uppfyllt.
2. Skalning aldrig överskrider de lägsta och högsta inställningarna

Tjänster i ett kluster konkurrerar om klusterresurser. En autoscaled tjänst ökar dess klustret Resursanvändning som begäranden per andra (RPS) ökar och släpper långsamt resurser när RPS minskar. Klusterresurser kommer förvärvas på begäran så länge dessa resurser finns för tjänsten för att hämta.

Mer information om hur du använder parametrarna Autoskala finns i [modellen Management kommandoradsgränssnittet referens](model-management-cli-reference.md) dokumentation.

##### <a name="static-scale"></a>Statisk skala

I allmänhet bör statiska skalning undvikas, eftersom det inte tillåter att klustret storlek minska autoskalning. Trots detta i vissa situationer kan statiska skalning vara bäst. Till exempel när ett kluster som är dedikerad till en enskild tjänst, ger autoskalning inga fördelar; alla klusterresurser ska tilldelas till den tjänsten.

För att skala ett kluster statiskt måste autoskalning stängas av. Inaktivera Autoskala med följande kommando:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

När du inaktiverar Autoskala skalas kommandot direkt antalet repliker för en tjänst.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Mer information om att skala antalet noder i klustret finns i skala agent noder i ett Container Service-kluster.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Skalning antal repliker med hjälp av instrumentpanelen Kubernetes

Ange följande på kommandoraden:

```
kubectl proxy
```

I Windows läggs installationsplatsen Kubernetes automatiskt inte till sökvägen. Först gå till mappen installera:

```
c:\users\<user name>\bin
```

När du kör kommandot, bör du se följande meddelande:

```
Starting to serve on 127.0.0.1:8001
```

Om porten används redan, visas ett meddelande som liknar följande exempel:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Du kan ange en alternativ port number med hjälp av den *--port* parameter.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

När du har startat instrumentpanelen servern, öppna en webbläsare och ange följande URL:

```
127.0.0.1:<port number>/ui
```

Från huvudskärmen instrumentpanelen, klickar du på **distributioner** i det vänstra navigeringsfältet. Om inte visas i navigeringsfönstret, Välj den här ikonen ![menyn som består av tre korta vågräta linjer](media/how-to-scale-clusters/icon-hamburger.png) längst upp till vänster.

Hitta distributionen för att ändra och klicka på ikonen ![menyn ikon som består av tre lodräta punkter](media/how-to-scale-clusters/icon-kebab.png) till höger och klicka sedan på **visa/edi YAML**.

Leta upp på skärmen för distribution av redigera den *spec* nod, ändra den *repliker* värdet och klicka på **uppdatering**.
