---
title: Optimera Visual Studio för Azure Service Fabric-nät
description: I den här artikeln visas hur du optimerar Visual Studio-prestanda för Service Fabric Mesh-projekt så att den första felsökningskörningen (F5) blir mycket snabbare.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: aa7a959128d3bcdfcce67d3abeac245975339a9f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007383"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimera Visual Studio-prestanda för Service Fabric Mesh-projekt

I den här artikeln visas hur du optimerar Visual Studio-prestanda för Service Fabric Mesh-projekt så att den första felsökningskörningen (F5) blir mycket snabbare.  

## <a name="change-visual-studio-settings"></a>Ändra Visual Studio-inställningar
 
Under **verktyg**  >  **alternativ**   >  **Service Fabric nät verktyg**  >  **Allmänt** i Visual Studio kan du justera följande inställningar:

- **Pull required Docker images on project open** (Hämta nödvändiga Docker-avbildningar när projektet öppnas) gör den första felsökningskörningen (F5) snabbare genom att börja avbildningsnedladdningen när projektet läses in.  
- **Deploy application on project open** (Distribuera app när projektet öppnas) kan göra den första felsökningskörningen (F5) snabbare genom att börja distributionsprocessen när projektet har öppnats.  
- **Remove application on project close** (Ta bort app när projektet stängs) frigör resurser (CPU, RAM) tilldelade till appen genom att ta bort Mesh-appen när projektet stängs.  

När du ser meddelanden i Service Fabric Tools-utdatafönster om att Visual Studio gör följande: pulling images (hämtar avbildningar), warming up (värmer upp) eller removing application (tar bort app) gäller det inställningarna ovan. Du kan inaktivera de här inställningarna.

## <a name="next-steps"></a>Nästa steg

Läs igenom [självstudien om felsökning av en Mesh-app](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)