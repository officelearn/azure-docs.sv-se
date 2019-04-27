---
title: Azure Kubernetes nätverksprinciper | Microsoft Docs
description: Lär dig om Kubernetes nätverksprinciper för att skydda ditt Kubernetes-kluster.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a5c367402bd1e61485095fd1d565a8582acc3a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824903"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes principer översikt över nätverk

Nätverksprinciper tillhandahåller mikro-segmentering för poddar precis som Nätverkssäkerhetsgrupper (NSG) tillhandahåller mikro-segmentering för virtuella datorer. Azure Network Policy-implementeringen stöder standard Kubernetes nätverkspolicy-specifikationen. Du kan använda etiketter för att välja en grupp med poddar och definiera en lista över ingående och utgående regler som anger vilken typ av trafik som tillåts till och från dessa poddar. Mer information om Kubernetes-nätverksprinciper i den [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Kubernetes översikt över principer](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure-nätverksprinciper fungerar tillsammans med Azure-CNI som tillhandahåller VNet-integrering för behållare. Den stöds endast på Linux-noder i dag. Implementeringarna konfigurera Linux IP-Adresstabell regler baserat på definierade principer för att tvinga trafikfiltrering.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planera säkerhet för Kubernetes-klustret
När du implementerar säkerhet för ditt kluster, Använd nätverkssäkerhetsgrupper (NSG) för att filtrera trafik Nord-sydlig, det vill säga trafik att ange och lämnar din kluster-undernät och Använd Kubernetes nätverksprinciper för öst-väst-trafik, som är. trafik mellan poddar i klustret.

## <a name="using-azure-kubernetes-network-policies"></a>Med hjälp av Azure Kubernetes-nätverksprinciper
Azure nätverksprinciper kan användas på följande sätt att tillhandahålla mikro-segmentering för poddar.

### <a name="acs-engine"></a>ACS-engine
ACS-Engine är ett verktyg som genererar en Azure Resource Manager-mall för distribution av ett Kubernetes-kluster i Azure. Klusterkonfigurationen anges i en JSON-fil som överförs till verktyget när mallen skapas. Läs mer om hela listan över klusterinställningar som stöds och deras beskrivningar i Microsoft Azure Container Service Engine - Klusterdefinitionen.

Om du vill aktivera principer på kluster som distribueras med acs-motor, ange värdet för inställningen networkPolicy i definitionsfilen för klustret är ”azure”.

#### <a name="example-configuration"></a>Exempelkonfiguration

Den nedan JSON exempelkonfiguration skapar ett nytt virtuellt nätverk och undernät och distribuerar ett Kubernetes-kluster i den med Azure CNI. Vi rekommenderar att du använder ”anteckningar” så här redigerar du JSON-filen. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Skapa din egen Kubernetes-kluster i Azure
Implementeringen kan användas för att tillhandahålla nätverksprinciper för Poddar i Kubernetes-kluster som du distribuerar själv, utan att behöva verktyg som ACS-Engine. I detta fall använder du först installera CNI plugin-programmet och aktivera det på alla virtuella datorer i ett kluster. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för ett Kubernetes-kluster som du distribuerar själv](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

När klustret har distribuerats genom att köra följande `kubectl` kommando för att ladda ned och installera Azure nätverksprincipen *daemonset* till klustret.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Lösningen är också med öppen källkod och koden är tillgängligt på den [Azure behållare nätverk databasen](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Lär dig mer om [behållarnätverk](container-networking-overview.md).
- [Distribuera plugin-programmet](deploy-container-networking.md) för Kubernetes-kluster eller Docker-behållare.