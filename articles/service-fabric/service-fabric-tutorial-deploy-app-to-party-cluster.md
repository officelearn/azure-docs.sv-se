---
title: Distribuera ett Azure Service Fabric-program till ett kluster från Visual Studio | Microsoft Docs
description: Läs mer om hur du distribuerar ett program till ett kluster från Visual Studio
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 1d8f8d903046f1d471f7abbe08a957b81522e391
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Självstudie: Distribuera ett program till ett Service Fabric-kluster i Azure
Den här självstudien är del två i en serie. Här får du se hur du distribuerar ett Azure Service Fabric-program till ett nytt kluster i Azure direkt från Visual Studio.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett kluster från Visual Studio
> * Distribuera ett program till ett fjärrkluster med Visual Studio


I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * Distribuera programmet till ett fjärrkluster
> * [Konfigurera CI/CD med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar den här självstudien:
- om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera **Azure Development** och arbetsbelastningarna **ASP.NET och webbutveckling**.
- [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet för röstning
Om du inte skapade exempelprogrammet för röstning i [del ett av självstudieserien](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>Distribuera exempelprogrammet

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Välj ett Service Fabric-kluster som du ska publicera till
Nu när programmet är redo kan du distribuera det till ett kluster direkt från Visual Studio.

Du har två alternativ för distributionen:
- Skapa ett kluster från Visual Studio. Med det här alternativet kan du skapa ett säkert kluster direkt från Visual Studio med dina önskade konfigurationer. Den här typen av kluster är idealiskt för testscenarier, där du kan skapa klustret och sedan publicera direkt i Visual Studio.
- Publicera till ett befintligt kluster i din prenumeration.

Den här självstudien visar hur du skapar ett kluster från Visual Studio. För de andra alternativen kan du kopiera och klistra in anslutningens slutpunkt, eller välja den från din prenumeration.
> [!NOTE]
> Många tjänster använder omvänd proxy när de kommunicerar med varandra. Kluster som skapas från Visual Studio och partkluster har en omvänd proxy som är aktiverad som standard.  Om du använder ett befintligt kluster måste du [aktivera omvänd proxy i klustret](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuera appen till Service Fabric-klustret
1. Högerklicka på programprojektet i Solution Explorer och välj **Publicera**.

2. Logga in med ditt Azure-konto så att du får åtkomst till dina prenumerationer. Det här steget är valfritt om du använder ett partkluster.

3. Välj listrutan för **anslutningens slutpunkt** och välj alternativet ”<Create New Cluster...>”.
    
    ![Dialogrutan Publicera](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. Ändra följande inställningar i dialogrutan ”Skapa kluster”:

    1. Ange namnet på klustret i fältet ”Klusternamn”, samt den prenumeration och plats som du vill använda.
    2. Valfritt: Du kan ändra antalet noder. Som standard har du tre noder, vilket är det som krävs för att testa scenarier i Service Fabric.
    3. Välj fliken ”Certifikat”. På den här fliken skriver du ett lösenord som ska skydda certifikatet i klustret. Med det här certifikatet blir klustret säkrare. Du kan också ändra sökvägen till den plats där du vill spara certifikatet. Visual Studio kan dessutom importera certifikatet åt dig, eftersom det är ett obligatoriskt steg för att kunna publicera programmet i klustret.
    4. Välj fliken med VM-information. Ange det lösenord som du vill använda för den Virtual Machines (VM) som ingår i klustret. Användarnamnet och lösenordet kan användas för att fjärransluta till de virtuella datorerna. Du måste också välja en virtuell datorstorlek och du kan ändra VM-avbildning om det behövs.
    5. Valfritt: På fliken ”Avancerat” kan du ändra listan med de portar som du vill ska vara öppna i belastningsutjämnaren som skapas tillsammans med klustret. Du kan också lägga till en befintlig Application Insights-nyckel som används för att dirigera programmets loggfiler.
    6. När du är klar med ändringen av inställningarna klickar du på knappen ”Skapa”. Detta tar några minuter att slutföra och utdatafönstret visar när klustret är färdigt.
    
    ![Dialogrutan Skapa kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. När det kluster som du vill använda är klart, högerklickar du på programprojektet och väljer **Publicera**.

    När publiceringen är klar bör du kunna skicka en begäran till programmet via en webbläsare.

5. Öppna din webbläsare och ange klusteradressen (anslutningens slutpunkt utan portinformation, till exempel win1kw5649s.westus.cloudapp.azure.com).

    Nu bör du få samma resultat som du fick när du körde programmet lokalt.

    ![API-svar från kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett kluster från Visual Studio
> * Distribuera ett program till ett fjärrkluster med Visual Studio

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Konfigurera kontinuerlig integrering med Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
