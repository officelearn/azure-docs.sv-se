---
title: Självstudie för att installera ett program på ett fristående Service Fabric-kluster – Azure Service Fabric | Microsoft Docs
description: I den här självstudien får du lära dig hur du installerar ett program i fristående Service Fabric-klustret.
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
ms.openlocfilehash: 58daa53dba8f18c3f73253008fe687ba8b0d0839
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274075"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Självstudier: Distribuera ett program på din fristående Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudiekursen, skapa ett fristående kluster på AWS och distribuera ett program till den.

Den här självstudiekursen är den tredje delen i en serie.  Fristående Service Fabric-kluster ger dig möjlighet att välja din egen miljö och skapa ett kluster som en del av vår metod för ”alla OS, alla moln” med Service Fabric. Den här självstudien visar hur du skapar den AWS-infrastruktur du behöver för att vara värd för det här fristående klustret.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ladda ned exempelprogrammet
> * Distribuera till klustret

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* [Installera Visual Studio 2019](https://www.visualstudio.com/) och installera den **Azure development** och **ASP.NET och webbutveckling** arbetsbelastningar.
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

5. Välj listrutan för **Anslutningsslutpunkt** och ange det offentliga DNS-namnet på en av noderna i klustret.  Till exempel `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Observera att i Azure, ett fullständigt kvalificerat domännamn (FQDN) inte är angivet automatiskt, men kan enkelt [anges i översikt över VM-sidan.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

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