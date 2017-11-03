---
title: Distribuera ett Azure Service Fabric-program till ett kluster som part | Microsoft Docs
description: "Lär dig hur du distribuerar ett program till ett kluster som part."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 5766ef2097b0da295d42e7c5909efc524049f418
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Distribuera ett program till ett kluster som part i Azure
Den här kursen ingår två av en serie och visar hur du distribuerar ett Azure Service Fabric-program till ett kluster som part i Azure.

I avsnitt två självstudiekursen seriens lär du dig hur du:
> [!div class="checklist"]
> * Distribuera ett program till ett kluster med hjälp av Visual Studio
> * Ta bort ett program från ett kluster med Service Fabric Explorer

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * Distribuera programmet till ett kluster
> * [Konfigurera CI/CD: N med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera den **Azure-utveckling** och **ASP.NET och web development** arbetsbelastningar.
- [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet röst
Om du inte att skapa exempelprogrammet röst [ingår i den här självstudiekursen serie](service-fabric-tutorial-create-dotnet-app.md), du kan ladda ned den. Kör följande kommando för att klona exempel app lagringsplatsen till den lokala datorn i ett kommandofönster.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Konfigurera en part-kluster
Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster i Azure som körs av Service Fabric-teamet där vem som helst kan distribuera program och lära sig mer om plattformen. Kostnadsfritt!

Bläddra till platsen för att få åtkomst till ett kluster som part: http://aka.ms/tryservicefabric och följ instruktionerna för att få åtkomst till ett kluster. Du behöver ett Facebook eller GitHub-konto för att få åtkomst till ett kluster som part.

Du kan använda ditt eget kluster i stället för part klustret, om du vill.  ASP.NET core webbklientdelen använder omvänd proxy för att kommunicera med tillståndskänslig service serverdel.  Part kluster och lokal utveckling klustret har omvänd proxy är aktiverat som standard.  Om du distribuerar exempelprogrammet röst till ditt eget kluster måste du [aktivera omvänd proxy i klustret](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Part kluster skyddas inte så att dina program och alla data som du lägger till i dem vara synliga för andra. Distribuera inte vad du inte vill att andra ska se. Glöm inte att läsa över våra användningsvillkor detaljerad information.

## <a name="configure-the-listening-port"></a>Konfigurera lyssningsporten
När VotingWeb frontend-tjänst skapas väljer slumpmässigt en port för tjänsten för att lyssna på i Visual Studio.  Tjänsten VotingWeb fungerar som klientdel för det här programmet och godkänner externa trafiken, så vi binda tjänsten till ett fast och vet port korrekt. I Solution Explorer öppnar *VotingWeb/PackageRoot/ServiceManifest.xml*.  Hitta de **Endpoint** resurs i den **resurser** avsnittet och ändra den **Port** värdet till mellan 80.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Uppdatera också egenskapsvärdet programmets URL i röst-projektet så att en webbläsare öppnas till rätt port när du felsöker med 'F5'.  I Solution Explorer, väljer du den **Röstningsdatabasen** projekt och uppdatera den **programmets URL** egenskapen.

![Programmets URL](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

## <a name="deploy-the-app-to-the-azure"></a>Distribuera appen till Azure
Nu när programmet är klart, kan du distribuera den till part klustret direkt från Visual Studio.

1. Högerklicka på **Röstningsdatabasen** i Solution Explorer och välj **publicera**.

    ![Dialogrutan Publicera](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. Ange anslutningens slutpunkt för part-kluster i den **Anslutningens slutpunkt** fältet och klickar på **publicera**.

    När publiceringen är klar bör du kunna skicka en begäran till programmet via en webbläsare.

3. Öppna din webbläsare och anger klusteradress (Anslutningens slutpunkt utan portinformation – till exempel win1kw5649s.westus.cloudapp.azure.com).

    Du bör nu se detta som du såg när du kör programmet lokalt.

    ![API-svar från kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Ta bort programmet från ett kluster med Service Fabric Explorer
Service Fabric Explorer är ett grafiskt användargränssnitt för att utforska och hantera program i ett Service Fabric-kluster.

Ta bort programmet från part klustret:

1. Bläddra till Service Fabric Explorer med hjälp av länken som tillhandahålls av registreringssidan part klustret. Till exempel http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. I Service Fabric Explorer navigerar du till den **fabric://Voting** noder i trädvyn till vänster.

3. Klicka på den **åtgärd** -knappen i den högra **Essentials** fönstret och välj **ta bort programmet**. Bekräfta borttagning av programinstans, vilket tar bort instansen av vårt program som körs i klustret.

![Ta bort program i Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Ta bort programtypen från ett kluster med Service Fabric Explorer
Program distribueras som programtyperna i ett Service Fabric-kluster, vilket gör att du kan ha flera instanser och versioner av det program som körs i klustret. När du har tagit bort instansen av vårt program som körs, kan vi också ta bort typ, för att slutföra rensningen av distributionen.

Mer information om programmodell i Service Fabric finns [modellen är ett program i Service Fabric](service-fabric-application-model.md).

1. Navigera till den **VotingType** noden i treeview.

2. Klicka på den **åtgärd** -knappen i den högra **Essentials** fönstret och välj **avetablera typen**. Bekräfta avetablering programtypen.

![Avetablera programtyp i Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

Därmed är självstudiekursen avslutad.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera ett program till ett kluster med hjälp av Visual Studio
> * Ta bort ett program från ett kluster med Service Fabric Explorer

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ställ in kontinuerlig integration med hjälp av Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)