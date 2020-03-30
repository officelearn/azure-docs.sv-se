---
title: Distribuera en självvärd Azure API Management-gateway till Kubernetes | Microsoft-dokument
description: Lär dig hur du distribuerar en självvärd Azure API Management-gateway till Kubernetes
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513840"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Distribuera en självvärd Azure API Management-gateway till Kubernetes

Den här artikeln innehåller stegen för att distribuera självvärd azure API Management-gateway till ett Kubernetes-kluster.

> [!NOTE]
> Den självvärdbaserade gatewayfunktionen är i förhandsversion. Under förhandsversionen är den självvärdbaserade gatewayen endast tillgänglig på utvecklar- och premiumnivåerna utan extra kostnad. Utvecklarnivån är begränsad till en enda självvärd gateway-distribution.


## <a name="prerequisites"></a>Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa ett Kubernetes-kluster. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) är ett bra alternativ för utvecklings- och utvärderingsändamål. För produktionsarbetsbelastningar kan du använda [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) eller ett Kubernetes-kluster i ett utländskt moln.
- [Etablera en gatewayresurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Distribuera gatewayen till Kubernetes

1. Välj **Gateways** under **Inställningar**.
2. Välj den gatewayresurs som du tänker distribuera.
3. Välj **Distribution**.
4. Observera att en ny token i textrutan **Token** skapades automatiskt med standardvärdena **Förfallodatum** och **hemlig nyckel.** Justera antingen eller båda om så önskas och välj **Generera** för att skapa en ny token.
5. Kontrollera att **Kubernetes** är markerat under **Distributionsskript**.
6. Välj **<gateway-namn>.yml** fillänk **bredvid Distribution** för att hämta filen.
7. Justera portmappningarna och behållarnamnet i yml-filen efter behov.
8. Markera **kopieringsikonen** som finns till höger i `kubectl` textrutan **Distribuera** för att spara kommandot i Urklipp. 
9. Klistra in kommandot i terminalfönstret (eller kommandofönstret). Observera att kommandot förväntar sig att den hämtade miljöfilen ska finnas i den aktuella katalogen.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Kör kommandot. Kommandot instruerar Kubernetes-klustret att köra behållaren med hjälp av självvärderade gatewayavbildningar som hämtats från Microsoft Container-registret och att konfigurera behållaren så att HTTP-portarna (8080) och HTTPS (443) ska exponeras.
11. Kör kommandot nedan för att kontrollera att gateway-podden körs. Observera att ditt pod-namn kommer att vara annorlunda. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Kör kommandot nedan för att kontrollera att gateway-tjänsten körs. Observera att ditt tjänstnamn kommer att vara annorlunda. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Gå tillbaka till Azure-portalen och bekräfta att gateway-nod som du just distribuerade rapporterar felfritt.

![gateway-status](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Använd <code>kubectl logs <gateway-pod-name></code> kommandot för att visa en ögonblicksbild av självvärdbaserad gatewaylogg.

## <a name="next-steps"></a>Nästa steg

* Mer information om den självvärderade gatewayen finns i [Azure API Management självvärderade gateway översikt](self-hosted-gateway-overview.md)
* Läs mer om [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


