---
title: Rensa ett fristående kluster
description: I den här självstudien får du lära dig hur du rensar AWS eller Azure-resurser i det fristående Service Fabric klustret.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75639028"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Självstudier: Rensa fristående kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här självstudien skapar du ett fristående kluster som finns på AWS eller Azure och installerar ett program i det.

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

1. Logga in på Azure-portalen.
2. Gå till avsnittet **Virtual Machines** .
3. Markera kryss rutorna för de tre noder som du skapade i del ett av självstudien.
4. Klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I del fyra i serien lärde du dig att rensa de resurser som skapades i tidigare steg.

> [!div class="checklist"]
> * Rensa dina resurser

> [!div class="nextstepaction"]
> [Tillbaka till början](service-fabric-tutorial-standalone-create-infrastructure.md)
