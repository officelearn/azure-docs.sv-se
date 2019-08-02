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
ms.openlocfilehash: 829963cf13eb1dc1b1113b6a296fe77dadb63bc4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224938"
---
# <a name="service-fabric-releases"></a>Service Fabric versioner

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Fel söknings guider</a> 
|  
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">problem med</a><a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioner</a><a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank"></a> <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank"></a>som 
| stöds kod exempel 
| 

Den här artikeln innehåller mer information om de senaste versionerna och uppdateringarna av Service Fabric Runtime och SDK: er.

## <a name="whats-new-in-service-fabric"></a>Vad är nytt i Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

Den senaste Service Fabric versionen innehåller stöd, tillförlitlighet och prestanda förbättringar, nya funktioner, fel korrigeringar och förbättringar för att förenkla hanteringen av kluster och program.

> [!IMPORTANT]
> Service Fabric 6,5 är den slutliga versionen med stöd för Service Fabric-verktyg i Visual Studio 2015. Kunderna uppmanas att gå vidare till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Här är what's New i Service Fabric 6,5:

- Service Fabric Explorer innehåller ett [avbildningsarkiv visnings](service-fabric-visualizing-your-cluster.md#image-store-viewer) program för att inspektera program som du har överfört till avbildnings lagret.

- [POA-1.4.0 (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) innehåller många själv diagnostiska förbättringar. [](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) Kunder med POA rekommenderas att flytta till den här versionen.

- [EventStore-tjänsten är aktive rad som standard](service-fabric-visualizing-your-cluster.md#event-store) i Service Fabric 6,5-kluster om du inte har valt ut.

- Har lagt till [replik livs cykel händelser](service-fabric-diagnostics-event-generation-operational.md#replica-events) för tillstånds känsliga tjänster.

- [Bättre insyn i status för Dirigerings nod](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inklusive varningar på kluster nivå om en Seed-nod är ohälsosam *(av*, *borttagen* eller *okänd*).

- Med [Service Fabric-verktyget för haveri beredskap för program](https://github.com/Microsoft/Service-Fabric-AppDRTool) kan Service Fabric tillstånds känsliga tjänster återställas snabbt när det primära klustret påträffar en katastrof. Data från det primära klustret synkroniseras kontinuerligt i det sekundära standby-programmet med hjälp av periodisk säkerhets kopiering och återställning.

- Visual Studio-stöd för [att publicera .net Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras automatiskt för Service Fabric 6,5 (och senare versioner) när du uppgraderar eller skapar ett nytt Linux-kluster i Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) installeras som standard på MacOS/Linux Onebox behållaravbildningen-kluster.

Mer information finns i versions [anmärkningar för Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |

## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4 uppdaterings version för Windows-kluster](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maj 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maj, 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
