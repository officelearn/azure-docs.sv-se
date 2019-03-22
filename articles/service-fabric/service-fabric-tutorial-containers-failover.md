---
title: Redundansväxla och skala om en containerapp för Service Fabric i Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur redundansväxling hanteras i ett Azure Service Fabric-containerprogram.  Lär dig också att skala containrarna och tjänsterna som körs i ett kluster.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: f5ac1c53a97fc2a3749582c5f48b967e3ce0c2c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104275"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Självstudier: Visa misslyckas över och skalning av behållartjänster med Service Fabric

Den här självstudiekursen är den tredje delen i en serie. I den här självstudien får du lära dig hur redundansväxling hanteras i Service Fabric-containerprogram. Du får också lära dig att skala containrar. I den här kursen för du göra följande:

> [!div class="checklist"]
> * Läs om att redundansväxla en container i ett Service Fabric-kluster
> * Skala webbklientcontainrar i ett program

## <a name="prerequisites"></a>Förutsättningar

Programmet från [Del 2](service-fabric-tutorial-package-containers.md) körs som ett aktivt Service Fabric-kluster.

## <a name="fail-over-a-container-in-a-cluster"></a>Redundansväxla en container i ett kluster

Service Fabric ser till att dina containerinstanser flyttas automatiskt till andra noder i klustret vid ett fel. Du kan också tömma en nod på containrar och sedan flytta dem till andra noder i klustret. Det finns flera sätt att skala tjänsterna. I det här exemplet använder vi Service Fabric Explorer.

Så här redundansväxlar du containern på klientsidan:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Klicka på noden **fabric:/TestContainer/azurevotefront** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare). Nodnamnet i trädvyn visar vilka noder som containern körs på för tillfället, till exempel `_nodetype_1`
3. Expandera noden **Noder** i trädvyn. Klicka på ellipsknappen (tre punkter) bredvid den nod som körs i containern.
4. Välj **Starta om** för att starta om noden och bekräfta omstartsåtgärden. Omstarten gör att containern växlar över till en annan nod i klustret.

![noderestart][noderestart]

Lägg märke till hur nodnamnet (som indikerar var klientcontainrarna körs) ändras till en annan nod i klustret. Efter en kort stund bör du kunna bläddra till programmet igen och se hur programmet nu körs på en annan nod.

## <a name="scale-containers-and-services-in-a-cluster"></a>Skala containrar och tjänster i ett kluster

Service Fabric-containrar kan enkelt skalas över ett kluster beroende på belastningen på tjänsterna. Du kan skala en container genom att ändra antalet instanser som körs i klustret.

Gör så här om du vill skala webbklienten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Klicka på ellipsknappen (tre punkter) bredvid noden **fabric:/TestContainer/azurevotefront** i trädvyn och välj **Scale Service** (Skala tjänst).

![sfxscale][sfxscale]

Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

1. Ändra antalet till **2** och klicka på **Scale Service** (Skala tjänst).
1. Klicka på noden **fabric:/TestContainer/azurevotefront** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare).

![sfxscaledone][sfxscaledone]

Du kan nu se att tjänsten har två instanser. I trädvyn ser du vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har vi dubblerat resurserna för bearbetning av användarbelastning för frontwebbtjänsten. Det är viktigt att veta att du inte behöver flera instanser av en tjänst för att den ska köras på ett tillförlitligt sätt. Om en tjänst misslyckas ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="next-steps"></a>Nästa steg

I den här självstudien visades redundansväxling av containrar samt skalning av ett program. Följande steg har slutförts:

> [!div class="checklist"]
> * Läs om att redundansväxla en container i ett Service Fabric-kluster
> * Skala webbklientcontainrar i ett program

I den här serien med självstudiekurser lärde du dig att:
> [!div class="checklist"]
> * Skapa containeravbildningar
> * Push-överför avbildningar till Azure Container Registry
> * Paketera containrar för Service Fabric med Yeoman
> * Skapa och köra ett Service Fabric-program med containrar
> * Hantera redundans och skalning i Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png