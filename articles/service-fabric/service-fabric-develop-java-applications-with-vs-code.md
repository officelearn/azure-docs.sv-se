---
title: Utveckla Java-program med Visual Studio Code
description: Den här artikeln visar hur du skapar, distribuerar och felsöker Java Service Fabric-program med Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610054"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Utveckla Java Service Fabric-program med Visual Studio Code

[Tillägget Service Fabric Reliable Services för VS-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att bygga Java Service Fabric-program på Windows-, Linux- och macOS-operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöker ett Java Service Fabric-program med Visual Studio Code.

> [!IMPORTANT]
> Java-program för service fabric kan utvecklas på Windows-datorer, men kan endast distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har installerat VS-kod, tillägget Service Fabric Reliable Services för VS-kod och eventuella beroenden som krävs för utvecklingsmiljön. Mer information finns i [Komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
I den här artikeln används röstningsprogrammet i [snabbstartsextmplet GitHub-lagringsprogram](https://github.com/Azure-Samples/service-fabric-java-quickstart)för Service Fabric Java. 

Om du vill klona databasen till utvecklingsdatorn kör du följande kommando från ett terminalfönster (kommandofönster i Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS-kod

Öppna VS-kod.  Klicka på Explorer-ikonen i **aktivitetsfältet** och klicka på **Öppna mapp**eller klicka på **Arkiv -> Öppna mapp**. Navigera till katalogen *./service-fabric-java-quickstart/Voting* i mappen där du klonade databasen och klicka sedan på **OK**. Arbetsytan ska innehålla samma filer som visas på skärmbilden nedan.

![Java-röstningsprogram på arbetsytan](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Skapa programmet

1. Tryck på (Ctrl + Skift + p) för att öppna **kommandopaletten** i VS-kod.
2. Sök efter och välj kommandot **Service Fabric: Build Application.** Byggutgången skickas till den integrerade terminalen.

   ![Kommandot Skapa program i VS-kod](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan du distribuera det till det lokala klustret. 

> [!IMPORTANT]
> Det går inte att distribuera Java-program till det lokala klustret på Windows-datorer.

1. Välj **kommandot Service Fabric: Deploy Application (Localhost)** i **kommandopaletten**. Utdata från installationsprocessen skickas till den integrerade terminalen.

   ![Kommandot Distribuera program i VS-kod](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar startar du en `http://localhost:19080/Explorer`webbläsare och öppnar Service Fabric Explorer: . Du bör se att programmet körs. Detta kan ta lite tid, så ha tålamod. 

   ![Röstningsprogram i Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. När du har verifierat att programmet körs startar du `http://localhost:8080`en webbläsare och öppnar den här sidan: . Detta är webb front-end för programmet. Du kan lägga till objekt och klicka på dem för att rösta.

   ![Röstningsprogram i webbläsaren](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Om du vill ta bort programmet från klustret väljer du kommandot **Service Fabric: Remove Application** från **kommandopaletten**. Utdata från avinstallationsprocessen skickas till den integrerade terminalen. Du kan använda Service Fabric Explorer för att kontrollera att programmet har tagits bort från det lokala klustret.

## <a name="debug-the-application"></a>Felsök programmet
När du felsöker program i VS-kod måste programmet köras på ett lokalt kluster. Brytpunkter kan sedan läggas till i koden.

> [!IMPORTANT]
> Felsökning av Java-program stöds inte på Windows-datorer.

Så här förbereder du VotingDataService och röstningsprogrammet för felsökning:

1. Uppdatera filen *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh.*
Kommentera ut kommandot på rad 6 (använd '#') och lägg till följande kommando längst ned i filen:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Uppdatera filen *Voting/VotingApplication/ApplicationManifest.xml.* Ange attributen **MinReplicaSetSize** och **TargetReplicaSetSize** till "1" i **elementet StatefulService:**
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klicka på ikonen Felsökning i **aktivitetsfältet** för att öppna felsökningsvyn i VS-kod. Klicka på kugghjulsikonen högst upp i felsökningsvyn och välj **Java** på menyn för rullgardinsmenyn. Filen launch.json öppnas. 

   ![Felsökningsikon i VS-kodarbetsytan](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. I filen launch.json anger du portvärdet i konfigurationen **Debug (Attach)** till **8001**. Spara filen.

   ![Felsökningskonfiguration för launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Distribuera programmet till det lokala klustret med kommandot **Service Fabric: Deploy Application (Localhost).** Kontrollera att programmet körs i Service Fabric Explorer. Din ansökan är nu klar att avsökas.

Så här anger du en brytpunkt:

1. Öppna filen */Voting/VotingDataService/src/statefulservice/VotingDataService.java* i Explorer. Ange en brytpunkt på första raden `try` kod `addItem` i blocket i metoden (rad 80).
   
   ![Ange brytpunkt i tjänsten Röstningsdata](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Se till att du anger brytpunkter på körbara kodrader. Brytpunkter som anges på metoddeklarationer, `try` `catch` satser eller satser kommer till exempel att missas av felsökaren.
2. Om du vill börja felsöka klickar du på ikonen Felsökning i **aktivitetsfältet,** väljer **felsökningskonfigurationen (Bifoga)** på felsökningsmenyn och klickar på körknappen (grön pil).

   ![Felsökningskonfiguration (bifoga)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Via en webbläsare går du till `http://localhost:8080`. Skriv ett nytt objekt i textrutan och klicka på **+ Lägg till**. Din brytpunkt ska träffas. Du kan använda verktygsfältet Felsökning högst upp i VS-kod för att fortsätta körningen, stega över rader, gå in i metoder eller gå ut ur den aktuella metoden. 
   
   ![Träff brytpunkt](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Om du vill avsluta felsökningssessionen klickar du på plugin-ikonen i verktygsfältet Felsökning högst upp i VS-kod.
   
   ![Koppla från felsökning](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. När du har felsökt kan du använda kommandot **Service Fabric: Remove Application** för att ta bort röstningsprogrammet från det lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker C# Service Fabric-program med VS-kod](./service-fabric-develop-csharp-applications-with-vs-code.md).
