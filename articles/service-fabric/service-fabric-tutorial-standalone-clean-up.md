---
title: Självstudie om rensning av fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: Den här självstudien visar hur du rensar ditt fristående kluster
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: d92712200920f135e362e15e1d9536dc2342ebaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211097"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa fristående kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här serien självstudiekurser skapar du ett fristående kluster som hanteras av AWS och installerar ett program i det.

Den här självstudien är del fyra i en serie. Den här delen av kursen visar hur du rensar AWS-resurser som du skapade för att hantera Service Fabric-klustret.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Rensa ett Service Fabric-kluster
> * Rensa dina AWS-resurser

## <a name="clean-up-service-fabric-cluster"></a>Rensa ett Service Fabric-kluster

* RDP i den EC2-instans som du använde för att installera Service Fabric
* Öppna PowerShell
* Ändra katalog till den extraherade mappen från den andra självstudien.
* Kör följande kommando för att ta bort Service Fabric-klustret:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` när du uppmanas, om det lyckades ser dina utdata ut som följer, med din egen IP-adress i stället för exemplet:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Rensa AWS-resurser

* Logga in på ditt AWS-konto
* Gå till EC2-konsolen.
* Välj de tre noder som du skapade i del ett av självstudien.
* Klicka på **Åtgärder** > **Instanstillstånd** > **Avsluta**

## <a name="next-steps"></a>Nästa steg

I del fyra i serien lärde du dig att rensa de resurser som skapades i tidigare steg.

> [!div class="checklist"]
> * Rensa dina resurser

> [!div class="nextstepaction"]
> [Tillbaka till början](service-fabric-tutorial-standalone-create-infrastructure.md)