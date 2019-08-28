---
title: Azure Service Fabric med VS Code Komma igång | Microsoft Docs
description: Den här artikeln är en översikt över hur du skapar Service Fabric program med hjälp av Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 9662ebd26a263fa006c8fccf877fdc950e9014c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102965"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric för Visual Studio Code

[Service Fabric Reliable Services tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) innehåller de verktyg som behövs för att skapa, bygga och felsöka Service Fabric program på Windows-, Linux-och MacOS-operativsystem.

Den här artikeln innehåller en översikt över kraven och installationen av tillägget samt användningen av de olika kommandon som tillhandahålls av tillägget. 

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan bara distribueras till Azure Linux-kluster. Fel sökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara installerade på alla miljöer.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman-generatorer – installera lämpliga generatorer för ditt program

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Följande krav måste vara installerade för Java-utveckling:

* [Java SDK](https://aka.ms/azure-jdks) (version 1,8)
* [Gradle](https://gradle.org/install/)
* [Fel sökning för Java vs Code-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Krävs för att felsöka Java-tjänster. Fel sökning av Java-tjänster stöds endast på Linux. Du kan installera genom att klicka på ikonen tillägg i **aktivitets fältet** i vs Code och söka efter tillägget eller från vs Code Marketplace.

Följande krav måste vara installerade för .NET Core/C# utveckling:

* [.Net Core](https://www.microsoft.com/net/learn/get-started) (version 2.0.0 eller senare)
* [för Visual Studio Code (drivs av OmniSharp) vs Code Extension C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Krävs för att C# felsöka tjänster. Du kan installera genom att klicka på ikonen tillägg i **aktivitets fältet** i vs Code och söka efter tillägget eller från vs Code Marketplace.

## <a name="setup"></a>Konfiguration

1. Öppen VS Code.
2. Klicka på ikonen tillägg i **aktivitets fältet** på vänster sida om vs Code. Sök efter "Service Fabric". Klicka på **Installera** för Service Fabric Reliable Services-tillägget.

## <a name="commands"></a>Kommandon
Service Fabric Reliable Services tillägget för VS Code innehåller många kommandon som hjälper utvecklare att skapa och distribuera Service Fabric-projekt. Du kan anropa kommandon från **paletten kommando** genom att `(Ctrl + Shift + p)`trycka på, skriva kommando namnet i indataströmmen och välja önskat kommando från listan med meddelanden. 

* Service Fabric: Skapa program 
* Service Fabric: Publicera program 
* Service Fabric: Distribuera program 
* Service Fabric: Ta bort program  
* Service Fabric: Bygg program 
* Service Fabric: Rengör program 

### <a name="service-fabric-create-application"></a>Service Fabric: Skapa program

**Service Fabric: Skapa program** kommando skapar ett nytt Service Fabric-program i din aktuella arbets yta. Beroende på vilka Yeoman-generatorer som installeras på din utvecklings dator kan du skapa flera olika typer av Service Fabric program, inklusive Java- C#,, container-och gäst projekt. 

1.  **Välj Service Fabric: Lägg till** tjänst kommando
2.  Välj typ för ditt nya Service Fabric-program. 
3.  Ange namnet på det program som du vill skapa
3.  Välj den typ av tjänst som du vill lägga till i Service Fabric programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Det nya Service Fabric programmet visas i arbets ytan.
6.  Öppna den nya programmappen så att den blir rotmappen i arbets ytan. Du kan fortsätta att köra kommandon härifrån.

### <a name="service-fabric-add-service"></a>Service Fabric: Lägg till tjänst
**Service Fabric: Lägg till** tjänst kommando lägger till en ny tjänst i ett befintligt Service Fabric-program. Programmet som tjänsten ska läggas till i måste vara rot katalogen för arbets ytan. 

1.  **Välj Service Fabric: Lägg till** tjänst kommando.
2.  Välj typ av aktuellt Service Fabric program. 
3.  Välj den typ av tjänst som du vill lägga till i Service Fabric programmet. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Den nya tjänsten visas i projekt katalogen. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicera program
**Service Fabric: Kommandot Publicera** program distribuerar ditt Service Fabric-program på ett fjärran slutet kluster. Mål klustret kan vara antingen ett säkert eller oskyddat kluster. Om parametrarna inte anges i Cloud. JSON, distribueras programmet till det lokala klustret.

1.  Första gången som programmet har skapats genereras en Cloud. JSON-fil i projekt katalogen.
2.  Mata in värdena för det kluster som du vill ansluta till i filen Cloud. JSON.
3.  **Välj Service Fabric: Kommandot Publicera** program.
4.  Visa mål klustret med Service Fabric Explorer för att bekräfta att programmet har installerats. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Distribuera program (localhost)
**Service Fabric: Distribuera program** kommandot distribuerar ditt Service Fabric program till det lokala klustret. Kontrol lera att ditt lokala kluster körs innan du använder kommandot. 

1. **Välj Service Fabric: Distribuera program** kommandot
2. Visa det lokala klustret med Service Fabric Explorer (http:\//localhost: 19080/Explorer) för att bekräfta att programmet har installerats. Det kan ta lite tid, så därför måste du vara patient.
3. Du kan också använda **Service Fabric: Kommandot Publicera** program utan parametrar som angetts i Cloud. JSON-filen för distribution till ett lokalt kluster.

> [!NOTE]
> Distribution av Java-program till det lokala klustret stöds inte på Windows-datorer.

### <a name="service-fabric-remove-application"></a>Service Fabric: Ta bort program
**Service Fabric: Ta bort** program kommandot tar bort ett Service Fabric-program från det kluster som det tidigare har distribuerats till med hjälp av vs Code-tillägget. 

1.  **Välj Service Fabric: Ta bort** program kommando.
2.  Visa klustret med Service Fabric Explorer för att bekräfta att programmet har tagits bort. Det kan ta lite tid, så därför måste du vara patient.

### <a name="service-fabric-build-application"></a>Service Fabric: Bygg program
**Service Fabric: Med kommandot** build Application kan du antingen bygga C# Java-eller Service Fabric-program. 

1.  Se till att du är i programmets rotmapp innan du kör det här kommandot. Kommandot identifierar typen av program (C# eller Java) och bygger ditt program enligt detta.
2.  **Välj Service Fabric: Kommandot build** Application.
3.  Utdatan från bygg processen skrivs till den integrerade terminalen.

### <a name="service-fabric-clean-application"></a>Service Fabric: Rengör program
**Service Fabric: Kommandot Rensa** program tar bort alla jar-filer och interna bibliotek som genererades av bygget. Endast giltigt för Java-program. 

1.  Se till att du är i programmets rotmapp innan du kör det här kommandot. 
2.  **Välj Service Fabric: Kommandot Rensa** program.
3.  Resultatet av den rena processen skrivs till den integrerade terminalen.

## <a name="next-steps"></a>Nästa steg

* Lär dig att [utveckla och felsöka C# Service Fabric program med vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Lär dig att [utveckla och felsöka Java Service Fabric program med vs Code](./service-fabric-develop-java-applications-with-vs-code.md).
