---
title: "Installera tillägget beständiga funktioner och exempel - Azure"
description: "Lär dig hur du installerar tillägget varaktiga funktioner för Azure Functions för portalen utveckling eller Visual Studio-utveckling."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installera tillägget beständiga funktioner och exempel (Azure-funktioner)

Den [varaktiga funktioner](durable-functions-overview.md) tillägget för Azure Functions finns i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Den här artikeln visar hur du installerar paketet och en uppsättning exempel för följande utvecklingsmiljöer:

* Visual Studio 2017 (rekommenderas) 
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio har för närvarande den bästa upplevelsen för att utveckla appar som använder beständiga funktioner.  Dina funktioner kan köras lokalt och kan även publiceras till Azure. Du kan starta med ett tomt projekt eller en uppsättning exempel funktioner.

### <a name="prerequisites"></a>Krav

* Installera den [senaste versionen av Visual Studio](https://www.visualstudio.com/downloads/) (version 15.3 eller högre). Inkludera Azure-verktyg i din installationsalternativ.

### <a name="start-with-sample-functions"></a>Börja med exempel funktioner

1. Hämta den [Exempelapp ZIP-filen för Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Du behöver inte lägga till NuGet-referens eftersom exempelprojektet har redan.
2. Installera och köra [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 eller senare. Alternativt kan du uppdatera den *local.appsettings.json* fil med verkliga Azure Storage-anslutningssträngar.
3. Öppna projektet i Visual Studio-2017. 
4. Börja med instruktioner om hur du kör exemplet [fungerar länkning – Hello sekvens exempel](durable-functions-sequence.md). Exemplet kan köra lokalt eller publiceras på Azure.

### <a name="start-with-an-empty-project"></a>Börja med ett tomt projekt
 
Följ samma anvisningar som börjar med exemplet, men gör du följande i stället för att hämta den *.zip* fil:

1. Skapa en Funktionsapp-projekt.
2. Lägg till följande NuGet-paketet referens till din *.csproj* fil:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure Portal

Om du vill kan använda du Azure portal för utveckling av beständiga funktioner.

### <a name="create-an-orchestrator-function"></a>Skapa en orchestrator-funktion

1. Skapa en ny funktionsapp på [functions.azure.com](https://functions.azure.com/signin).
2. Konfigurera funktionsapp till [använder version 2.0 runtime](functions-versions.md).
3. Skapa en ny funktion och välj den **varaktiga funktioner Orchestrator - C#** mall.
4. Under **inte installerade tilläggen**, klickar du på **installera** att ladda ned tillägget från NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Kopiera exempelkod till funktionen appen

1. Hämta den [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) fil.
2. Packa upp exempelfilen till `D:\home\site\wwwroot` i appen funktionen med Kudu- eller FTP.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör funktionen länkning exempel](durable-functions-sequence.md)
