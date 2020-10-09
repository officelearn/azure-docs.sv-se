---
title: Nätverks principer för Azure Kubernetes | Microsoft Docs
description: Lär dig mer om Kubernetes-nätverks principer för att skydda ditt Kubernetes-kluster.
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
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "73159691"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Översikt över nätverksprinciper för Azure Kubernetes

Nätverks principer ger Micro-segmentering för poddar precis som nätverks säkerhets grupper (NSG: er) tillhandahåller mikrosegmentering för virtuella datorer. Implementeringen av Azure Network Policy stöder standard principen för Kubernetes-nätverks principer. Du kan använda etiketter för att välja en grupp med poddar och definiera en lista med ingångs-och utgångs regler som anger vilken typ av trafik som tillåts och från dessa poddar. Läs mer om principerna för Kubernetes-nätverk i [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Översikt över Kubernetes-nätverks principer](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure nätverks principer fungerar tillsammans med Azure-CNI som tillhandahåller VNet-integrering för behållare. Den stöds bara på Linux-noder idag. Implementeringarna konfigurerar Linux IP Table-regler baserat på de definierade principerna för att tillämpa trafik filtrering.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planera säkerhet för ditt Kubernetes-kluster
När du implementerar säkerheten för klustret använder du nätverks säkerhets grupper (NSG: er) för att filtrera North-South trafik, det vill säga trafik som registrerar och lämnar ditt kluster under nät och använder Kubernetes nätverks principer för East-West trafik, det vill säga trafik mellan poddar i klustret.

## <a name="using-azure-kubernetes-network-policies"></a>Använda nätverks principer i Azure Kubernetes
Azures nätverks principer kan användas på följande sätt för att tillhandahålla mikrosegmentering för poddar.

### <a name="acs-engine"></a>ACS-motor
ACS-Engine är ett verktyg som genererar en Azure Resource Manager-mall för distributionen av ett Kubernetes-kluster i Azure. Klusterkonfigurationen anges i en JSON-fil som överförs till verktyget när mallen skapas. Läs mer om hela listan med klusterinställningar som stöds och beskrivningar av dessa i Microsoft Azure Container Service Engine - Cluster Definition.

Om du vill aktivera principer för kluster som distribueras med ACS-Engine anger du värdet för inställningen networkPolicy i kluster definitions filen som ska vara "Azure".

#### <a name="example-configuration"></a>Exempelkonfiguration

Konfigurationen för följande JSON-exempel skapar ett nytt virtuellt nätverk och undernät och distribuerar ett Kubernetes-kluster i det med Azure CNI. Vi rekommenderar att du använder anteckningar för att redigera JSON-filen. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Skapa ditt eget Kubernetes-kluster i Azure
Implementeringen kan användas för att tillhandahålla nätverks principer för poddar i Kubernetes-kluster som du distribuerar själv, utan att förlita dig på verktyg som ACS-Engine. I det här fallet installerar du först CNI-plugin-programmet och aktiverar det på varje virtuell dator i ett kluster. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för ett Kubernetes-kluster som du distribuerar själv](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

När klustret har distribuerats kör du följande `kubectl` kommando för att ladda ned och tillämpa Azure Network Policy- *daemonset* i klustret.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Lösningen är också öppen källa och koden är tillgänglig på [Azure Container Networking-lagringsplatsen](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Kubernetes-tjänsten](../aks/intro-kubernetes.md).
-  Lär dig mer om [behållar nätverk](container-networking-overview.md).
- [Distribuera plugin-programmet](deploy-container-networking.md) för Kubernetes-kluster eller Docker-behållare.
