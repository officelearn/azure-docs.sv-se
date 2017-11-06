---
title: Hur du uppgraderar projekt till den aktuella versionen av Azure-verktyg | Microsoft Docs
description: "Lär dig hur du uppgraderar ett Azure-projekt i Visual Studio till den aktuella versionen av Azure-verktyg"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 9a35de7ca0e7161468181b21709e1bd9915d566f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Hur du uppgraderar projekt till den aktuella versionen av Azure-verktyg för Visual Studio
## <a name="overview"></a>Översikt
När du har installerat den aktuella versionen av Azure-verktyg (eller en tidigare version som är nyare än 1.6) alla projekt som har skapats med hjälp av en Azure-verktyg släpper innan 1.6 (November 2011) uppgraderas automatiskt så fort du öppnar dem. Om du skapar projekt med 1.6 (November 2011) versionen av dessa verktyg och du har installerat uppdateringen, kan du öppna projekten i den äldre versionen och senare om du vill uppgradera dem.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Hur projektet ändras när du uppgraderar den
Om ett projekt uppgraderas automatiskt eller att du vill uppgradera den projektet har ändrats för att fungera med aktuella versioner av vissa sammansättningar och vissa egenskaper är också ändras eftersom det här avsnittet beskrivs. Om ditt projekt kräver andra ändringar att vara kompatibel med den nya versionen av verktygen, måste du utföra ändringarna manuellt.

* Filen web.config för webbroller och filen app.config för arbetsroller uppdateras för att referera till den nya versionen av Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.
* Microsoft.WindowsAzure.StorageClient.dll och Microsoft.WindowsAzure.Diagnostics.dll Microsoft.WindowsAzure.ServiceRuntime.dll sammansättningarna uppgraderas till de nya versionerna.
* Publiceringsprofiler som lagras i Azure-projekt-filen (.ccproj) flyttas till en separat fil med tillägget-.azurePubXml i den **publicera** underkatalog.
* Vissa egenskaper i profilen som har uppdaterats för att stödja nya och ändrade funktioner. **AllowUpgrade** ersätts av **DeploymentReplacementMethod** eftersom du kan uppdatera en distribuerad molntjänst samtidigt eller inkrementellt.
* Egenskapen **UseIISExpressByDefault** läggs och inställd på false, så att den webbserver som används för felsökning automatiskt inte ändras från Internet Information Services (IIS) till IIS Express. IIS Express är standardwebbservern för projekt som skapas med nyare versioner av verktygen.
* Om Azure Caching finns i en eller flera roller i ditt projekt, ändras vissa egenskaper i tjänstkonfigurationen (.cscfg-filen) och tjänstdefinitionen (.csdef-fil) när ett projekt har uppgraderats. Om projektet använder Azure Caching NuGet-paketet, uppgraderas projektet till den senaste versionen av paketet. Du bör öppna filen web.config och kontrollera att klientkonfigurationen har korrekt bibehålls under uppgraderingen. Om du har lagt till referenser till sammansättningar Azure Caching klienten utan att använda NuGet-paketet, uppdateras inte dessa sammansättningar; Du måste manuellt uppdatera dessa referenser till de nya versionerna.

> [!IMPORTANT]
> För F #-projekt, måste du manuellt uppdatera referenser till sammansättningar i Azure så att de refererar till nyare versioner av dessa sammansättningar.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Hur du uppgraderar ett Azure-projekt till den aktuella versionen
1. Installera den aktuella versionen av Azure-verktyg i installationen av Visual Studio som du vill använda för det uppgraderade projektet och sedan öppna projektet som du vill uppgradera. Om projektet har skapats med en Azure-verktyg släpper innan 1.6 (November 2011) projektet uppgraderas automatiskt till den aktuella versionen. Om projektet har skapats med November 2011 släppa och att versionen fortfarande är installerad, projektet öppnas i den versionen.
2. Öppna snabbmenyn för distributionsprojektets nod i Solution Explorer, Välj **egenskaper**, och välj sedan den **programmet** fliken i dialogrutan som visas.
   
    Den **programmet** visar den version av verktyg som är associerad med projektet. Om den aktuella versionen av Azure-verktyg visas har projektet redan uppgraderats. Om du har installerat en nyare version av verktyg än vad på fliken visas en **uppgradera** visas knappen.
3. Välj den **uppgradera** för att uppgradera ett projekt till den aktuella versionen av verktygen.
4. Skapa projektet och åtgärda eventuella fel som uppstår vid API ändringar. Information om hur du ändrar koden för den nya versionen finns i dokumentationen för det specifika API: et.

