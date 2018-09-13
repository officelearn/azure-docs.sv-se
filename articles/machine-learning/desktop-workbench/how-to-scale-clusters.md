---
title: Så här skalar du ett Azure Container Service-kluster för Machine Learning | Microsoft Docs
description: Skala ett ACS-kluster – automatisk skalning och statiska skalning; skalning av antalet noder i klustret
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: e547d778ebf34b55c0c18921cf28e2a78fd269cc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647213"
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Skala kluster för att hantera web service dataflöde

## <a name="why-scale-the-cluster"></a>Varför skala klustret?

Skala kluster för Azure Container Service (ACS) är ett effektivt sätt att optimera dataflödet service samtidigt som klusterstorleken till ett minimum för att minska kostnaderna. 

För att bättre förstå automatisk skalning, Överväg följande exempel visar ett kluster som kör tre webbtjänster:

![Exempel: Tre tjänster i ett kluster](media/how-to-scale-clusters/three-services.png)

Tjänsterna har olika toppefterfrågan: Service 1 (blå linje) kräver 40 poddar vid toppar i efterfrågan, Service 2 (orange rad) kräver 38 högt belastad och Service 3 (grå linje) kräver 50 högt belastad. Om du reservera nödvändiga högsta kapacitet för varje tjänst individuellt, det här klustret måste minst 40 + 38 + 50 = 128 Totalt antal poddar.

Men överväga faktiska pod-användning när som helst i tid, representeras av den svarta streckad linjen i diagrammet. I det här fallet den *högsta antalet poddar som används vid något tillfälle* är 64, som inträffar vid 20:00 när Service 3 är högt belastad. Service 3 använder 50 poddar just nu, men Service 2 används bara 9 poddar och Service 1 använder endast 5. Kom ihåg att det här är *högsta användning* för det här klustret. Det innebär att vid något tillfälle klustret använder fler än 64 poddar--halva beräknade krav på 128 poddarna för de tre tjänsterna skalas oberoende för högsta användning.

För att uppfylla den aktuella efterfrågan för varje tjänst i stället för att helt enkelt kräver tillräckligt med resurser för belastning för alla tjänster, genom att tilldela om poddar i klustret – det vill säga genom att skalas om--kan du minska klusterstorlek. I det här enkla exemplet minskar autoskalning det begärda antalet poddar från 128 till 64, klippa ut krävs klusterstorleken i hälften.

Skala antalet poddar är en relativt snabbt åtgärd som kräver mindre än en minut, så att tjänstens tillgänglighet inte påverkas allvarligt.

> [!NOTE]
> Det hjälper inte att skala ett kluster med begäran svarstidsproblem. För driftsättning, bör skala upp öka antalet lyckade och minska tjänsten inte är tillgänglig. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Så här skalar du webbtjänster på ACS-kluster

Installationsalternativet kluster för modellhantering CLI som standard konfigurerar två agenter och en pod i din miljö. Den installerar även Kubernetes CLI.

Du kan skala webbtjänster som du har distribuerat till ACS genom att justera:

* Antalet agentnoder i klustret
* Antalet Kubernetes pod replikerna som körs på agentnoder

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Skalning av antalet noder i klustret

