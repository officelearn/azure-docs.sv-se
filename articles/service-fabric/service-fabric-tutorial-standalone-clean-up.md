---
title: Självstudie om rensning av fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: I den här självstudien du lära dig hur du rensar ditt fristående kluster
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
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274085"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa upp ditt fristående kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudiekursen, skapa ett fristående kluster som finns på AWS- eller Azure och installera ett program till den.

Den här självstudien är del fyra i en serie. Den här delen av kursen visar hur du rensar AWS eller Azure-resurser som du skapade för att vara värd för Service Fabric-klustret.

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Rensa ett Service Fabric-kluster
> * Rensa upp din AWS- eller Azure-resurser

## <a name="clean-up-service-fabric-cluster"></a>Rensa ett Service Fabric-kluster

1. RDP till den virtuella datorn som du använde för att installera Service Fabric.
2. Öppna PowerShell.
3. Ändra katalog till den extraherade mappen från den andra självstudien.
4. Kör följande kommando för att ta bort Service Fabric-klustret:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Ange `Y` när du uppmanas att göra om den lyckades dina utdata ser ut följande, med dina egna IP-adresser som använts istället i:

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

1. Logga in på din AWS-konto.
2. Gå till EC2-konsolen.
3. Välj de tre noder som du skapade i del ett av självstudien.
4. Klicka på **åtgärder** > **Instanstillståndet** > **avsluta**.

## <a name="clean-up-azure-resources"></a>Rensa Azure-resurser

1. Logga in på Azure Portal.
2. Gå till den **virtuella datorer** avsnittet.
3. Markera kryssrutorna för de tre noder som du skapade i den första delen av kursen.
4. Klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I del fyra i serien lärde du dig att rensa de resurser som skapades i tidigare steg.

> [!div class="checklist"]
> * Rensa dina resurser

> [!div class="nextstepaction"]
> [Tillbaka till början](service-fabric-tutorial-standalone-create-infrastructure.md)
