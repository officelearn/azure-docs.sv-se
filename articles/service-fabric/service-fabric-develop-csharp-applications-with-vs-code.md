---
title: Utveckla .NET Core-program med Visual Studio-kod
description: Den här artikeln visar hur du skapar, distribuerar och felsöker .NET Core Service Fabric-program med Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614531"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Utveckla C# Service Fabric-program med Visual Studio-kod

[Tillägget Service Fabric Reliable Services för VS-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att skapa .NET Core Service Fabric-program på Windows-, Linux- och macOS-operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöker ett .NET Core Service Fabric-program med Visual Studio Code.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har installerat VS-kod, tillägget Service Fabric Reliable Services för VS-kod och eventuella beroenden som krävs för utvecklingsmiljön. Mer information finns i [Komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
I den här artikeln används CounterService-programmet i [Tjänst fabric .NET Core som kommer igång med GitHub-databasen](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Om du vill klona databasen till utvecklingsdatorn kör du följande kommando från ett terminalfönster (kommandofönster i Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS-kod

### <a name="windows"></a>Windows
Högerklicka på VS-kodikonen på Start-menyn och välj **Kör som administratör**. Om du vill koppla felsökningen till dina tjänster måste du köra VS-kod som administratör.

### <a name="linux"></a>Linux
Med terminalen navigerar du till sökvägen /service-fabric-dotnet-core-getting-started/Services/CounterService från katalogen som programmet klonades till lokalt.

Kör följande kommando för att öppna VS-kod som en rotanvändare så att felsökaren kan koppla till dina tjänster.
```
sudo code . --user-data-dir='.'
```

Programmet ska nu visas på arbetsytan VS-kod.

![Counter Service-program i arbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Skapa programmet
1. Tryck på (Ctrl + Skift + p) för att öppna **kommandopaletten** i VS-kod.
2. Sök efter och välj kommandot **Service Fabric: Build Application.** Byggutgången skickas till den integrerade terminalen.

   ![Kommandot Skapa program i VS-kod](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan du distribuera det till det lokala klustret. 

1. Välj **kommandot Service Fabric: Deploy Application (Localhost)** i **kommandopaletten**. Utdata från installationsprocessen skickas till den integrerade terminalen.

   ![Kommandot Distribuera program i VS-kod](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar startar du en webbläsare\/och öppnar Service Fabric Explorer: http: /localhost:19080/Explorer. Du bör se att programmet körs. Detta kan ta lite tid, så ha tålamod. 

   ![Counter Service-program i Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. När du har verifierat att programmet körs startar du en\/webbläsare och öppnar den här sidan: http: /localhost:31002. Detta är webb front-end för programmet. Uppdatera sidan för att se det aktuella värdet för räknaren när den ökar.

   ![Counter Service-program i webbläsaren](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publicera programmet i ett Azure Service Fabric-kluster
Tillsammans med distribution av programmet till det lokala klustret kan du också publicera programmet till ett fjärr-Azure Service Fabric-kluster. 

1. Se till att du har byggt ditt program med hjälp av instruktionerna ovan. Uppdatera den genererade `Cloud.json` konfigurationsfilen med information om det fjärrkluster som du vill publicera till.

2. Välj **kommandot Service Fabric: Publicera program**i **kommandopaletten**. Utdata från installationsprocessen skickas till den integrerade terminalen.

   ![Kommandot Publicera program i VS-kod](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. När distributionen är klar startar du en `https:<clusterurl>:19080/Explorer`webbläsare och öppnar Service Fabric Explorer: . Du bör se att programmet körs. Detta kan ta lite tid, så ha tålamod. 

## <a name="debug-the-application"></a>Felsök programmet
När du felsöker program i VS-kod måste programmet köras på ett lokalt kluster. Brytpunkter kan sedan läggas till i koden.

Så här anger du en brytpunkt och felsöker:
1. Öppna filen */src/CounterServiceApplication/CounterService/CounterService.cs* i Explorer och ange en brytpunkt på `RunAsync` linje 62 inuti metoden.
3. Klicka på ikonen Felsökning i **aktivitetsfältet** för att öppna felsökningsvyn i VS-kod. Klicka på kugghjulsikonen högst upp i felsökningsvyn och välj **.NET Core** på den rullgardinsmenyn. Filen launch.json öppnas. Du kan stänga den här filen. Nu bör du se konfigurationsalternativ i felsökningskonfigurationsmenyn bredvid körningsknappen (grön pil).

   ![Felsökningsikon i VS-kodarbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Välj **.NET Core Attach** på felsökningskonfigurationsmenyn.

   ![Felsökningsikon i VS-kodarbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Öppna Service Fabric Explorer i\/en webbläsare: http: /localhost:19080/Explorer. Klicka på **Program** och öka detaljnivån för att fastställa den primära noden som CounterService körs på. I bilden nedan är den primära noden för CounterService Nod 0.

   ![Primär nod för CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. I VS-kod klickar du på körikonen (grön pil) bredvid felsökningskonfigurationen **för .NET Core Attach.** I dialogrutan för processval väljer du den CounterService-process som körs på den primära noden som du identifierade i steg 4.

   ![Primär process](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Brytpunkten i *CounterService.cs* filen kommer att slås mycket snabbt. Du kan sedan utforska värdena för de lokala variablerna. Använd verktygsfältet Felsökning högst upp i VS-kod för att fortsätta körningen, stega över rader, gå in i metoder eller gå ut ur den aktuella metoden. 

   ![Felsöka värden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Om du vill avsluta felsökningssessionen klickar du på plugin-ikonen i verktygsfältet Felsökning högst upp i VS-koden.
   
   ![Koppla från felsökning](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. När du har felsökt kan du använda kommandot **Service Fabric: Remove Application** för att ta bort CounterService-programmet från det lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker Java Service Fabric-program med VS-kod](./service-fabric-develop-java-applications-with-vs-code.md).



