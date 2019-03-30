---
title: Utveckla .NET Core Azure Service Fabric-program med Visual Studio Code | Microsoft Docs
description: Den här artikeln visar hur du bygga, distribuera och felsöka .NET Core Service Fabric-program som använder Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 680c141e32333c4747ee69919229bd9381f536a4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664258"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Utveckla C# Service Fabric-program med Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att skapa .NET Core Service Fabric-program i operativsystemen Windows, Linux och macOS.

Den här artikeln visar hur du bygga, distribuera och felsöka en .NET Core Service Fabric-program som använder Visual Studio Code.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS Code, Service Fabric Reliable Services-tillägget för VS Code och eventuella beroenden som krävs för din utvecklingsmiljö. Mer information finns i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
Den här artikeln använder CounterService programmet i den [GitHub-lagringsplatsen för Service Fabric .NET Core komma igång-exempel](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Om du vill klona lagringsplatsen till din utvecklingsdator, kör du följande kommando från ett terminalfönster (kommandofönstret på Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS Code

### <a name="windows"></a>Windows
Högerklicka på VS Code-ikonen i Start-menyn och välj **kör som administratör**. Om du vill bifoga felsökningsprogrammet till tjänsterna, som du behöver köra VS Code som administratör.

### <a name="linux"></a>Linux
Använd terminalen och navigera till sökvägen /service-fabric-dotnet-core-getting-started/Services/CounterService från den katalog som programmet klonades i lokalt.

Kör följande kommando för att öppna VS Code som en rotanvändare felsökningsprogrammet går att koppla till dina tjänster.
```
sudo code . --user-data-dir='.'
```

Programmet bör nu visas i arbetsytan för VS Code.

![Räknaren tjänstprogram i arbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Skapa programmet
1. Tryck på (Ctrl + Skift + p) för att öppna den **Kommandopaletten** i VS Code.
2. Sök efter och välj den **Service Fabric: Skapa program** kommando. Resultatet skickas till den integrerade terminalen.

   ![Skapa programmet kommandot i VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan distribuera du den till det lokala klustret. 

1. Från den **Kommandopaletten**väljer den **Service Fabric: Distribuera programmet (Localhost) kommando**. Utdata från installationen skickas till den integrerade terminalen.

   ![Distribuera programmet kommandot i VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar, starta en webbläsare och öppna Service Fabric Explorer: http:\//localhost:19080 / Explorer. Du bör se att programmet körs. Det kan ta lite tid, så ha tålamod. 

   ![Räknaren tjänstprogram i Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. När du har kontrollerat programmet körs, starta en webbläsare och öppna den här sidan: http:\//localhost:31002. Det här är en webbklient för programmet. Uppdatera sidan om du vill se det aktuella värdet för räknaren som ökas.

   ![Räknaren tjänstprogram i webbläsare](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Felsök programmet
När du felsöker program i VS Code, måste du köra programmet i ett lokalt kluster. Brytpunkter kan sedan läggas till i koden.

Om du vill ange en brytpunkt och felsökning, gör du följande:
1. Öppna i Explorer den */src/CounterServiceApplication/CounterService/CounterService.cs* filen och ange en brytpunkt på rad 62 inuti den `RunAsync` metoden.
3. Klicka på ikonen Debug på den **Aktivitetsfältet** att öppna vyn felsökare i VS Code. Klicka på kugghjulsikonen överst i vyn felsökare och välj **.NET Core** från den nedrullningsbara menyn för miljön. Launch.json-filen öppnas. Du kan stänga den här filen. Du bör nu se konfigurationsalternativ i Felsök configuration-menyn bredvid knappen Kör (grön pil).

   ![Felsöka ikon i VS Code-arbetsyta](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Välj **.NET Core bifoga** från felsökningsmenyn för konfigurationen.

   ![Felsöka ikon i VS Code-arbetsyta](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Öppna Service Fabric Explorer i en webbläsare: http:\//localhost:19080 / Explorer. Klicka på **program** och öka detaljrikedomen för att fastställa den primära noden som CounterService körs på. I bilden nedan den primära noden för CounterService är noden 0.

   ![Primära noden för CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. I VS Code, klicka på ikonen kör (grön pil) bredvid den **.NET Core bifoga** debug-konfiguration. Välj CounterService processen som körs på den primära noden som du identifierade i steg 4 i dialogrutan för val av processen.

   ![Primär Process](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Brytpunkt i den *CounterService.cs* fil körs mycket snabbt. Sedan kan du utforska värdena för lokala variabler. Använda verktygsfältet Felsök överst i VS Code för att fortsätta köras, hoppa över rader, steg till metoder, eller Stega ut ur den aktuella metoden. 

   ![Felsöka värden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Om du vill avsluta felsökningssessionen, klickar du på ikonen Plugin i verktygsfältet Felsök överst i VS Code...
   
   ![Koppla från felsökare](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. När du är klar felsökning kan du använda den **Service Fabric: Ta bort programmet** kommando för att ta bort CounterService program från ditt lokala kluster. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker Java Service Fabric-program med VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



