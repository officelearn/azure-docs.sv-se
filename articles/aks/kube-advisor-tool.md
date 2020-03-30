---
title: Kontrollera dina Kubernetes-distributioner på Azure för implementering av metodtips
description: Lär dig hur du söker efter implementering av metodtips i dina distributioner på Azure Kubernetes Service med kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 29ea7dba1df8bc7c68e3d17563a51b784ce4a561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595441"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Söka efter Kubernetes metodtips i ditt kluster

Det finns flera metodtips som du bör följa på dina Kubernetes-distributioner för att säkerställa bästa prestanda och återhämtning för dina program. Du kan använda verktyget kube-advisor för att leta efter distributioner som inte följer dessa förslag.

## <a name="about-kube-advisor"></a>Om kube-advisor

[Verktyget kube-advisor][kube-advisor-github] är en enda behållare som är utformad för att köras i klustret. Den frågar Kubernetes API-server för information om dina distributioner och returnerar en uppsättning föreslagna förbättringar.

Verktyget kube-advisor kan rapportera om resursbegäran och gränser som saknas i PodSpecs för Windows-program samt Linux-program, men själva kube-advisor-verktyget måste schemaläggas på en Linux-pod. Du kan schemalägga en pod så att den körs på en nodpool med ett visst operativsystem med hjälp av en [nodväljare][k8s-node-selector] i podns konfiguration.

> [!NOTE]
> Verktyget kube-advisor stöds av Microsoft på bästa sätt. Problem och förslag bör arkiveras på GitHub.

## <a name="running-kube-advisor"></a>Köra kube-rådgivare

Så här kör du verktyget i ett kluster som är konfigurerat för [rollbaserad åtkomstkontroll (RBAC)](azure-ad-integration.md)med hjälp av följande kommandon. Det första kommandot skapar ett Kubernetes-tjänstkonto. Det andra kommandot kör verktyget i en pod med det tjänstkontot och konfigurerar podden för borttagning när den har avslutats. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Om du inte använder RBAC kan du köra kommandot på följande sätt:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Inom några sekunder bör du se en tabell som beskriver potentiella förbättringar av dina distributioner.

![Utdata för Kube-rådgivare](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Utförda kontroller

Verktyget validerar flera kubernetes bästa praxis, var och en med sina egna föreslagna reparation.

### <a name="resource-requests-and-limits"></a>Resursbegäranden och -gränser

Kubernetes stöder definition av [resursbegäranden och begränsningar för pod-specifikationer][kube-cpumem]. Begäran definierar den minsta CPU och minne som krävs för att köra behållaren. Gränsen definierar den maximala cpu och minne som ska tillåtas.

Som standard anges inga begäranden eller gränser på pod-specifikationer. Detta kan leda till att noder överplaneras och att behållare svälter. Verktyget kube-advisor belyser poddar utan begäranden och gränser.

## <a name="cleaning-up"></a>Rensa

Om klustret har aktiverat RBAC kan `ClusterRoleBinding` du rensa när du har kört verktyget med följande kommando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Om du kör verktyget mot ett kluster som inte är RBAC-aktiverat krävs ingen rensning.

## <a name="next-steps"></a>Nästa steg

- [Felsöka problem med Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors