---
title: Utveckla Java Azure Service Fabric-program med Visual Studio Code | Microsoft Docs
description: Den här artikeln visar hur du skapar, distribuerar och felsöka Java Service Fabric-program med hjälp av Visual Studio-koden.
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 54c94c50f6292694e947d97a10fd6976c14e19df
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116179"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Utveckla Java Service Fabric-program med Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att skapa Java Service Fabric-program i Windows, Linux och macOS operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöka ett Service Fabric Java-program med hjälp av Visual Studio-koden.

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS-kod, Service Fabric Reliable Services-tillägget för VS-koden och eventuella beroenden som krävs för din utvecklingsmiljö. Läs mer i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
Den här artikeln använder röst-programmet i den [Service Fabric Java-program quickstart exempel GitHub-lagringsplatsen](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Om du vill klona databasen på utvecklingsdatorn, kör du följande kommando från ett terminalfönster (kommandofönstret på Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS-kod

Öppna VS-kod.  Klicka på ikonen Explorer i den **aktivitet fältet** och på **öppna mappen**, eller klicka på **Arkiv -> Öppna mappen**. Navigera till den *./service-fabric-java-quickstart/Voting* katalog i den mapp där du klona databasen sedan klickar du på **OK**. Arbetsytan ska innehålla samma filer som visas i skärmbilden nedan.

![Java-Röstningsappen i arbetsytan](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Skapa programmet

1. Tryck på (Ctrl + Skift + p) för att öppna den **kommandot paletten** i VS-kod.
2. Söka efter och välja den **Service Fabric: skapa programmet** kommando. Build-utdata skickas till integrerad terminalen.

   ![Skapa program kommandot i VS-kod](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan distribuera du den till det lokala klustret. 

> [!IMPORTANT]
> Distribuera Java-program till det lokala klustret stöds inte på Windows-datorer.

1. Från den **kommandot paletten**, Välj den **Service Fabric: kommandot distribuera programmet (Localhost)**. Utdata från installationen skickas till integrerad terminalen.

   ![Distribuera programmet kommandot i VS-kod](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. När installationen är klar, starta en webbläsare och öppna Service Fabric Explorer: http://localhost:19080/Explorer. Du bör se att programmet körs. Det kan ta lite tid så att tålamod. 

   ![Röstningsapp i Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. När du har kontrollerat att programmet körs kan starta en webbläsare och öppnar den här sidan: http://localhost:8080. Det här är webbklientdelen för programmet. Du kan lägga till objekt och klicka på dem för att rösta.

   ![Röstningsapp i webbläsare](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Om du vill ta bort programmet från klustret, väljer den **Service Fabric: ta bort programmet** från den **kommandot paletten**. Utdata från avinstallationen skickas till integrerad terminalen. Du kan använda Service Fabric Explorer för att kontrollera att programmet har tagits bort från det lokala klustret.

## <a name="debug-the-application"></a>Felsöka programmet
När du felsöker program i VS-kod måste du köra programmet på lokala klustret. Kan sedan att lägga till brytpunkter i koden.

> [!IMPORTANT]
> Felsökning av Java-program stöds inte på Windows-datorer.

Om du vill förbereda VotingDataService och röst-programmet för felsökning, gör du följande:

1. Uppdatering av *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* fil.
Kommentera ut kommandot på rad 6 (Använd '#') och Lägg till följande kommando till slutet av filen:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Uppdatering av *Voting/VotingApplication/ApplicationManifest.xml* fil. Ange den **MinReplicaSetSize** och **TargetReplicaSetSize** attribut till ”1” i den **StatefulService** element:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klicka på ikonen Debug i den **aktivitet fältet** att öppna vyn felsökare i VS-kod. Klicka på ikonen växeln överst i felsökaren vyn och välj **Java** från listrutan för miljön. Launch.json filen öppnas. 

   ![Felsöka ikonen VS kod arbetsytan](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. Ange Portvärdet i konfigurationen med namnet i filen launch.json **Debug (bifoga)** till **8001**. Spara filen.

   ![Felsöka konfigurationen för launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Distribuera programmet till det lokala klustret med hjälp av den **Service Fabric: distribuera programmet (Localhost)** kommando. Kontrollera att programmet körs i Service Fabric Explorer. Tillämpningsprogrammet är nu redo att felsökas.

Om du vill ange en brytpunkt gör du följande:

1. Öppna i Utforskaren i */Voting/VotingDataService/src/statefulservice/VotingDataService.java* fil. Ange en brytpunkt på första raden med kod i den `try` blockera i den `addItem` metod (rad 80).
   
   ![Ange brytpunkter i röstning datatjänst](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Kontrollera att du kan ange brytpunkter på körbara rader med kod. Till exempel brytpunkter på metoddeklarationer, `try` instruktioner eller `catch` instruktioner kommer missas av felsökningsprogrammet.
2. Påbörja felsökning genom att klicka på ikonen Debug i den **aktivitet fältet**, Välj den **felsökning (bifoga)** configuration debug-menyn och klicka på knappen Kör (grön pil).

   ![Felsöka (bifoga) konfiguration](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. I en webbläsare går du till http://localhost:8080. Ange ett nytt objekt i textrutan och klicka på **+ Lägg till**. Din brytpunkt bör vara påträffades. Du kan använda verktygsfältet Felsök överst i VS-kod för att fortsätta köras, hoppa över rader, steget till metoder eller steg utanför den aktuella metoden. 
   
   ![Träffar brytpunkt](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Klicka på ikonen Plugin i verktygsfältet Felsök överst i VS-kod för att avsluta felsökningssessionen.
   
   ![Koppla från felsökare](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. När du är klar med felsökning kan du använda den **Service Fabric: ta bort programmet** för att ta bort programmet Röstningsdatabasen från din lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utveckla och felsöka C# Service Fabric-program med VS kod](./service-fabric-develop-csharp-applications-with-vs-code.md).
