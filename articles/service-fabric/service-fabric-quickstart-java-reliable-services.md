---
title: 'Snabbstart: Skapa en Java-app på Azure Service Fabric'
description: I den här snabbstarten skapar du ett Java-program för Azure med ett Service Fabric-exempelprogram (tillförlitliga tjänster).
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ca6a1063f6ddd5c42d0d08f43b87a3387cc46a14
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859263"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Snabbstart:  Distribuera en Java-app till Azure Service Fabric på Linux

Den här snabb starten visar hur du distribuerar ditt första Java-program till Azure Service Fabric att använda Sol förmörkelse IDE på en dator med Linux-utvecklare. När du är klar har du ett röstningsprogram med en Java-webbklient som sparar röstningsresultat i en tillståndskänslig backend-tjänst i klustret.

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av mikrotjänster och containrar.

![Skärmbild av programmet](./media/service-fabric-quickstart-java/votingapp.png)

I den här snabbstarten lär du dig att:

* Använda Eclipse som ett verktyg för Service Fabric Java-program
* Distribuera programmet till ditt lokala kluster
* Skala ut programmet över flera noder

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

1. [Installera Service Fabric SDK och Service Fabric Command Line Interface (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installera Git](https://git-scm.com/)
3. [Installera Eclipse](https://www.eclipse.org/downloads/)
4. [Konfigurera en Java-miljö](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development) och kontrollera att du följer de valfria stegen för att installera plugin-programmet Eclipse

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Kör programmet lokalt

1. Starta ditt lokala kluster genom att köra följande kommando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Det kan ta lite tid att starta det lokala klustret. För att bekräfta att klustret är helt aktiverat öppnar du Service Fabric Explorer på **http://localhost:19080** . När du ser fem felfria noder vet du att det lokala klustret är igång och redo.

    ![Felfritt lokalt kluster](./media/service-fabric-quickstart-java/localclusterup.png)

2. Öppna Eclipse.
3. Välj **fil** > **import** **Gradle befintliga Gradle-projekt** och följ guiden. >  > 
4. Välj **katalog** och välj `Voting` katalogen från den `service-fabric-java-quickstart` mapp som du har klonat från GitHub. Välj **Slutför**.

    ![Dialogrutan för import till Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Nu finns projektet `Voting` i Package Explorer (Paketutforskaren) för Eclipse.
6. Högerklicka på projektet och välj **Publicera program** i list rutan **Service Fabric** . Välj **PublishProfiles/Local. JSON** som mål profil och välj **publicera**.

    ![Dialogrutan för lokal publicering](./media/service-fabric-quickstart-java/localjson.png)

7. Öppna din favorit webbläsare och få åtkomst till programmet genom att gå `http://localhost:8080`till.

    ![Programmets lokala klientdel](./media/service-fabric-quickstart-java/runninglocally.png)

Du kan nu lägga till en uppsättning röstningsalternativ och börja ta emot röster. Programmet körs och lagrar alla data i Service Fabric-klustret, utan att en separat databas krävs.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster

Tjänsterna kan enkelt skalas över ett kluster när belastningen på tjänsterna ändras. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret. Det går att skala tjänsterna på flera sätt. Du kan till exempel använda skript eller kommandon från Service Fabric CLI (sfctl). I följande steg använder du Service Fabric Explorer.

Service Fabric Explorer körs i alla Service Fabric-kluster och kan nås från en webbläsare genom att du bläddrar till klustrets HTTP-hanteringsport (19080), till exempel `http://localhost:19080`.

Så här skalar du webbklienttjänsten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `https://localhost:19080`.
2. Välj ellipsen ( **...** ) bredvid noden **Fabric:/röstning/VotingWeb** i trädvyn och välj **Scale service (skala tjänst**).

    ![Skalningstjänst i Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

3. Ändra antalet till **2** och välj **Scale service (skala tjänst)** .
4. Välj noden **infrastruktur:/röstning/VotingWeb** i trädvyn och expandera noden partition (representeras av ett GUID).

    ![Skalningstjänsten i Service Fabric Explorer har slutförts](./media/service-fabric-quickstart-java/servicescaled.png)

    Du kan nu se att tjänsten har två instanser och i trädvyn du se vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om ett fel uppstår för en tjänst ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Använda Eclipse som ett verktyg för Service Fabric Java-program
* Distribuera Java-program till ditt lokala kluster
* Skala ut programmet över flera noder

I självstudien för Java-appar finns mer information om hur du arbetar med Java-appar i Service Fabric.

> [!div class="nextstepaction"]
> [Distribuera en Java-app](./service-fabric-tutorial-create-java-app.md)
