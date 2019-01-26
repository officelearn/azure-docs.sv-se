---
title: Metodtips för Azure Service Fabric-program och -kluster | Microsoft Docs
description: Metodtips för hantering av Service Fabric-kluster och program.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 06240ac08a12b67e95b4cb9b9a33fcca32de45a8
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914115"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Metodtips för Azure Service Fabric-program och -kluster

Om du vill hantera Azure Service Fabric-program och -kluster har finns det åtgärder som vi rekommenderar starkt att du utför för att optimera för tillförlitligheten i din produktionsmiljö; utföra åtgärder som definierats i det här dokumentet och välj en av våra [mallar för Azure-exempel Service Fabric-kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates) börjar utforma din lösning för produktion eller ändra befintliga mallen om du vill lägga till dessa metoder.

## <a name="security"></a>Säkerhet 

* [Metodtips för säkerhet](service-fabric-best-practices-security.md)

## <a name="networking"></a>Nätverk

* [Metodtips för nätverk](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planera för beräkning och skalning

* [Metodtips för att skala beräkning](service-fabric-best-practices-capacity-scaling.md)
* [Compute-kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktur som kod

* [Metodtips för att implementera infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

* [Metodtips för klustret övervakning och diagnostik](service-fabric-best-practices-monitoring.md)

## <a name="checklist"></a>Checklista

När du har slutfört alla avsnitt ovan, se till att du har integrerat alla av de bästa metoderna i checklistan för produktion-beredskap:
* [Checklista för Azure Service Fabric produktion beredskap](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsökning: [Felsökningsguide för Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)