---
title: Distribuera en Azure API Management-Gateway med egen värd till Kubernetes | Microsoft Docs
description: Lär dig hur du distribuerar en Azure API Management Gateway med egen värd till Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513840"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Distribuera en Azure API Management-Gateway med egen värd till Kubernetes

Den här artikeln innehåller steg för att distribuera Azure API Management Gateway med egen värd till ett Kubernetes-kluster.

> [!NOTE]
> Funktionen för lokal gateway är i för hands version. Under för hands versionen är den egna värdbaserade gatewayen bara tillgänglig på utvecklings-och Premium-nivåerna utan extra kostnad. Developer-nivån är begränsad till en enda lokal gateway-distribution.


## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa ett Kubernetes-kluster. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) är ett lämpligt alternativ för utvecklings-och utvärderings syfte. För produktions arbets belastningar kan du använda [Azure Kubernetes-tjänsten](https://azure.microsoft.com/services/kubernetes-service/) eller ett Kubernetes-kluster i ett externt moln.
- [Etablera en gateway-resurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Distribuera gatewayen till Kubernetes

1. Välj **gateways** från **Inställningar**.
2. Välj den gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. Observera att en ny token i text rutan **token** genererades automatiskt när du använder standard värdena för **förfallo datum** och **hemlig nyckel** . Justera antingen eller båda om du vill och välj **generera** för att skapa en ny token.
5. Kontrol lera att **Kubernetes** är markerat under **distributions skript**.
6. Hämta filen genom att välja **< Gateway-name >. yml-** fillänk bredvid **distribution** .
7. Justera port mappningarna och behållar namnet i YML-filen efter behov.
8. Välj **kopierings** ikonen längst till höger i text rutan **distribuera** för att spara `kubectl` kommandot i Urklipp. 
9. Klistra in kommandot i terminalfönstret (eller kommando fönstret). Observera att kommandot förväntar sig att den nedladdade miljö filen finns i den aktuella katalogen.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Kör kommandot. Kommandot instruerar ditt Kubernetes-kluster att köra behållaren, med hjälp av egen värdbaserade gatewayens avbildning som hämtats från Microsoft Container Registry och för att konfigurera behållaren för att exponera HTTP (8080) och HTTPS (443)-portar.
11. Kör kommandot nedan för att kontrol lera att Gateway-Pod körs. Observera att ditt Pod-namn är annorlunda. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Kör kommandot nedan för att kontrol lera att Gateway-tjänsten körs. Observera att namnet på din tjänst är annorlunda. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Gå tillbaka till Azure Portal och kontrol lera att Gateway-noden som du precis har distribuerat rapporterar felfri status.

![Gateway-status](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Använd <code>kubectl logs <gateway-pod-name></code> kommando om du vill visa en ögonblicks bild av Gateway-loggen med egen värd.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Läs mer om [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/intro-kubernetes)


