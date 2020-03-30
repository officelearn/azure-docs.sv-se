---
title: Azure Kubernetes nätverksprinciper | Microsoft-dokument
description: Lär dig mer om Kubernetes nätverksprinciper för att skydda kubernetes-klustret.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159691"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Översikt över nätverksprinciper för Azure Kubernetes

Nätverksprinciper ger mikrosegmentering för poddar precis som NSG (Network Security Groups) ger mikrosegmentering för virtuella datorer. Azure Network Policy-implementeringen stöder standardspecifikationen för Kubernetes Network Policy. Du kan använda etiketter för att välja en grupp poddar och definiera en lista med regler för in- och utgående som anger vilken typ av trafik som tillåts till och från dessa poddar. Läs mer om Kubernetes nätverksprinciper i [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Översikt över Kubernetes-nätverksprinciper](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure-nätverksprinciper fungerar tillsammans med Azure CNI som tillhandahåller VNet-integrering för behållare. Det stöds endast på Linux-noder idag. Implementeringarna konfigurerar Linux IP-tabellregler baserat på definierade principer för att framtvinga trafikfiltrering.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planera säkerhet för kubernetes-klustret
När du implementerar säkerhet för klustret använder du nätverkssäkerhetsgrupper (NSG) för att filtrera trafik mellan nord och syd, det vill än trafik som kommer in i och lämnar klustret och använda Kubernetes-nätverksprinciper för öst-västlig trafik, det vill än trafik mellan poddar i klustret.

## <a name="using-azure-kubernetes-network-policies"></a>Använda Azure Kubernetes nätverksprinciper
Azure Network Policies kan användas på följande sätt för att tillhandahålla mikrosegmentering för poddar.

### <a name="acs-engine"></a>ACS-motor
ACS-Engine är ett verktyg som genererar en Azure Resource Manager-mall för distributionen av ett Kubernetes-kluster i Azure. Klusterkonfigurationen anges i en JSON-fil som överförs till verktyget när mallen skapas. Läs mer om hela listan med klusterinställningar som stöds och beskrivningar av dessa i Microsoft Azure Container Service Engine - Cluster Definition.

Om du vill aktivera principer för kluster som distribueras med acs-engine anger du värdet för networkPolicy-inställningen i klusterdefinitionsfilen som ska vara "azure".

#### <a name="example-configuration"></a>Exempelkonfiguration

Nedanstående JSON-exempelkonfiguration skapar ett nytt virtuellt nätverk och undernät och distribuerar ett Kubernetes-kluster i det med Azure CNI. Vi rekommenderar att du använder "Anteckningar" för att redigera JSON-filen. 
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
Implementeringen kan användas för att tillhandahålla nätverksprinciper för poddar i Kubernetes-kluster som du distribuerar själv, utan att förlita dig på verktyg som ACS-engine. I det här fallet installerar du först CNI-plugin-programmet och aktiverar det på alla virtuella datorer i ett kluster. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för ett Kubernetes-kluster som du distribuerar själv](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

När klustret har `kubectl` distribuerats kör följande kommando för att hämta och tillämpa Azure-nätverksprincipdemonseten i *klustret.*

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Lösningen är också öppen källkod och koden är tillgänglig i [Azure Container Networking repository](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Lär dig mer om [behållarnätverk](container-networking-overview.md).
- [Distribuera plugin-programmet](deploy-container-networking.md) för Kubernetes-kluster eller Docker-behållare.
