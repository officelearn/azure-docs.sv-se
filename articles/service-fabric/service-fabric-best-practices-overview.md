---
title: Metodtips för Azure Service Fabric-program och kluster
description: Metodtips och designöverväganden för hantering av kluster, appar och tjänster med Hjälp av Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551785"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Metodtips för Azure Service Fabric-program och kluster

Den här artikeln innehåller länkar till metodtips för hantering av Azure Service Fabric-program och kluster. Vi rekommenderar starkt att du implementerar dessa metoder för att optimera tillförlitligheten i din produktionsmiljö. Använd en av [klustermallarna för Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) för att börja utforma produktionslösningen eller uppdatera den befintliga mallen för att infoga dessa metoder.

## <a name="security"></a>Säkerhet

* [Metodtips för säkerhet](service-fabric-best-practices-security.md)

## <a name="networking"></a>Nätverk

* [Metodtips för nätverk](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Beräkningsplanering och skalning

* [Metodtips för beräkningskalning](service-fabric-best-practices-capacity-scaling.md)
* [Planering av beräkningskapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktur som kod

* [Metodtips för att implementera infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

* [Metodtips för klusterövervakning och diagnostik](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Programdesign

* [Metodtips för programdesign](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Checklista

När du har implementerat de metoder som föreslås i föregående avsnitt kontrollerar du att du har integrerat alla metodtips i checklistan för produktionsberedskap:
* [Checklista för produktionberedskap för Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa service fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka service fabric: [felsökningsguider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)