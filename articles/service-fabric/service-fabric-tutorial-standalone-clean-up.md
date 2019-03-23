---
title: Självstudie om rensning av fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: Den här självstudien visar hur du rensar ditt fristående kluster
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: e3e618bfd141e5cf7159d8df5b2b59f0d17d000f
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372207"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa upp ditt fristående kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudieserien skapar du ett fristående kluster som hanteras av AWS och installerar ett program i det.

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