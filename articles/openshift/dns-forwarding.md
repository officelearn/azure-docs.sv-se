---
title: Konfigurera DNS-vidarebefordran för Azure Red Hat OpenShift 4
description: Konfigurera DNS-vidarebefordran för Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82232640"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurera DNS-vidarebefordran i ett Azure Red Hat OpenShift 4-kluster

Om du vill konfigurera DNS-vidarebefordran i ett Azure Red Hat OpenShift-kluster måste du ändra DNS-operatorn. Den här ändringen gör att ditt program poddar körs i klustret för att matcha namn som finns på en privat DNS-server utanför klustret. De här stegen är dokumenterade för OpenShift 4,3 [här](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Om du till exempel vill vidarebefordra alla DNS-begäranden för *. example.com för att lösas av en DNS-Server 192.168.100.10 kan du redigera operatörs konfigurationen genom att köra:
 
```bash
oc edit dns.operator/default
```
 
Då startas en redigerare och du kan ersätta `spec: {}` med:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Spara filen och avsluta redigeraren.

## <a name="next-steps"></a>Nästa steg
Läs mer information om DNS-vidarebefordran för OpenShift 4,3 [här](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).