Följande kommando anger antalet agentnoder i klustret:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Det här kan ta några minuter. Läs mer om att skala antalet noder i klustret, [skala agentnoder i ett Container Service-kluster](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Skalning av antalet poddrepliker för Kubernetes i ett kluster
 
Du kan skala antalet poddrepliker som tilldelats till klustret med Azure Machine Learning CLI eller [Kubernetes-instrumentpanelen] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Mer information om Kubernetes-poddar repliken finns i den [Kubernetes-Poddar](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentation.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Skala ett kluster med Azure Machine Learning CLI

Det finns två sätt att skala ett kluster med hjälp av CLI:

- Automatisk skalning
- Statisk skala

Automatisk skalning är aktiv som standard när tjänsten har skapats och i de flesta fall är den bästa metoden för skalning.

##### <a name="autoscale"></a>Automatisk skalning

Följande kommando aktiverar automatisk skalning och anger minsta och största antalet repliker för tjänsten.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Exempelvis anger `autoscale-min-replicas` till 5 skapar fem repliker. Ange numret till värden, till exempel 10 för att komma fram till ett nummer som är optimala för webbtjänsten, och övervaka antalet 503 felmeddelanden. Justera sedan antalet på lämpligt sätt.


| Parameternamn | Typ | Beskrivning |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | boolesk | Anger om autoskalning är aktiverat. Standard: SANT |
| `autoscale-min-replicas` | heltal | Anger det minsta antalet poddar. Måste vara 0 eller högre. Standard: 1 |
| `autoscale-max-replicas` | heltal | Anger det maximala antalet poddar. Måste vara minst 1. Om automatisk skalning max repliker är mindre än Autoskala minsta antal repliker, kommer automatisk skalning max repliker att ignoreras. Standard: 10 |
| `autoscale-refresh-period-seconds` | heltal | Anger hur länge i sekunder mellan uppdateringar för automatisk skalning. Standard: 1 |
| `autoscale-target-utilization` | heltal | Anger den procent belastningen som mål för automatisk skalning, mellan 1 och 100. Standard: 70 |

Automatisk skalning fungerar för att säkerställa att följande villkor:

1. Målanvändning uppfylls
2. Skala aldrig överskrider de lägsta och högsta inställningarna

Tjänster i ett kluster konkurrerar om klusterresurser. Tjänstens autoscaled ökar dess kluster Resursanvändning som begäranden per sekund (RPS) och kommer att släppa resurser när RPS minskar långsamt. Klusterresurser ska förvärvas på begäran så länge som sådana resurser som finns att hämta-tjänsten.

Mer information om hur du använder automatisk skalning parametrar finns i den [referens för objektmodell Management Command Line Interface](model-management-cli-reference.md) dokumentation.

##### <a name="static-scale"></a>Statisk skala

I allmänhet bör statiska skalning undvikas, eftersom den inte tillåter att klustret storlek minskning av automatisk skalning. Men det är möjligt, i vissa fall kan statiska skalning vara bäst. Till exempel när ett kluster är dedikerad till en enda tjänst, tillhandahåller automatisk skalning inga fördelar; alla klusterresurser ska tilldelas till den tjänsten.

För att statiskt skala ett kluster, måste det inaktiveras automatisk skalning. Inaktivera automatisk skalning med följande kommando:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

När du inaktiverar automatisk skalning skalar kommandot direkt antalet repliker för en tjänst.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Mer information om att skala antalet noder i klustret finns i skala agentnoder i ett Container Service-kluster.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Skala antalet repliker med hjälp av Kubernetes-instrumentpanelen

Ange följande i Kommandotolken:

```
kubectl proxy
```

På Windows läggs Kubernetes-installationsplatsen inte automatiskt till sökvägen. Först gå till installationsmappen:

```
c:\users\<user name>\bin
```

När du kör kommandot bör du se följande meddelande:

```
Starting to serve on 127.0.0.1:8001
```

Om porten används redan, visas ett meddelande som liknar följande exempel:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Du kan ange en alternativ port nummer med hjälp av den *--port* parametern.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

När du har startat instrumentpanelsserver, öppna en webbläsare och ange följande URL:

```
127.0.0.1:<port number>/ui
```

Från huvudskärmen instrumentpanelen, klickar du på **distributioner** på det vänstra navigeringsfältet. Om inte visas i navigeringsfönstret, Välj den här ikonen ![menyn som består av tre korta vågräta linjer](media/how-to-scale-clusters/icon-hamburger.png) på det övre vänstra hörnet.

Leta upp distributionen för att ändra och klicka på ikonen ![menyikonen som består av tre lodräta punkterna](media/how-to-scale-clusters/icon-kebab.png) till höger och klicka sedan på **visa/redigera YAML**.

På skärmen redigera distribution letar du upp den *spec* nod, ändra den *repliker* värdet och klicka på **uppdatering**.
