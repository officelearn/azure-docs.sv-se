---
title: Självstudie om rensning av fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: I den här självstudien får du lära dig hur du rensar ditt fristående kluster
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385158"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa ditt fristående kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudien skapar du ett fristående kluster som finns på AWS eller Azure och installerar ett program i det.

Den här självstudien är del fyra i en serie. Den här delen av självstudien visar hur du rensar AWS eller Azure-resurser som du har skapat som värd för ditt Service Fabric-kluster.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Rensa ett Service Fabric-kluster
> * Rensa dina AWS-eller Azure-resurser

## <a name="clean-up-service-fabric-cluster"></a>Rensa ett Service Fabric-kluster

1. RDP till den virtuella dator som du använde för att installera Service Fabric.
2. Öppna PowerShell.
3. Ändra katalog till den extraherade mappen från den andra självstudien.
4. Kör följande kommando för att ta bort Service Fabric-klustret:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. `Y` När du uppmanas att göra det kommer dina utdata att se ut ungefär så här, med dina egna IP-adresser som ersätts i:

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

1. Logga in på ditt AWS-konto.
2. Gå till EC2-konsolen.
3. Välj de tre noder som du skapade i del ett av självstudien.
4. Klicka på **åtgärder** > **instans tillstånd** > **Avsluta**.

## <a name="clean-up-azure-resources"></a>Rensa Azure-resurser

1. Logga in på Azure Portal.
2. Gå till avsnittet **Virtual Machines** .
3. Markera kryss rutorna för de tre noder som du skapade i del ett av självstudien.
4. Klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I del fyra i serien lärde du dig att rensa de resurser som skapades i tidigare steg.

> [!div class="checklist"]
> * Rensa dina resurser

> [!div class="nextstepaction"]
> [Tillbaka till början](service-fabric-tutorial-standalone-create-infrastructure.md)
