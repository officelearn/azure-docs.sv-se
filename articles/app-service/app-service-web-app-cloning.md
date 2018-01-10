---
title: "Web App kloning med hjälp av PowerShell"
description: "Lär dig mer om att klona ditt webbprogram till nya webbprogram med hjälp av PowerShell."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
ms.openlocfilehash: 8f58464ac212b84623d2287205271301dbaa0ffb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure Apptjänst-App kloning med hjälp av PowerShell
Med versionen av Microsoft Azure PowerShell version 1.1.0 har ett nytt alternativ lagts till ny AzureRMWebApp som skulle ge användaren möjlighet att klona en befintlig Webbapp till en nyligen skapade appen i en annan region eller i samma region. Detta gör att kunder att distribuera flera appar över olika regioner, snabbt och enkelt.

Kloning av appen är för närvarande stöds endast för apptjänstplaner för premium-nivån. Ny funktion använder samma begränsningar som Web Apps Backup-funktionen, se [säkerhetskopiera en webbapp i Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Kloning av en befintlig App
Scenario: En befintlig webbapp i södra centrala USA region användaren vill klona innehållet till en ny webbapp i norra centrala USA region. Detta kan åstadkommas med hjälp av Azure Resource Manager-version av PowerShell-cmdleten för att skapa ett nytt webbprogram med alternativet - SourceWebApp.

Vi veta resursgruppens namn som innehåller webbappen källa, kan du använda följande PowerShell-kommando för att hämta information om käll-webbappen (i det här fallet med namnet källa webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Vi kan använda New AzureRmAppServicePlan kommandot som i följande exempel för att skapa en ny App Service-Plan

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Med kommandot New AzureRmWebApp kan vi skapa den nya webbappen i området norra centrala USA och koppla den till en befintlig premiumnivån App Service-Plan, vi kan dessutom använda samma resursgrupp som webbappen källa eller definiera en ny resursgrupp , följande visar att:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Att klona en befintlig webbapp, inklusive alla associerade distributionen fack användaren måste använda parametern IncludeSourceWebAppSlots, följande PowerShell-kommando visar hur du använder av denna parameter med kommandot Nytt AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

Om du vill klona en befintlig webbapp inom samma region användaren kommer att behöva skapa en ny resursgrupp och en ny apptjänst planera i samma region och sedan använda följande PowerShell-kommando för att klona webbappen

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Kloning av en befintlig App som en Apptjänst-miljö
Scenario: En befintlig webbapp i södra centrala USA region användaren vill klona innehållet till en ny webbapp till en befintlig App Service miljö (ASE).

Vi veta resursgruppens namn som innehåller webbappen källa, kan du använda följande PowerShell-kommando för att hämta information om käll-webbappen (i det här fallet med namnet källa webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Känna till de ASE namnet och resursgruppens namn som ASE tillhör, användaren kan använda kommandot New AzureRmWebApp för att skapa den nya webbappen i befintliga ASE, följande visar att:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Parametern plats krävs av äldre skäl, men när det gäller att skapa en app i en ASE kommer att ignoreras. 

## <a name="cloning-an-existing-app-slot"></a>Kloning av en befintlig App-plats
Scenario: Användaren vill klona en befintlig Webbprogramplats till antingen ett nytt webbprogram eller en ny plats för webbprogram. Den nya Webbappen kan vara i samma region som den ursprungliga platsen Web App eller i en annan region.

Veta resursgruppens namn som innehåller webbappen källa använda vi följande PowerShell-kommando för att få källa web app platsens information (i det här fallet med namnet källa webappslot) knutna till webbprogrammet käll-webapp:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Följande visar hur du skapar en klon av webbprogram för källan till ett nytt webbprogram:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Konfigurera Traffic Manager under kloning av en App
Skapa flera regioner webbprogram och konfigurera Azure Traffic Manager för att dirigera trafik till dessa webbprogram, är ett n viktiga scenario till att säkerställa att kunders appar är hög tillgänglighet, när du klonar en befintlig webbapp har du möjlighet att ansluta båda webbprogram till en ny traffic manager-profil eller en befintlig - medveten om att endast Azure Resource Manager-version för Traffic Manager stöds.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Skapa en ny Traffic Manager-profil under kloning av en App
Scenario: Användaren vill klona ett webbprogram till en annan region, när du konfigurerar en Azure Resource Manager traffic manager-profil som innehåller både webbprogram. Följande visar hur du skapar en klon av webbprogram för källan till en ny webbapp när du konfigurerar en ny Traffic Manager-profil:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Att lägga till nya klonas webbprogram i en befintlig Traffic Manager-profil
Scenario: Användaren redan har en Azure Resource Manager traffic manager-profil som han vill lägga till både webbprogram som slutpunkter. Om du vill göra det måste vi måste först att montera den befintliga traffic manager profil-id, behöver vi prenumerations-id, resursgruppens namn och det befintliga profilnamnet för traffic manager.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

När du har med traffic Manager-id, visar följande skapa en klon av webbprogram för källan till en ny webbapp när du lägger till dem i en befintlig Traffic Manager-profil:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Aktuella begränsningar
Den här funktionen är för närvarande under förhandsgranskning, vi arbetar för att lägga till nya funktioner över tiden, i följande lista finns kända begränsningar för den aktuella versionen av appen kloning:

* Inställningar för automatisk skalning klonas inte
* Schemat för säkerhetskopiering inställningar klonas inte
* VNET-inställningarna klonas inte
* App Insights konfigureras inte automatiskt på mål-webbprogram
* Enkelt autentiseringsinställningarna klonas inte
* Kudu-tillägget klonas inte
* Tips regler klonas inte
* Databasen innehåll klonas inte
* Utgående IP-adresser ändras om Kloningen till en annan skalningsenhet

### <a name="references"></a>Referenser
* [Kloning av Web App](app-service-web-app-cloning.md)
* [Säkerhetskopiera en webbapp i Azure App Service](web-sites-backup.md)
* [Azure Resource Manager-stöd för Azure Traffic Manager-Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introduktion till App Service-miljöer](environment/intro.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

