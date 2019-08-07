---
title: Klona app med PowerShell – Azure App Service
description: Lär dig hur du klonar din App Service-app till en ny app med hjälp av PowerShell.
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
ms.custom: seodec18
ms.openlocfilehash: 52d02fd79571e42f71c06b7090534136e4a5e341
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814681"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service kloning av appar med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Med versionen av Microsoft Azure PowerShell version 1.1.0 har ett nytt alternativ lagts till i så `New-AzWebApp` att du kan klona en befintlig App Service-app till en nyligen skapad app i en annan region eller i samma region. Med det här alternativet kan kunder distribuera ett antal appar i olika regioner snabbt och enkelt.

Kloning av appar stöds för standard-, Premium-, Premium v2-och isolerade App Service-planer. Den nya funktionen använder samma begränsningar som App Service säkerhets kopierings funktion, se [säkerhetskopiera en app i Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klona en befintlig app
Scenario: En befintlig app i regionen USA, södra centrala, och du vill klona innehållet till en ny app i norra centrala USA-regionen. Det kan åstadkommas med hjälp av den Azure Resource Manager versionen av PowerShell-cmdleten för att skapa en ny `-SourceWebApp` app med alternativet.

Om du känner till resurs grupps namnet som innehåller käll-appen kan du använda följande PowerShell-kommando för att hämta käll appens information (i det `source-webapp`här fallet med namnet):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Om du vill skapa en ny app service-plan kan `New-AzAppServicePlan` du använda kommandot som i följande exempel

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Med hjälp av kommandot kan du skapa den nya appen i regionen Norra centrala USA och koppla den till en befintlig App Service-plan. `New-AzWebApp` Du kan dessutom använda samma resurs grupp som käll-appen, eller definiera en ny resurs grupp, som du ser i följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Om du vill klona en befintlig app, inklusive alla tillhör ande distributions platser `IncludeSourceWebAppSlots` , måste du använda-parametern. Följande PowerShell-kommando visar hur parametern används med `New-AzWebApp` kommandot:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Om du vill klona en befintlig app inom samma region måste du skapa en ny resurs grupp och en ny app service-plan i samma region och sedan använda följande PowerShell-kommando för att klona appen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klona en befintlig app till en App Service-miljön
Scenario: En befintlig app i regionen USA, södra centrala, och du vill klona innehållet till en ny app till en befintlig App Service-miljön (ASE).

Om du känner till resurs grupps namnet som innehåller käll-appen kan du använda följande PowerShell-kommando för att hämta käll appens information (i det `source-webapp`här fallet med namnet):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Om du vet namnet på ASE och namnet på resurs gruppen som ASE tillhör kan du skapa den nya appen i den befintliga ASE, som du ser i följande kommando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametern krävs på grund av en gammal orsak, men den ignoreras när du skapar appen i en ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klona en befintlig app-plats
Scenario: Du vill klona en befintlig distributions plats för en app till antingen en ny app eller en ny plats. Den nya appen kan finnas i samma region som den ursprungliga app-platsen eller i en annan region.

Om du känner till resurs grupps namnet som innehåller käll-appen kan du använda följande PowerShell-kommando för att hämta käll program platsens information (i det `source-appslot`här fallet med `source-app`namnet) som är kopplad till:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Följande kommando visar hur du skapar en klon av käll programmet till en ny app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurera Traffic Manager vid kloning av en app
Att skapa appar för flera regioner och konfigurera Azure Traffic Manager för att dirigera trafik till alla dessa appar är ett viktigt scenario för att säkerställa att kundernas appar är hög tillgängliga. När du klonar en befintlig app har du möjlighet att ansluta båda apparna antingen till en ny Traffic Manager-profil eller en befintlig. Endast Azure Resource Manager version av Traffic Manager stöds.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Skapa en ny Traffic Manager-profil vid kloning av en app
Scenario: Du vill klona en app till en annan region, samtidigt som du konfigurerar en Azure Resource Manager Traffic Manager-profil som innehåller båda apparna. Följande kommando visar hur du skapar en klon av käll programmet till en ny app när du konfigurerar en ny Traffic Manager profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Lägga till en ny klonad app i en befintlig Traffic Manager-profil
Scenario: Du har redan en Azure Resource Manager Traffic Manager-profil och vill lägga till båda apparna som slut punkter. För att göra det måste du först sätta samman det befintliga ID: t för Traffic Manager-profilen. Du behöver prenumerations-ID, resurs gruppens namn och namnet på den befintliga Traffic Manager-profilen.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

När du har använt Traffic Manager-ID: t visar följande kommando hur du skapar en klon av käll programmet till en ny app när du lägger till dem i en befintlig Traffic Manager profil:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuella begränsningar
Här följer kända begränsningar för app-kloning:

* Inställningarna för automatisk skalning klonas inte
* Inställningarna för säkerhets kopierings schema har inte klonas
* VNET-inställningar klonas inte
* App Insights konfigureras inte automatiskt i mål programmet
* Inställningar för enkel autentisering klonas inte
* Kudu-tillägget har inte klonas
* Tips regler klonas inte
* Databas innehållet har inte klonas
* Utgående IP-adresser ändras om kloning till en annan skalnings enhet
* Inte tillgängligt för Linux-appar

### <a name="references"></a>Referenser
* [App Service kloning](app-service-web-app-cloning.md)
* [Säkerhetskopiera en app i Azure App Service](manage-backup.md)
* [Azure Resource Manager stöd för för hands versionen av Azure Traffic Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduktion till App Service-miljöer](environment/intro.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/manage-resources-powershell.md)

