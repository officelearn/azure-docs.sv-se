---
title: Metodtips för Azure Service Fabric-program och -kluster | Microsoft Docs
description: Metodtips för hantering av Service Fabric-kluster och program.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206789"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Metodtips för Azure Service Fabric-program och -kluster

Den här artikeln innehåller länkar till bästa praxis för att hantera Azure Service Fabric-program och -kluster. Vi rekommenderar starkt att du implementerar dessa metoder för att optimera tillförlitligheten för din produktionsmiljö. Använd någon av de [Service Fabric-kluster mallar](https://github.com/Azure-Samples/service-fabric-cluster-templates) börjar utforma din lösning för produktion eller uppdatera den befintliga mallen om du vill lägga till dessa metoder.

## <a name="security"></a>Säkerhet

* [Metodtips för säkerhet](service-fabric-best-practices-security.md)

## <a name="networking"></a>Nätverk

* [Metodtips för nätverk](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Beräkningsplanering och skalning

* [Metodtips för att skala beräkning](service-fabric-best-practices-capacity-scaling.md)
* [Compute-kapacitetsplanering](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktur som kod

* [Metodtips för att implementera infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

* [Metodtips för klustret övervakning och diagnostik](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Programdesign

* [Metodtips för programmets design](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Checklista

Se till att du har integrerat de bästa metoderna i produktion beredskap checklistan när du har implementerat de metoder som föreslås i föregående avsnitt:
* [Azure Service Fabric produktion beredskap Checklista](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka Service Fabric: [Felsökning](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)