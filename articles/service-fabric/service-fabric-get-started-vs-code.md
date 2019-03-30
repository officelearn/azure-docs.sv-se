---
title: Azure Service Fabric med VS Code komma igång | Microsoft Docs
description: Den här artikeln är en översikt över att skapa Service Fabric-program som använder Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: a0405e6c699192839f72b290d0466ab5062ac584
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670144"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric för Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) innehåller verktyg som behövs för att skapa, bygga och felsöka Service Fabric-program på Windows, Linux och macOS-operativsystem.

Den här artikeln innehåller en översikt över kraven och installationen av tillägget samt användning av olika kommandon som tillhandahålls av tillägget. 

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte på Windows.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste installeras på alla miljöer.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman-generatorer – installera lämplig generatorer för ditt program

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Följande krav måste vara installerad för Java-utveckling:

* [Java SDK](https://aka.ms/azure-jdks) (version 1.8)
* [Gradle](https://gradle.org/install/)
* [Felsökare för Java VS Code-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) behövs för att felsöka Java-tjänster. Felsökning av Java-tjänster stöds i Linux endast. Du kan installera antingen genom att klicka på ikonen tillägg i den **Aktivitetsfältet** i VS Code och sökning för tillägget eller från VS Code Marketplace.

Följande krav måste vara installerad för .NET Core /C# utveckling:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (version 2.0.0 eller senare)
* [C#för Visual Studio Code (drivs av OmniSharp) VS Code-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) behövs för att felsöka C# tjänster. Du kan installera antingen genom att klicka på ikonen tillägg i den **Aktivitetsfältet** i VS Code och sökning för tillägget eller från VS Code Marketplace.

## <a name="setup"></a>Konfiguration

1. Öppna VS Code.
2. Klicka på ikonen tillägg på den **Aktivitetsfältet** på vänster sida av VS Code. Sök efter ”Service Fabric”. Klicka på **installera** för Service Fabric Reliable Services-tillägget.

## <a name="commands"></a>Kommandon
Service Fabric Reliable Services-tillägget för VS Code innehåller många kommandon som hjälper utvecklare att skapa och distribuera Service Fabric-projekt. Du kan anropa kommandon från den **Kommandopaletten** genom att trycka på `(Ctrl + Shift + p)`, att skriva kommandot i det inkommande fältet och välja önskad kommandot från listan. 

* Service Fabric: Skapa program 
* Service Fabric: Publicera program 
* Service Fabric: Distribuera program 
* Service Fabric: Ta bort program  
* Service Fabric: Skapa program 
* Service Fabric: Rensa programmet 

### <a name="service-fabric-create-application"></a>Service Fabric: Skapa program

Den **Service Fabric: Skapa program** kommandot skapar ett nytt Service Fabric-program i din aktuella arbetsyta. Du kan skapa flera typer av Service Fabric-program, inklusive Java, beroende på vilken yeoman-generatorer är installerade på utvecklingsdatorn, C#, behållare, och Gäst-projekt. 

1.  Välj den **Service Fabric: Lägg till tjänst** kommando
2.  Välj typ för det nya Service Fabric-programmet. 
3.  Ange namnet på programmet som du vill skapa
3.  Välj typ av tjänst som du vill lägga till Service Fabric-programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Det nya Service Fabric-programmet visas i arbetsytan.
6.  Öppna programmappen så att det blir rotmappen på arbetsytan. Du kan fortsätta köra kommandon härifrån.

### <a name="service-fabric-add-service"></a>Service Fabric: Lägg till tjänst
Den **Service Fabric: Lägg till tjänst** kommando lägger till en ny tjänst till ett befintligt Service Fabric-program. Det program som tjänsten kommer att läggas till måste vara i rotkatalogen på arbetsytan. 

1.  Välj den **Service Fabric: Lägg till tjänst** kommando.
2.  Välj typ av aktuella Service Fabric-programmet. 
3.  Välj typ av tjänst som du vill lägga till Service Fabric-programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Den nya tjänsten visas i projektkatalogen. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicera program
Den **Service Fabric: Publicera program** kommandot distribuerar Service Fabric-programmet på ett fjärrkluster. Målklustret kan vara en säker eller ett oskyddat kluster. Om parametrar inte har angetts i Cloud.json distribueras programmet till det lokala klustret.

1.  Första gången som har skapat programmet skapas en Cloud.json-fil i projektkatalogen.
2.  Ange värden för det kluster som du vill ansluta till i filen Cloud.json.
3.  Välj den **Service Fabric: Publicera program** kommando.
4.  Visa målkluster med Service Fabric Explorer för att bekräfta att programmet har installerats. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Distribuera programmet (Localhost)
Den **Service Fabric: Distribuera program** kommandot distribuerar Service Fabric-programmet till ditt lokala kluster. Kontrollera att det lokala klustret körs innan du använder kommandot. 

1. Välj den **Service Fabric: Distribuera program** kommando
2. Visa det lokala klustret med Service Fabric Explorer (http:\//localhost:19080 / Explorer) att bekräfta att programmet har installerats. Det kan ta lite tid, så ha tålamod.
3. Du kan också använda **Service Fabric: Publicera program** kommandot utan parametrar som angetts i filen Cloud.json att distribuera till ett lokalt kluster.

> [!NOTE]
> Distribuera Java-program till det lokala klustret stöds inte på Windows-datorer.

### <a name="service-fabric-remove-application"></a>Service Fabric: Ta bort program
Den **Service Fabric: Ta bort programmet** kommandot tar bort ett Service Fabric-program från klustret att det redan har distribuerats till med hjälp av VS Code-tillägg. 

1.  Välj den **Service Fabric: Ta bort programmet** kommando.
2.  Visa klustret med Service Fabric Explorer för att bekräfta att programmet har tagits bort. Det kan ta lite tid, så ha tålamod.

### <a name="service-fabric-build-application"></a>Service Fabric: Skapa program
Den **Service Fabric: Ta bort programmet** kommando kan skapa antingen Java eller C# Service Fabric-program. 

1.  Kontrollera att du är i rotmappen för programmet innan du kör det här kommandot. Kommandot visar vilka typer av program (C# eller Java) och därefter skapar ditt program.
2.  Välj den **Service Fabric: Skapa program** kommando.
3.  Utdata från skapandeprocessen skrivs till den integrerade terminalen.

### <a name="service-fabric-clean-application"></a>Service Fabric: Rensa programmet
Den **Service Fabric: Clean Application** kommandot tar bort alla jar-filer och interna bibliotek som har genererats av versionen. Giltigt för Java-program. 

1.  Kontrollera att du är i rotmappen för programmet innan du kör det här kommandot. 
2.  Välj den **Service Fabric: Clean Application** kommando.
3.  Utdata från ren processen skrivs till den integrerade terminalen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker C# Service Fabric-program med VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Lär dig hur du [utvecklar och felsöker Java Service Fabric-program med VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
