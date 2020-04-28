---
title: Metod tips för Azure Service Fabric-program och-kluster
description: Bästa praxis och design överväganden för hantering av kluster, appar och tjänster med hjälp av Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551785"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Metod tips för Azure Service Fabric-program och-kluster

Den här artikeln innehåller länkar till metod tips för att hantera Azure Service Fabric-program och-kluster. Vi rekommenderar starkt att du implementerar dessa metoder för att optimera tillförlitligheten i din produktions miljö. Använd en av [Service Fabric Cluster-mallarna](https://github.com/Azure-Samples/service-fabric-cluster-templates) för att börja designa din produktions lösning eller uppdatera din befintliga mall så att du kan lägga till dessa metoder.

## <a name="security"></a>Säkerhet

* [Metod tips för säkerhet](service-fabric-best-practices-security.md)

## <a name="networking"></a>Nätverk

* [Metod tips för nätverk](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Beräkningsplanering och skalning

* [Metod tips för beräknings skalning](service-fabric-best-practices-capacity-scaling.md)
* [Planering av beräknings kapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktur som kod

* [Metod tips för att implementera infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

* [Metod tips för kluster övervakning och diagnostik](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Programdesign

* [Metod tips för program design](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Checklista

När du har implementerat de metoder som föreslås i föregående avsnitt bör du se till att du har integrerat alla rekommenderade metoder i check listan för produktions beredskap:
* [Check lista för Azure Service Fabric Production readiness](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md)
* Felsöka Service Fabric: [fel söknings guider](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)