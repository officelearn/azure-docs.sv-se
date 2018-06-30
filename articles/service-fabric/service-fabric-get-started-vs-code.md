---
title: Azure Service Fabric med VS-kod komma igång | Microsoft Docs
description: Den här artikeln är en översikt över hur du skapar Service Fabric-program med Visual Studio-koden.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 367829c269bd1d96e6aa5fab1be008483a4ab5ab
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116203"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric för Visual Studio Code

Den [Service Fabric Reliable Services-tillägget för VS kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) innehåller verktyg som behövs för att skapa, skapa och felsöka Service Fabric-program i Windows, Linux och macOS operativsystem.

Den här artikeln innehåller en översikt över kraven och installationen av tillägget samt användning av olika kommandon som tillhandahålls av tillägget. 

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste installeras på alla miljöer.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman generatorer--installera lämplig generatorer för ditt program

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Följande krav måste vara installerad för Java development:

* [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (version 1.8)
* [Gradle](https://gradle.org/install/)
* [Felsökaren för Java-kod för VS tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) behövs för att felsöka Java-tjänster. Felsökning av Java-tjänster stöds på Linux endast. Du kan installera antingen genom att klicka på ikonen tillägg i den **aktivitet fältet** i VS-kod och sökning för tillägget eller från Marketplace för VS-kod.

Följande krav måste vara installerad för .NET Core / C#-utveckling:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (version 2.0.0 eller senare)
* [C# för Visual Studio Code (drivs av OmniSharp) VS anknytning](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) behövs för att felsöka C#-tjänster. Du kan installera antingen genom att klicka på ikonen tillägg i den **aktivitet fältet** i VS-kod och sökning för tillägget eller från Marketplace för VS-kod.

## <a name="setup"></a>Konfiguration

1. Öppna VS-kod.
2. Klicka på ikonen tillägg i den **aktivitet fältet** på vänster sida av VS-kod. Sök efter ”Service Fabric”. Klicka på **installera** för Service Fabric Reliable Services-tillägget.

## <a name="commands"></a>Kommandon
Service Fabric Reliable Services-tillägget för VS-koden innehåller många kommandon för att hjälpa utvecklare att skapa och distribuera Service Fabric-projekt. Du kan anropa kommandon från den **kommandot paletten** genom att trycka på `(Ctrl + Shift + p)`, skriva kommandonamnet i fältet indata och välja önskad kommando från listan över fråga. 

* Service Fabric: Skapa program 
* Service Fabric: Publicera program 
* Service Fabric: Distribuera program 
* Service Fabric: Ta bort programmet  
* Service Fabric: Skapa program 
* Service Fabric: Ren program 

### <a name="service-fabric-create-application"></a>Service Fabric: Skapa program

Den **Service Fabric: skapa program** kommando skapar ett nytt Service Fabric-program i din aktuella arbetsytan. Beroende på vilka yeoman generatorer är installerade på utvecklingsdatorn kan skapa du flera typer av Service Fabric-program, Java, C#, behållare och Gäst-projekt. 

1.  Välj den **Service Fabric: Lägg till tjänst** kommando
2.  Välj typ för det nya Service Fabric-programmet. 
3.  Ange namnet på programmet som du vill skapa
3.  Välj typ av tjänst som du vill lägga till Service Fabric-programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Det nya Service Fabric-programmet visas i arbetsytan.
6.  Öppna programmappen så att det blir rotmappen i arbetsytan. Du kan fortsätta att köra kommandon från den här.

### <a name="service-fabric-add-service"></a>Service Fabric: Lägga till tjänst
Den **Service Fabric: Lägg till tjänst** kommando lägger till en ny tjänst till ett befintligt Service Fabric-program. Det program som tjänsten kommer att läggas till måste vara i rotkatalogen på arbetsytan. 

1.  Välj den **Service Fabric: Lägg till tjänst** kommando.
2.  Välj typ av ditt aktuella Service Fabric-program. 
3.  Välj typ av tjänst som du vill lägga till Service Fabric-programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Den nya tjänsten visas i projektkatalogen. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicera program
Den **Service Fabric: publicera programmet** kommandot distribuerar Service Fabric-programmet på ett kluster. Målklustret kan vara en säker eller ett oskyddade kluster. Om parametrar inte har angetts i Cloud.json har programmet distribuerats till det lokala klustret.

1.  Första gången som programmet skapas, skapas en Cloud.json-fil i projektkatalogen.
2.  Ange värden för det kluster som du vill ansluta till i filen Cloud.json.
3.  Välj den **Service Fabric: publicera programmet** kommando.
4.  Visa målkluster med Service Fabric Explorer för att bekräfta att programmet har installerats. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Distribuera programmet (Localhost)
Den **Service Fabric: distribuera programmet** kommandot distribuerar Service Fabric-program till det lokala klustret. Se till att dina lokala klustret körs innan du använder kommandot. 

1.  Välj den **Service Fabric: distribuera programmet** kommando
2.  Visa det lokala klustret med Service Fabric Explorer (http://localhost:19080/Explorer) att bekräfta att programmet har installerats. Det kan ta lite tid så att tålamod.
3.  Du kan också använda **Service Fabric: publicera programmet** kommandot utan parametrar som anges i filen Cloud.json att distribuera till det lokala klustret.

> [!NOTE]
> Distribuera Java-program till det lokala klustret stöds inte på Windows-datorer.

### <a name="service-fabric-remove-application"></a>Service Fabric: Ta bort programmet
Den **Service Fabric: ta bort programmet** kommandot tar bort ett Service Fabric-program från klustret att det redan har distribuerats till med VS-kod. 

1.  Välj den **Service Fabric: ta bort programmet** kommando.
2.  Visa klustret med Service Fabric Explorer för att bekräfta att programmet har tagits bort. Det kan ta lite tid så att tålamod.

### <a name="service-fabric-build-application"></a>Service Fabric: Skapa program
Den **Service Fabric: ta bort programmet** kommando kan skapa Java eller Service Fabric C#-program. 

1.  Kontrollera att du är i rotmappen för programmet innan du kör det här kommandot. Kommandot identifierar typ av programmet (C# eller Java) och därefter skapar ditt program.
2.  Välj den **Service Fabric: skapa programmet** kommando.
3.  Utdata från skapar skrivs till integrerad terminalen.

### <a name="service-fabric-clean-application"></a>Service Fabric: Ren program
Den **Service Fabric: ren programmet** kommando tar bort alla jar-filer och interna bibliotek som har genererats av versionen. Giltigt för Java-program. 

1.  Kontrollera att du är i rotmappen för programmet innan du kör det här kommandot. 
2.  Välj den **Service Fabric: ren programmet** kommando.
3.  Rensa processens utdata skrivs till integrerad terminal.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utveckla och felsöka C# Service Fabric-program med VS kod](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Lär dig hur du [utveckla och felsöka Java Service Fabric-program med VS kod](./service-fabric-develop-java-applications-with-vs-code.md).
