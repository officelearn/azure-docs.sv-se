---
title: Klona app med PowerShell
description: Läs om hur du klonar apptjänstappen till en ny app med PowerShell. En mängd olika kloningsscenarier omfattas, inklusive Traffic Manager-integrering.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255195"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Kloning av Azure App-tjänst-appar med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I och med lanseringen av Microsoft Azure PowerShell version 1.1.0 har ett nytt alternativ lagts till `New-AzWebApp` som gör att du kan klona en befintlig App Service-app till en nyskapd app i en annan region eller i samma region. Med det här alternativet kan kunder distribuera ett antal appar mellan olika regioner snabbt och enkelt.

Appkloning stöds för standard-, Premium-, Premium V2- och Enolated apptjänstplaner. Den nya funktionen använder samma begränsningar som apptjänstsäkerhetsfunktionen, se [Säkerhetskopiera en app i Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klona en befintlig app
Scenario: En befintlig app i region Södra centrala USA och du vill klona innehållet till en ny app i regionen Norra centrala USA. Det kan åstadkommas med hjälp av Azure Resource Manager-versionen av PowerShell-cmdlet för att skapa en ny app med `-SourceWebApp` alternativet.

När du känner till resursgruppsnamnet som innehåller källappen kan du använda följande PowerShell-kommando för att hämta källappens information (i det här fallet med namnet): `source-webapp`

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Om du vill skapa en ny `New-AzAppServicePlan` appserviceplan kan du använda kommandot som i följande exempel

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Med `New-AzWebApp` kommandot kan du skapa den nya appen i regionen Norra centrala USA och koppla den till en befintlig apptjänstplan. Dessutom kan du använda samma resursgrupp som källappen eller definiera en ny resursgrupp, som visas i följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Om du vill klona en befintlig app med `IncludeSourceWebAppSlots` alla associerade distributionsplatser måste du använda parametern.  Observera att `IncludeSourceWebAppSlots` parametern endast stöds för kloning av en hel app, inklusive alla dess platser. Följande PowerShell-kommando visar hur parametern `New-AzWebApp` används med kommandot:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Om du vill klona en befintlig app inom samma region måste du skapa en ny resursgrupp och ett nytt apptjänstabonnemang i samma region och sedan använda följande PowerShell-kommando för att klona appen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klona en befintlig app till en apptjänstmiljö
Scenario: En befintlig app i region Södra centrala USA och du vill klona innehållet till en ny app till en befintlig App Service Environment (ASE).

När du känner till resursgruppsnamnet som innehåller källappen kan du använda följande PowerShell-kommando för att hämta källappens information (i det här fallet med namnet): `source-webapp`

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Genom att känna till ASE:s namn och resursgruppsnamnet som ASE tillhör kan du skapa den nya appen i den befintliga ASE:n, som visas i följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametern `Location` krävs på grund av äldre orsak, men den ignoreras när du skapar appen i en ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klona en befintlig appplats
Scenario: Du vill klona en befintlig distributionsplats för en app till antingen en ny app eller en ny plats. Den nya appen kan finnas i samma region som den ursprungliga appplatsen eller i en annan region.

När du känner till resursgruppsnamnet som innehåller källappen kan du använda följande PowerShell-kommando `source-appslot`för `source-app`att få källappplatsens information (i det här fallet med namnet) knuten till:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Följande kommando visar hur du skapar en klon av källappen till en ny app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurera Traffic Manager medan du klonar en app
Att skapa appar med flera regioner och konfigurera Azure Traffic Manager för att dirigera trafik till alla dessa appar är ett viktigt scenario för att säkerställa att kundernas appar är mycket tillgängliga. När du klonar en befintlig app har du möjlighet att ansluta båda apparna till antingen en ny traffic manager-profil eller en befintlig. Endast Azure Resource Manager-versionen av Traffic Manager stöds.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Skapa en ny Traffic Manager-profil medan du klonar en app
Scenario: Du vill klona en app till en annan region, samtidigt som du konfigurerar en Azure Resource Manager-trafikhanterares profil som innehåller båda apparna. Följande kommando visar hur du skapar en klon av källappen till en ny app samtidigt som du konfigurerar en ny Traffic Manager-profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Lägga till ny klonad app i en befintlig Traffic Manager-profil
Scenario: Du har redan en Azure Resource Manager traffic manager-profil och vill lägga till båda apparna som slutpunkter. För att göra det måste du först montera det befintliga profil-ID:t för trafikhanteraren. Du behöver prenumerations-ID, resursgruppsnamnet och det befintliga profilnamnet traffic manager.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

När trafikstyrnings-ID har fått trafikstyrnings-ID visas att du skapar en klon av källappen i en ny app samtidigt som du lägger till dem i en befintlig Traffic Manager-profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuella begränsningar
Här är de kända begränsningarna för app kloning:

* Inställningar för automatisk skalning klonas inte
* Inställningar för säkerhetskopieringsschema klonas inte
* VNET-inställningar klonas inte
* Appstatistik ställs inte in automatiskt i målappen
* Enkel Auth-inställningar klonas inte
* Kudu Extension klonas inte
* TiP-regler klonas inte
* Databasinnehållet klonas inte
* Utgående IP-adresser ändras om kloning till en annan skalningsenhet
* Inte tillgängligt för Linux-appar

### <a name="references"></a>Referenser
* [Kloning av apptjänst](app-service-web-app-cloning.md)
* [Säkerhetskopiera en app i Azure App Service](manage-backup.md)
* [Azure Resource Manager-stöd för förhandsversionen av Azure Traffic Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduktion till App Service-miljöer](environment/intro.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

