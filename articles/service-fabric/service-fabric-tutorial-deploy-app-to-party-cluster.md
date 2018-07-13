---
title: Distribuera en Service Fabric-app till ett kluster i Azure | Microsoft Docs
description: Läs mer om hur du distribuerar ett program till ett kluster från Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f83ebcce68a7abe53d7b8eaeff5913a907e3df9a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344197"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Självstudie: Distribuera en Service Fabric-app till ett kluster i Azure

Den här självstudien är del två i en serie. Här får du se hur du distribuerar ett Azure Service Fabric-program till ett nytt kluster i Azure direkt från Visual Studio.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett kluster från Visual Studio
> * Distribuera ett program till ett fjärrkluster med Visual Studio

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * Distribuera programmet till ett fjärrkluster
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurera CI/CD med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera **Azure Development** och arbetsbelastningarna **ASP.NET och webbutveckling**.
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet för röstning

Om du inte skapade exempelprogrammet för röstning i [del ett av självstudieserien](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Nu när programmet är redo kan du distribuera det till ett kluster direkt från Visual Studio. Ett [Service Fabric-kluster](/service-fabric/service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från

Du har två alternativ för distributionen i Visual Studio:

* Skapa ett kluster i Azure från Visual Studio. Med det här alternativet kan du skapa ett säkert kluster direkt från Visual Studio med dina önskade konfigurationer. Den här typen av kluster är idealiskt för testscenarier, där du kan skapa klustret och sedan publicera direkt i Visual Studio.
* Publicera till ett befintligt kluster i din prenumeration.  Du kan skapa Service Fabric-kluster via [Azure Portal](https://portal.azure.com)med hjälp av [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)- eller [Azure CLI](./scripts/cli-create-cluster.md)-skript, eller från en [Azure Resource Manager-mall](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

I den här självstudien skapar vi ett kluster från Visual Studio. Om du redan har ett distribuerat kluster kan du kopiera och klistra in anslutningens slutpunkt, eller välja den från din prenumeration.
> [!NOTE]
> Många tjänster använder omvänd proxy när de kommunicerar med varandra. Kluster som skapas från Visual Studio och partkluster har en omvänd proxy som är aktiverad som standard.  Om du använder ett befintligt kluster måste du [aktivera omvänd proxy i klustret](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>Hitta tjänsten VotingWebs slutpunkt

Hitta först slutpunkten för frontend-webbtjänsten.  Frontend-webbtjänsten lyssnar på en viss port.  När programmet distribueras till ett kluster i Azure, körs både klustret och programmet bakom en Azure-belastningsutjämnare.  Programporten måste vara öppen i Azure-belastningsutjämnaren så att inkommande trafik kan nå webbtjänsten.  Porten (t.ex. 8080) hittar du i filen *VotingWeb/PackageRoot/ServiceManifest.xml* i elementet **Endpoint**:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

I nästa steg anger du den här porten på fliken **Avancerat** i dialogrutan **Skapa kluster**.  Om du distribuerar programmet till ett befintligt kluster kan du öppna den här porten i Azure-belastningsutjämnaren med ett [PowerShell-skript](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) eller i [Azure Portal](https://portal.azure.com).

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Skapa ett kluster i Azure från Visual Studio

Högerklicka på programprojektet i Solution Explorer och välj **Publicera**.

Logga in med ditt Azure-konto så att du får åtkomst till dina prenumerationer. Det här steget är valfritt om du använder ett partkluster.

Välj listrutan för **Slutpunkt för anslutningen** och välj alternativet **<Create New Cluster...>**.

![Dialogrutan Publicera](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Ändra följande inställningar i dialogrutan **Skapa kluster**:

1. Ange namnet på klustret i fältet **Klusternamn**, samt den prenumeration och plats som du vill använda.
2. Valfritt: Du kan ändra antalet noder. Som standard har du tre noder, vilket är det som krävs för att testa scenarier i Service Fabric.
3. Välj fliken **Certifikat**. På den här fliken skriver du ett lösenord som ska skydda certifikatet i klustret. Med det här certifikatet blir klustret säkrare. Du kan också ändra sökvägen till den plats där du vill spara certifikatet. Visual Studio kan dessutom importera certifikatet åt dig, eftersom det är ett obligatoriskt steg för att kunna publicera programmet i klustret.
4. Välj fliken med **information om den virtuella datorn**. Ange det lösenord som du vill använda för den Virtual Machines (VM) som ingår i klustret. Användarnamnet och lösenordet kan användas för att fjärransluta till de virtuella datorerna. Du måste också välja en virtuell datorstorlek och du kan ändra VM-avbildning om det behövs.
5. På fliken **Avancerat** kan du ändra listan med de portar som du vill ska vara öppna i Azure-belastningsutjämnaren som skapades tillsammans med klustret.  Lägg till tjänsteslutpunkten för VotingWeb som du identifierade i föregående steg. Du kan också lägga till en befintlig Application Insights-nyckel för att dirigera programmets loggfiler.
6. När du är klar med ändringen av inställningarna väljer du knappen **Skapa**. Detta tar några minuter att slutföra och utdatafönstret visar när klustret är färdigt.

![Dialogrutan Skapa kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>Distribuera exempelprogrammet

När det kluster som du vill använda är klart, högerklickar du på programprojektet och väljer **Publicera**.

När publiceringen är klar bör du kunna skicka en begäran till programmet via en webbläsare.

Öppna din webbläsare och ange klusteradressen (anslutningens slutpunkt utan portinformation, till exempel win1kw5649s.westus.cloudapp.azure.com).

Nu bör du få samma resultat som du fick när du körde programmet lokalt.

![API-svar från kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett kluster från Visual Studio
> * Distribuera ett program till ett fjärrkluster med Visual Studio

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Aktivera HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
