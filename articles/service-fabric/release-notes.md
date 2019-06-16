---
title: Azure Service Fabric-versioner
description: Viktig information om de senaste funktionerna och förbättringarna i Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 5610c6d31732144086812bb02b65cfaffa067eae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063003"
---
# <a name="service-fabric-releases"></a>Service Fabric-versioner

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Felsökning</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">ärendeuppföljning</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">supportalternativ</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioner som stöds</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kodexempel</a>

Den här artikeln innehåller mer information om de senaste versioner och uppdateringar till Service Fabric runtime och SDK: er.

## <a name="whats-new-in-service-fabric"></a>**Vad är nytt i Service Fabric**

### <a name="service-fabric-65"></a>Service Fabric 6.5

Den senaste Service Fabric-versionen innehåller support, tillförlitlighet och prestanda, nya funktioner, felkorrigeringar och förbättringar för att underlätta kluster och programlivscykelhantering.

> [!IMPORTANT]
> Service Fabric 6.5 är den slutliga versionen med Service Fabric-verktygen stöder i Visual Studio 2015. Kunder bör att flytta till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) framöver.

Här är vad är nytt i Service Fabric 6.5:

- Service Fabric Explorer innehåller en [Store bildvisningsprogram](service-fabric-visualizing-your-cluster.md#image-store-viewer) för kontroll av program som du har överfört till avbildningsarkivet.

- [Patch Orchestration Application POA ()](service-fabric-patch-orchestration-application.md) version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) innehåller många förbättringar som självdiagnostik. Kunder POA rekommenderas att flytta till den här versionen.

- [EventStore Service är aktiverad som standard](service-fabric-visualizing-your-cluster.md#event-store) för 6.5 för Service Fabric-kluster om du har valt.

- Lagt till [Livscykelhändelser för repliken](service-fabric-diagnostics-event-generation-operational.md#replica-events) för tillståndskänsliga tjänster.

- [Bättre synlighet för seed nodstatus](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inklusive klusternivå varningar om en seed-noden är defekt (*ned*, *har tagits bort* eller *okänd*).

- [Service Fabric programmet Disaster Recovery Tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) tillåter tillståndskänslig Service Fabric-tjänster att återhämta sig snabbt när det primära klustret påträffar en katastrof. Data från primär klustret synkroniseras kontinuerligt på den sekundära standby-program med regelbunden säkerhetskopiering och återställning.

- Visual Studio-stöd för [publicering av .NET Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) kommer att installeras automatiskt för Service Fabric 6.5 (och senare versioner) när du uppgraderar eller skapa ett nytt kluster för Linux på Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras som standard på MacOS-/ Linux-OneBox-kluster.

Mer information finns i [viktig för Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 30 november 2018 | [Azure Service Fabric 6.4 ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6.4 uppdatera versionen för Windows-kluster](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Azure Service Fabric 6.4 uppdatering Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Azure Service Fabric 6.4 uppdatering Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| Den 8 april 2019 | [Azure Service Fabric 6.4 uppdatering Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| Den 2 maj 2019 | [Azure Service Fabric 6.4 uppdatering Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maj 2019 | [Azure Service Fabric 6.4 uppdatering Release](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
