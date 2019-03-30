---
title: Utveckla Java Azure Service Fabric-program med Visual Studio Code | Microsoft Docs
description: Den här artikeln visar hur du bygga, distribuera och felsöka Java Service Fabric-program som använder Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667760"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Utveckla Java Service Fabric-program med Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att bygga Java Service Fabric-program i operativsystemen Windows, Linux och macOS.

Den här artikeln visar hur du bygga, distribuera och felsöka en Java Service Fabric-program som använder Visual Studio Code.

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte på Windows.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS Code, Service Fabric Reliable Services-tillägget för VS Code och eventuella beroenden som krävs för din utvecklingsmiljö. Mer information finns i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
Den här artikeln använder programmet röstning i den [GitHub för Service Fabric Java-program Snabbstart exempellagringsplatsen](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Om du vill klona lagringsplatsen till din utvecklingsdator, kör du följande kommando från ett terminalfönster (kommandofönstret på Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS Code

Öppna VS Code.  Klicka på ikonen Explorer på den **Aktivitetsfältet** och klicka på **Öppna mapp**, eller klicka på **Arkiv -> Öppna mapp**. Navigera till den *./service-fabric-java-quickstart/Voting* katalog i den mapp där du klonade databasen sedan klickar du på **OK**. Arbetsytan ska innehålla samma filer som visas i skärmbilden nedan.

![Java-Röstningsappen i arbetsyta](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Skapa programmet

1. Tryck på (Ctrl + Skift + p) för att öppna den **Kommandopaletten** i VS Code.
2. Sök efter och välj den **Service Fabric: Skapa program** kommando. Resultatet skickas till den integrerade terminalen.

   ![Skapa programmet kommandot i VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan distribuera du den till det lokala klustret. 

> [!IMPORTANT]
> Distribuera Java-program till det lokala klustret stöds inte på Windows-datorer.

1. Från den **Kommandopaletten**väljer den **Service Fabric: Distribuera programmet (Localhost) kommando**. Utdata från installationen skickas till den integrerade terminalen.

   ![Distribuera programmet kommandot i VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar, starta en webbläsare och öppna Service Fabric Explorer: `http://localhost:19080/Explorer`. Du bör se att programmet körs. Det kan ta lite tid, så ha tålamod. 

   ![Röstningsprogrammet i Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. När du har kontrollerat att programmet körs, starta en webbläsare och öppna den här sidan: `http://localhost:8080`. Det här är en webbklient för programmet. Du kan lägga till objekt och klicka på dem för att rösta.

   ![Röstningsprogrammet i webbläsare](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Om du vill ta bort programmet från klustret, Välj den **Service Fabric: Ta bort programmet** från den **Kommandopaletten**. Utdata från avinstallationen skickas till den integrerade terminalen. Du kan använda Service Fabric Explorer för att verifiera att programmet har tagits bort från det lokala klustret.

## <a name="debug-the-application"></a>Felsök programmet
När du felsöker program i VS Code, måste du köra programmet i ett lokalt kluster. Brytpunkter kan sedan läggas till i koden.

> [!IMPORTANT]
> Felsökning av Java-program stöds inte på Windows-datorer.

För att förbereda VotingDataService och röstningsprogrammet för felsökning, gör du följande:

1. Uppdatera den *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* fil.
Kommentera ut kommandot på rad 6 (Använd '#') och Lägg till följande kommando i slutet av filen:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Uppdatera den *Voting/VotingApplication/ApplicationManifest.xml* fil. Ange den **MinReplicaSetSize** och **TargetReplicaSetSize** attribut till ”1” i den **StatefulService** element:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klicka på ikonen Debug på den **Aktivitetsfältet** att öppna vyn felsökare i VS Code. Klicka på kugghjulsikonen överst i vyn felsökare och välj **Java** från den nedrullningsbara menyn för miljön. Launch.json-filen öppnas. 

   ![Felsöka ikon i VS Code-arbetsyta](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. I launch.json-filen anger Portvärdet i konfigurationen med namnet **Felsök (Anslut)** till **8001**. Spara filen.

   ![Felsök konfigurationen för launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Distribuera programmet till det lokala klustret med hjälp av den **Service Fabric: Distribuera programmet (Localhost)** kommando. Kontrollera att programmet körs i Service Fabric Explorer. Programmet är nu redo så att den felsöks.

Om du vill konfigurera en brytpunkt, gör du följande:

1. Öppna i Explorer den */Voting/VotingDataService/src/statefulservice/VotingDataService.java* fil. Ange en brytpunkt på första rad med kod i den `try` blockera i den `addItem` metod (rad 80).
   
   ![Konfigurera brytpunkt i rösta datatjänst](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Se till att ange brytpunkter på körbara rader med kod. Till exempel brytpunkter på metoddeklarationer `try` instruktioner eller `catch` uttryck som kan missas av felsökningsprogrammet.
2. Starta felsökning genom att klicka på felsökningsikon i den **Aktivitetsfältet**väljer den **Debug (bifoga)** configuration debug-menyn och klicka på knappen Kör (grön pil).

   ![Felsök (Anslut) konfiguration](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. I en webbläsare går du till `http://localhost:8080`. Ange ett nytt objekt i textrutan och klicka på **+ Lägg till**. Vara bör kommer till din brytpunkt. Du kan använda verktygsfältet Felsök överst i VS Code för att fortsätta köras, hoppa över rader, steg till metoder, eller Stega ut ur den aktuella metoden. 
   
   ![Kommer till brytpunkten](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Avsluta felsökningssessionen, klicka på plugin-ikonen i verktygsfältet Felsök överst i VS Code.
   
   ![Koppla från felsökare](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. När du är klar felsökning kan du använda den **Service Fabric: Ta bort programmet** kommando för att ta bort programmet röstning från ditt lokala kluster. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker C# Service Fabric-program med VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
