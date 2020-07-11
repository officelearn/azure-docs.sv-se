---
title: Installera en app i ett fristående kluster
description: I den här självstudien får du lära dig hur du installerar ett program i det fristående Service Fabric klustret.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 8dd47d27f928cb33e5a2bbc3ba9a6ab2bc044852
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245389"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Självstudie: Distribuera ett program i ett fristående Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här själv studie serien skapar du ett fristående kluster som finns på AWS och distribuerar ett program till det.

Den här självstudiekursen är den tredje delen i en serie.  Service Fabric fristående kluster ger dig möjlighet att välja din egen miljö och skapa ett kluster som en del av vår metod för "alla operativ system, alla moln" med Service Fabric. Den här självstudien visar hur du skapar den AWS-infrastruktur du behöver för att vara värd för det här fristående klustret.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ladda ned exempelprogrammet
> * Distribuera till klustret

## <a name="prerequisites"></a>Förhandskrav

Innan du börjar den här självstudien:

* [Installera Visual Studio 2019](https://www.visualstudio.com/) och installera arbets belastningarna **Azure Development** och **ASP.net och webb utveckling** .
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet för röstning

Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuera appen till Service Fabric-klustret

När programmet har laddats ned kan du distribuera det till ett kluster direkt från Visual Studio.

1. Öppna Visual Studio

2. Välj **File**  >  **Öppna** fil

3. Gå till den mapp som du klonade git-lagringsplatsen till och välj Voting.sln

4. Högerklicka på programprojektet `Voting` i Solution Explorer och välj **Publicera**

5. Välj listrutan för **Anslutningsslutpunkt** och ange det offentliga DNS-namnet på en av noderna i klustret.  Ett exempel är `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Observera att ett fullständigt kvalificerat domän namn (FQDN) inte anges automatiskt i Azure, men det kan enkelt [anges i översikts sidan för den virtuella datorn.](../virtual-machines/linux/portal-create-fqdn.md)

6. Öppna en webbläsare och ange klusteradressen (anslutningsslutpunkten – den här appen distribuerar på port 8080, till exempel ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![API-svar från kluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Nästa steg

I del tre i den här serien lärde du dig hur du distribuerar ett program i klustret:

> [!div class="checklist"]
> * Ladda ned exempelprogrammet
> * Distribuera till klustret

Gå vidare till steg fyra i serien för att rensa klustret.

> [!div class="nextstepaction"]
> [Rensa dina resurser](service-fabric-tutorial-standalone-clean-up.md)
