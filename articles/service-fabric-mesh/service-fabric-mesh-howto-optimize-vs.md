---
title: Optimera Visual Studio-prestanda för Azure Service Fabric Mesh-projekt | Microsoft Docs
description: Optimera Visual Studio-prestanda för Azure Service Fabric Mesh-appar
services: service-fabric-mesh
keywords: optimera felsökningsprestanda
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778934"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimera Visual Studio-prestanda för Service Fabric Mesh-projekt

I den här artikeln visas hur du optimerar Visual Studio-prestanda för Service Fabric Mesh-projekt så att den första felsökningskörningen (F5) blir mycket snabbare.  

## <a name="change-visual-studio-settings"></a>Ändra Visual Studio-inställningar
 
I Visual Studio, under **Tools** (Verktyg)  > **Options** (Alternativ)   > **Service Fabric Mesh Tools** (Service Fabric Mesh-verktyg)  > **General** (Allmänt) kan du justera följande inställningar:

- **Pull required Docker images on project open** (Hämta nödvändiga Docker-avbildningar när projektet öppnas) gör den första felsökningskörningen (F5) snabbare genom att börja avbildningsnedladdningen när projektet läses in.  
- **Deploy application on project open** (Distribuera app när projektet öppnas) kan göra den första felsökningskörningen (F5) snabbare genom att börja distributionsprocessen när projektet har öppnats.  
- **Remove application on project close** (Ta bort app när projektet stängs) frigör resurser (CPU, RAM) tilldelade till appen genom att ta bort Mesh-appen när projektet stängs.  

När du ser meddelanden i Service Fabric Tools-utdatafönster om att Visual Studio gör följande: pulling images (hämtar avbildningar), warming up (värmer upp) eller removing application (tar bort app) gäller det inställningarna ovan. Du kan inaktivera de här inställningarna.

## <a name="next-steps"></a>Nästa steg

Läs igenom [självstudien om felsökning av en Mesh-app](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)