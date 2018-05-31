---
title: Självstudie för att installera ett program på ett fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: I den här självstudien får du lära dig hur du installerar ett program i ett fristående Service Fabric-kluster.
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
ms.openlocfilehash: 65c0ca98393fbb4f54eee2f4864218f231765904
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208581"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Självstudie: Distribuera ett program i ett fristående Service Fabric-kluster

Med Fristående Service Fabric-kluster kan du välja vilken miljö du vill och skapa ett kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudieserien skapar du ett fristående kluster som hanteras av AWS och distribuerar ett program i det.

Den här självstudiekursen är den tredje delen i en serie.  Med Fristående Service Fabric-kluster kan du välja vilken miljö du vill och skapa ett kluster enligt vår metod ”valfritt operativsystem, valfritt moln” med Service Fabric. Den här självstudien visar hur du skapar den AWS-infrastruktur du behöver för att vara värd för det här fristående klustret.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ladda ned exempelprogrammet
> * Distribuera till klustret

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera **Azure Development** och arbetsbelastningarna **ASP.NET och webbutveckling**.
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet för röstning

Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuera appen till Service Fabric-klustret

När programmet har laddats ned kan du distribuera det till ett kluster direkt från Visual Studio.

1. Öppna Visual Studio

2. Välj **Arkiv** > **Öppna**

3. Gå till den mapp som du klonade git-lagringsplatsen till och välj Voting.sln

4. Högerklicka på programprojektet `Voting` i Solution Explorer och välj **Publicera**

5. Välj listrutan för **Anslutningsslutpunkt** och ange det offentliga DNS-namnet på en av noderna i klustret.  Till exempel, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

6. Öppna en webbläsare och ange klusteradressen (anslutningsslutpunkten – den här appen distribuerar på port 8080, till exempel ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![API-svar från kluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Nästa steg

I del tre i den här serien lärde du dig hur du distribuerar ett program i klustret:

> [!div class="checklist"]
> * Ladda ned exempelprogrammet
> * Distribuera till klustret

Gå vidare till steg fyra i serien för att rensa klustret.

> [!div class="nextstepaction"]
> [Rensa resurserna](service-fabric-tutorial-standalone-clean-up.md)