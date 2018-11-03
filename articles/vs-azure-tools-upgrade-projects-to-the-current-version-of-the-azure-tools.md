---
title: Uppgradera projekt till den aktuella versionen av Azure-verktyg | Microsoft Docs
description: Lär dig hur du uppgraderar ett Azure-projekt i Visual Studio till den aktuella versionen av Azure-verktyg
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1d64070a-078d-468a-87f4-e6715de6475f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: a8b62bd261fb2b39f3e16549d2b531e442dc7b38
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969323"
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Uppgradera projekt till den aktuella versionen av Azure Tools för Visual Studio
## <a name="overview"></a>Översikt
När du har installerat den aktuella versionen av Azure-verktyg (eller en tidigare version som är nyare än 1.6) alla projekt som har skapats med hjälp av en Azure-verktyg släpper innan 1.6 (November 2011) uppgraderas automatiskt så fort du öppnar dem. Om du har skapat projekt med hjälp av den 1.6 (November 2011)-versionen av verktygen och du fortfarande har den version som är installerad, kan du öppna projekt i den äldre versionen och bestämma senare om du vill uppgradera dem.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Hur ditt projekt ändras när du uppgraderar den
Om ett projekt uppgraderas automatiskt eller om du anger att du vill uppgradera den, projektet ändras för att fungera med aktuella versioner av vissa osignerade sammansättningar och vissa egenskaper är också ändras eftersom det här avsnittet beskrivs. Om ditt projekt kräver andra ändringar att vara kompatibel med den nya versionen av verktygen, måste du utföra ändringarna manuellt.

* Filen web.config för web-roller och filen app.config för arbetsroller uppdateras för att referera till den nya versionen av Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.
* Microsoft.WindowsAzure.StorageClient.dll och Microsoft.WindowsAzure.Diagnostics.dll Microsoft.WindowsAzure.ServiceRuntime.dll sammansättningarna uppgraderas till de nya versionerna.
* Publiceringsprofiler som har lagrats i Azure projektfilen (.ccproj) flyttas till en separat fil med tillägget-.azurePubXml i den **publicera** underkatalog.
* Vissa egenskaper på profilen som har uppdaterats för att stödja nya och ändrade funktioner. **AllowUpgrade** ersätts av **DeploymentReplacementMethod** eftersom du kan uppdatera en distribuerad molntjänst samtidigt eller inkrementellt.
* Egenskapen **UseIISExpressByDefault** läggs och inställd på false, så att den webbserver som används för felsökning inte automatiskt ändra från Internet Information Services (IIS) IIS Express. IIS Express är standard-webbserver för projekt som skapas med de nyare utgåvorna av verktygen.
* Om Azure Caching finns i en eller flera av dina Projektroller, ändras vissa egenskaper i tjänstkonfigurationen (.cscfg-filen) och tjänstdefinitionen (.csdef-fil) när ett projekt har uppgraderats. Om projektet använder Azure Caching NuGet-paketet, uppgraderas projektet till den senaste versionen av paketet. Du bör öppna web.config-filen och kontrollera att klientkonfigurationen har korrekt bibehålls under uppgraderingsprocessen. Om du har lagt till referenser till Azure Caching klientsammansättningar utan att använda NuGet-paketet, uppdateras inte dessa sammansättningar; Du måste manuellt uppdatera dessa referenser till de nya versionerna.

> [!IMPORTANT]
> För F #-projekt, måste du manuellt uppdatera referenser till sammansättningar i Azure så att de refererar till nyare versioner av dessa sammansättningar.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Uppgradera ett Azure-projekt till den aktuella versionen
1. Installera den aktuella versionen av Azure-verktygen i installationen av Visual Studio som du vill använda för uppgraderade projektet och öppna sedan projektet som du vill uppgradera. Om projektet har skapats med en Azure-verktyg versionen innan 1.6 (November 2011), projektet uppgraderas automatiskt till den aktuella versionen. Om projektet har skapats från November 2011 versionen och den versionen finns kvar, projektet öppnas i den versionen.
2. Öppna snabbmenyn för distributionsprojektets nod i Solution Explorer, Välj **egenskaper**, och välj sedan den **program** fliken i dialogrutan som visas.
   
    Den **program** fliken visar verktyg-version som är associerat med projektet. Om den aktuella versionen av Azure Tools visas har projektet redan uppgraderats. Om du har installerat en senare version av verktyg än vad på fliken visas en **uppgradera** visas knappen.
3. Välj den **uppgradera** knappen för att uppgradera ett projekt till den aktuella versionen av verktygen.
4. Skapa projektet och korrigera eventuella fel som uppstår vid API-ändringar. Information om hur du ändrar koden för den nya versionen finns i dokumentationen för specifika API: et.

