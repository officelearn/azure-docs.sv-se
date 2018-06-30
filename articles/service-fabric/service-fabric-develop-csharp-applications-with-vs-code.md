---
title: Utveckla .NET Core Azure Service Fabric-program med Visual Studio Code | Microsoft Docs
description: Den här artikeln visar hur du skapar, distribuerar och felsöka .NET Core Service Fabric-program med hjälp av Visual Studio-koden.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 27c7c62125f3f559fb1764292729cbbfdc1c4e5f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116290"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Utveckla C# Service Fabric-program med Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att skapa .NET Core Service Fabric-program i Windows, Linux och macOS operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöka ett .NET Core Service Fabric-program med hjälp av Visual Studio-koden.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS-kod, Service Fabric Reliable Services-tillägget för VS-koden och eventuella beroenden som krävs för din utvecklingsmiljö. Läs mer i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Hämta exemplet
Den här artikeln använder CounterService programmet i den [exempel GitHub-lagringsplatsen för Service Fabric .NET Core komma igång](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Om du vill klona databasen på utvecklingsdatorn, kör du följande kommando från ett terminalfönster (kommandofönstret på Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS-kod

### <a name="windows"></a>Windows
Högerklicka på ikonen VS koden i Start-menyn och välj **kör som administratör**. Om du vill koppla felsökaren till dina tjänster som du behöver köra VS-kod som administratör.

### <a name="linux"></a>Linux
Använd terminalen och navigera till sökvägen /service-fabric-dotnet-core-getting-started/Services/CounterService från katalogen som programmet klonades i lokalt.

Kör följande kommando för att öppna VS-kod som rotanvändare så att felsökaren kan ansluta till dina tjänster.
```
sudo code . --user-data-dir='.'
```

Programmet ska nu visas i arbetsytan VS-kod.

![Räknaren tjänstprogrammet i arbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Skapa programmet
1. Tryck på (Ctrl + Skift + p) för att öppna den **kommandot paletten** i VS-kod.
2. Söka efter och välja den **Service Fabric: skapa programmet** kommando. Build-utdata skickas till integrerad terminalen.

   ![Skapa program kommandot i VS-kod](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan distribuera du den till det lokala klustret. 

1. Från den **kommandot paletten**, Välj den **Service Fabric: kommandot distribuera programmet (Localhost)**. Utdata från installationen skickas till integrerad terminalen.

   ![Distribuera programmet kommandot i VS-kod](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. När installationen är klar, starta en webbläsare och öppna Service Fabric Explorer: http://localhost:19080/Explorer. Du bör se att programmet körs. Det kan ta lite tid så att tålamod. 

   ![Räknaren tjänstprogrammet i Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. När du har kontrollerat att programmet körs kan starta en webbläsare och öppnar den här sidan: http://localhost:31002. Det här är webbklientdelen för programmet. Uppdatera sidan om du vill se det aktuella värdet för räknaren ökas.

   ![Räknaren tjänstprogram i webbläsare](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Felsöka programmet
När du felsöker program i VS-kod måste du köra programmet på lokala klustret. Kan sedan att lägga till brytpunkter i koden.

Om du vill ange en brytpunkt och felsökningsloggar, gör du följande:
1. Öppna i Utforskaren i */src/CounterServiceApplication/CounterService/CounterService.cs* fil och anger en brytpunkt på rad 62 inuti den `RunAsync` metoden.
3. Klicka på ikonen Debug i den **aktivitet fältet** att öppna vyn felsökare i VS-kod. Klicka på ikonen växeln överst i felsökaren vyn och välj **.NET Core** från listrutan för miljön. Launch.json filen öppnas. Du kan stänga den här filen. Du bör nu se konfigurationsalternativ på menyn debug configuration bredvid knappen Kör (grön pil).

   ![Felsöka ikonen VS kod arbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Välj **.NET Core bifoga** debug configuration-menyn.

   ![Felsöka ikonen VS kod arbetsytan](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Öppna Service Fabric Explorer i en webbläsare: http://localhost:19080/Explorer. Klicka på **program** och öka detaljnivån dowwn för att fastställa den primära noden som CounterService körs på. I bilden nedan den primära noden för CounterService är noden 0.

   ![Primära noden för CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. VS-kod, klicka på ikonen kör (grön pil) bredvid den **.NET Core bifoga** debug-konfiguration. Välj CounterService process som körs på den primära noden som du identifierade i steg 4 i dialogrutan för val av processen.

   ![Primär Process](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Brytpunkter i den *CounterService.cs* fil körs mycket snabbt. Du kan sedan utforska värdena för lokala variabler. Använd verktygsfältet Felsök överst i VS-kod för att fortsätta körningen, hoppa över rader, steget till metoder eller steg utanför den aktuella metoden. 

   ![Felsöka värden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Klicka på ikonen plug i verktygsfältet Felsök överst i VS-kod om du vill avsluta felsökningssessionen...
   
   ![Koppla från felsökare](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. När du är klar med felsökning kan du använda den **Service Fabric: ta bort programmet** kommando för att ta bort programmet CounterService från din lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utveckla och felsöka Java Service Fabric-program med VS kod](./service-fabric-develop-java-applications-with-vs-code.md).



