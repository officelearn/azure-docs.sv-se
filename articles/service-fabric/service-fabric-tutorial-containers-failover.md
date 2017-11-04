---
title: "Redundans och skala en behållare Azure Service Fabric-app | Microsoft Docs"
description: "Lär dig hur redundans hanteras i ett program för Azure Service Fabric-behållare.  Lär dig också att skala behållare och tjänster som körs i ett kluster."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker-behållare, Mikrotjänster, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 21dd9dfbc90c26236c43e2c334305ca97f63d361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Visa misslyckas över och skalning för behållartjänster med Service Fabric

Den här kursen ingår tre i en serie. I kursen får du lära dig hur redundans hanteras i Service Fabric-behållarprogram. Dessutom får du lära dig hur du skalar behållare. I den här kursen har du:

> [!div class="checklist"]
> * Lär dig mer om behållaren redundans i ett Service Fabric-kluster  
> * Skala web front-end behållare i en kommit in

## <a name="prerequisites"></a>Krav
Programmet från [del 2](service-fabric-tutorial-package-containers.md) körs i en aktiv Service Fabric-klustret.

## <a name="fail-over-a-container-in-a-cluster"></a>Redundansväxla en behållare i ett kluster
Service Fabric ser till att din behållarinstanser flyttas automatiskt till andra noder i klustret, om ett fel inträffar. Du kan också manuellt tömma en nod av behållare och smidigt flytta dem till andra noder i klustret. Det finns flera sätt att skala tjänsterna. I det här exemplet använder vi Service Fabric Explorer.

Så här redundansväxlar du behållaren på klientsidan:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Klicka på den **fabric: / TestContainer/azurevotefront** noden i trädvyn och expandera noden partition (representerade av GUID). Lägg märke till nodnamn i trädvyn, som visar noderna behållaren körs på - exempel`_nodetype_1`
3. Expandera den **noder** noden i treeview. Klicka på ellipsknappen (tre punkter) bredvid noden som kör behållaren.
1. Välj **Starta om** för att starta om noden och bekräfta omstartsåtgärden. Omstarten gör att behållaren växlar över till en annan nod i klustret.

![noderestart][noderestart]

Observera hur nod namn på där frontend behållarna körs nu ändras till en annan nod i klustret. Efter en liten stund bör du kunna bläddra till programmet igen och se det program som körs nu på en annan nod.

## <a name="scale-containers-and-services-in-a-cluster"></a>Skala behållarna och tjänster i ett kluster
Service Fabric-behållare kan skalas över ett kluster kan utföra belastningen på tjänsterna. Du kan skala en behållare genom att ändra antalet instanser som körs i klustret.

Om du vill skala webbklientdelen gör du följande steg:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Klicka på ellipsknappen (tre punkter) bredvid den **fabric: / TestContainer/azurevotefront** noder i trädet visa och välja **skala Service**.

![sfxscale][sfxscale]

Du kan nu välja att skala antalet instanser av webbklientdelen.

3. Ändra antalet till **2** och klicka på **Scale Service** (Skala tjänst).
4. Klicka på den **fabric: / TestContainer/azurevotefront** noder i trädet visa och expandera noden partition (representerade av GUID).

![sfxscaledone][sfxscaledone]

Du kan nu se att tjänsten har två instanser. I trädvyn ser du vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har vi dubblerat resurserna för bearbetning av användarbelastning för frontwebbtjänsten. Det är viktigt att veta att du inte behöver flera instanser av en tjänst för att den ska köras på ett tillförlitligt sätt. Om en tjänst misslyckas ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen behållaren redundans har visat samt skalning av ett program. Följande steg har slutförts:

> [!div class="checklist"]
> * Lär dig mer om behållaren redundans i ett Service Fabric-kluster  
> * Skala web front-end behållare i en kommit in

I den här självstudiekursen serien du lärt dig hur du: 
> [!div class="checklist"]
> * Skapa avbildningar av behållare
> * Push-behållaren avbildningar till registret för Azure-behållare
> * Paketet behållare för Service Fabric med Yeoman
> * Skapa och köra ett Service Fabric-program med behållare
> * Hur redundans och skalning hanteras i Service Fabric

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
