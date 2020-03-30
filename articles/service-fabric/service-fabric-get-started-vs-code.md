---
title: Azure-tjänst fabric med VS-kod komma igång
description: Den här artikeln är en översikt över hur du skapar Service Fabric-program med Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258491"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service fabric för Visual Studio-kod

[Tillägget Service Fabric Reliable Services för VS-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) innehåller de verktyg som krävs för att skapa, skapa och felsöka Service Fabric-program på Windows-, Linux- och macOS-operativsystem.

Den här artikeln innehåller en översikt över kraven och inställningarna för tillägget samt användningen av de olika kommandon som tillhandahålls av tillägget. 

> [!IMPORTANT]
> Java-program för service fabric kan utvecklas på Windows-datorer, men kan endast distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Krav

Följande förutsättningar måste installeras på alla miljöer.

* [Visual Studio-kod](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Tyg SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman Generatorer - installera lämpliga generatorer för din ansökan

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Följande förutsättningar måste installeras för Java-utveckling:

* [Java SDK](https://aka.ms/azure-jdks) (version 1.8)
* [Gradle](https://gradle.org/install/)
* [Felsökare för Java VS-kodtillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Behövs för att felsöka Java-tjänster. Felsökning av Java-tjänster stöds endast på Linux. Du kan installera antingen genom att klicka på ikonen Tillägg i **aktivitetsfältet** i VS-kod och söka efter tillägget eller från VS-kod marketplace.

Följande förutsättningar måste installeras för utveckling av .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (version 2.0.0 eller senare)
* [C# för Visual Studio-kod (som drivs av OmniSharp) VS-kodtillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Behövs för att felsöka C# tjänster. Du kan installera antingen genom att klicka på ikonen Tillägg i **aktivitetsfältet** i VS-kod och söka efter tillägget eller från VS-kod marketplace.

## <a name="setup"></a>Installation

1. Öppna VS-kod.
2. Klicka på ikonen Tillägg i **aktivitetsfältet** till vänster i VS-koden. Sök efter "Service Fabric". Klicka på **Installera** för tillägget Service Fabric Reliable Services.

## <a name="commands"></a>Kommandon
Tillägget Service Fabric Reliable Services för VS-kod innehåller många kommandon som hjälper utvecklare att skapa och distribuera Service Fabric-projekt. Du kan anropa kommandon från **kommandopaletten** genom att trycka på `(Ctrl + Shift + p)`, skriva in kommandonamnet i indatafältet och välja önskat kommando i snabblistan. 

* Service Fabric: Skapa program 
* Service Fabric: Publicera program 
* Tjänst fabric: distribuera program 
* Service Fabric: Ta bort program  
* Service Fabric: Bygg ansökan 
* Service Tyg: Ren ansökan 

### <a name="service-fabric-create-application"></a>Service Fabric: Skapa program

Kommandot **Service Fabric: Skapa program** skapar ett nytt Service Fabric-program på den aktuella arbetsytan. Beroende på vilka yeoman-generatorer som är installerade på din utvecklingsmaskin kan du skapa flera typer av Service Fabric-program, inklusive Java-, C#, Container- och Gästprojekt. 

1.  Välj kommandot **Tjänstinfrastruktur: Skapa program**
2.  Välj typ för det nya Service Fabric-programmet. 
3.  Ange namnet på det program som du vill skapa
3.  Välj den typ av tjänst som du vill lägga till i programmet Service Fabric. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Det nya Programmet Service Fabric visas på arbetsytan.
6.  Öppna den nya programmappen så att den blir rotmappen på arbetsytan. Du kan fortsätta köra kommandon härifrån.

### <a name="service-fabric-add-service"></a>Service Fabric: Lägg till service
Kommandot **Tjänst fabric: Lägg till tjänst** lägger till en ny tjänst i ett befintligt Service Fabric-program. Programmet som tjänsten ska läggas till måste vara arbetsytans rotkatalog. 

1.  Välj kommandot **Tjänst fabric: Lägg till tjänst.**
2.  Välj typ av ditt aktuella Service Fabric-program. 
3.  Välj den typ av tjänst som du vill lägga till i programmet Service Fabric. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Den nya tjänsten visas i projektkatalogen. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicera program
**Kommandot Service Fabric: Publicera program** distribuerar ditt Service Fabric-program i ett fjärrkluster. Målklustret kan vara antingen ett säkert eller ett osäkert kluster. Om parametrar inte anges i Cloud.json distribueras programmet till det lokala klustret.

1.  Första gången programmet skapas genereras en Cloud.json-fil i projektkatalogen.
2.  Mata in värdena för det kluster som du vill ansluta till i filen Cloud.json.
3.  Välj kommandot **Tjänstinfrastruktur: Publicera program.**
4.  Visa målklustret med Service Fabric Explorer för att bekräfta att programmet har installerats. 

### <a name="service-fabric-deploy-application-localhost"></a>Tjänst fabric: Distribuera program (Localhost)
**Kommandot Service Fabric: Deploy Application** distribuerar ditt Service Fabric-program till ditt lokala kluster. Kontrollera att det lokala klustret körs innan du använder kommandot. 

1. Välj kommandot **Service Fabric: Deploy Application**
2. Visa det lokala klustret med\/Service Fabric Explorer (http: /localhost:19080/Explorer) för att bekräfta att programmet har installerats. Detta kan ta lite tid, så ha tålamod.
3. Du kan också använda **kommandot Service Fabric: Publicera program** utan parametrar i filen Cloud.json för att distribuera till ett lokalt kluster.

> [!NOTE]
> Det går inte att distribuera Java-program till det lokala klustret på Windows-datorer.

### <a name="service-fabric-remove-application"></a>Service Fabric: Ta bort program
**Kommandot Tjänst fabric: Ta bort program** tar bort ett Service Fabric-program från klustret som det tidigare har distribuerats till med vs-kodtillägget. 

1.  Välj kommandot **Tjänstinfrastruktur: Ta bort program.**
2.  Visa klustret med Service Fabric Explorer för att bekräfta att programmet har tagits bort. Detta kan ta lite tid, så ha tålamod.

### <a name="service-fabric-build-application"></a>Service Fabric: Bygg ansökan
**Kommandot Service Fabric: Build Application** kan skapa antingen Java- eller C#Service Fabric-program. 

1.  Kontrollera att du befinner dig i programmets rotmapp innan du kör det här kommandot. Kommandot identifierar typen av program (C# eller Java) och skapar ditt program därefter.
2.  Välj kommandot **Service Fabric: Build Application.**
3.  Utdata från byggprocessen skrivs till den integrerade terminalen.

### <a name="service-fabric-clean-application"></a>Service Tyg: Ren ansökan
**Kommandot Service Fabric: Clean Application** tar bort alla jar-filer och inbyggda bibliotek som genererades av build. Gäller endast för Java-program. 

1.  Kontrollera att du befinner dig i programmets rotmapp innan du kör det här kommandot. 
2.  Välj kommandot **Service Fabric: Clean Application.**
3.  Utgången av den rena processen skrivs till den integrerade terminalen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker C# Service Fabric-program med VS-kod](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Lär dig hur du [utvecklar och felsöker Java Service Fabric-program med VS-kod](./service-fabric-develop-java-applications-with-vs-code.md).
