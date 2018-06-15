---
title: Web App kloning med hjälp av PowerShell
description: Lär dig mer om att klona ditt webbprogram till nya webbprogram med hjälp av PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
ms.locfileid: "27867486"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure Apptjänst-App kloning med hjälp av PowerShell
Med versionen av Microsoft Azure PowerShell version 1.1.0, ett nytt alternativ har lagts till `New-AzureRMWebApp` som hjälper dig att klona en befintlig Webbapp till en nyligen skapade appen i en annan region eller i samma region. Det här alternativet gör det möjligt för kunder att distribuera flera appar över olika regioner, snabbt och enkelt.

Kloning av appen är för närvarande stöds endast för apptjänstplaner för premium-nivån. Ny funktion använder samma begränsningar som Web Apps Backup-funktionen, se [säkerhetskopiera en webbapp i Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Kloning av en befintlig App
Scenario: En befintlig webbapp i södra centrala USA region, och du vill klona innehållet till en ny webbapp i norra centrala USA region. Det kan åstadkommas med hjälp av Azure Resource Manager-version av PowerShell-cmdleten för att skapa ett nytt webbprogram med den `-SourceWebApp` alternativet.

Veta resursgruppens namn som innehåller webbappen källa, du kan använda följande PowerShell-kommando för att hämta information om käll-webbappen (i det här fallet med namnet `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Du kan använda för att skapa en ny App Service-Plan, `New-AzureRmAppServicePlan` kommandot som i följande exempel

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Med den `New-AzureRmWebApp` kommandot kan du skapa den nya webbappen i region norra centrala USA och koppla den till en befintlig premiumnivån App Service-Plan. Du kan dessutom använda samma resursgrupp som källa webbapp eller definiera en ny resursgrupp som visas i följande kommando:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Om du vill klona en befintlig webbapp, inklusive alla tillhörande distributionsplatser, måste du använda den `IncludeSourceWebAppSlots` parameter. Följande PowerShell-kommando visar hur du använder för att parametern med det `New-AzureRmWebApp` kommando:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Om du vill klona en befintlig webbapp i samma region som du behöver skapa en ny resursgrupp och en ny apptjänst planera i samma region och sedan använda följande PowerShell-kommando för att klona webbappen

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Kloning av en befintlig App som en Apptjänst-miljö
Scenario: En befintlig webbapp i södra centrala USA region, och du vill klona innehållet till en ny webbapp till en befintlig App Service miljö (ASE).

Veta resursgruppens namn som innehåller webbappen källa, du kan använda följande PowerShell-kommando för att hämta information om käll-webbappen (i det här fallet med namnet `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Känna till de ASE namnet och resursgruppens namn som ASE tillhör, kan du skapa den nya webbappen i befintliga ASE som visas i följande kommando:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Den `Location` parametern krävs av äldre skäl, men när du skapar appen i en ASE ignoreras. 

## <a name="cloning-an-existing-app-slot"></a>Kloning av en befintlig App-plats
Scenario: Du vill klona en befintlig Webbprogramplats till antingen ett nytt webbprogram eller en ny plats för webbprogram. Den nya Webbappen kan vara i samma region som den ursprungliga platsen Web App eller i en annan region.

Veta resursgruppens namn som innehåller webbappen källa, du kan använda följande PowerShell-kommando för att hämta information om källan web app platsens (i det här fallet med namnet `source-webappslot`) knutna till webbprogrammet `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Följande kommando visar hur du skapar en klon av webbprogram för källan till ett nytt webbprogram:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurera Traffic Manager under kloning av en app
Skapa flera regioner webbprogram och konfigurera Azure Traffic Manager för att dirigera trafik till dessa webbprogram, är ett viktigt scenario så att kunderna appar är hög tillgänglighet. När du klonar en befintlig webbapp har möjlighet att ansluta båda webbprogram till en ny traffic manager-profil eller en befintlig. Endast Azure Resource Manager version av Traffic Manager stöds.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Skapa en ny Traffic Manager-profil under kloning av en app
Scenario: Du vill klona en webbapp till en annan region när du konfigurerar en Azure Resource Manager traffic manager-profil som innehåller både webbprogram. Följande kommando visar hur du skapar en klon av webbprogram för källan till en ny webbapp när du konfigurerar en ny Traffic Manager-profil:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Att lägga till nya klonas webbprogram i en befintlig Traffic Manager-profil
Scenario: Du redan har en Azure Resource Manager traffic manager-profil och vill lägga till både webbprogram som slutpunkter. För att göra det, måste du först att montera den befintliga trafiken manager-profil-ID. Du behöver prenumerations-ID, resursgruppens namn och det befintliga profilnamnet för traffic manager.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Efter med traffic Manager-ID, visar följande kommando för att skapa en klon av webbprogram för källan till en ny webbapp när du lägger till dem i en befintlig Traffic Manager-profil:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuella begränsningar
Den här funktionen är för närvarande under förhandsgranskning och nya funktioner läggs över tid. Här följer kända begränsningar för den aktuella versionen av appen kloning:

* Inställningar för automatisk skalning klonas inte
* Schemat för säkerhetskopiering inställningar klonas inte
* VNET-inställningarna klonas inte
* App Insights konfigureras inte automatiskt på mål-webbprogram
* Enkelt autentiseringsinställningarna klonas inte
* Kudu-tillägget klonas inte
* Tips regler klonas inte
* Databasen innehåll klona inte
* Utgående IP-adresser ändras om Kloningen till en annan skalningsenhet

### <a name="references"></a>Referenser
* [Kloning av Web App](app-service-web-app-cloning.md)
* [Säkerhetskopiera en webbapp i Azure App Service](web-sites-backup.md)
* [Azure Resource Manager-stöd för Azure Traffic Manager-Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduktion till App Service-miljöer](environment/intro.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

